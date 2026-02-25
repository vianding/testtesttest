# LEAN — Interview-Focused Technical Analysis

> Focused on backtesting correctness, index/portfolio construction, bias control, performance, and data governance.
> All claims cite exact file paths and class/method names.

---

## Table of Contents

- [Tracing One Backtest Run End-to-End](#tracing-one-backtest-run-end-to-end)
- [A. Bias Handling](#a-bias-handling)
- [B. Index & Portfolio Construction Support](#b-index--portfolio-construction-support)
- [C. Performance & Scalability](#c-performance--scalability)
- [D. Data Quality, Validation & Governance](#d-data-quality-validation--governance)
- [E. How I Would Talk About This in an Interview](#e-how-i-would-talk-about-this-in-an-interview)

---

## Tracing One Backtest Run End-to-End

Understanding this path is the prerequisite for reasoning about every bias below.

```
1. config.json  ("environment": "backtesting")
       ↓
2. Launcher → Engine(systemHandlers, algorithmHandlers, liveMode=false)
       ↓
3. Engine.Run(job)                                        [Engine/Engine.cs ~L87]
   │  CreateAlgorithmInstance()   → loads DLL / Python
   │  CreateBrokerage()           → BacktestingBrokerage
   │  SecurityService.Init()      → builds Security objects
   │  DataManager.Init()          → wires IDataFeed + UniverseSelection
   │  HistoryProvider.Init()      → SubscriptionDataReaderHistoryProvider
   │  SetupHandler.Setup()        → calls algorithm.Initialize()
       ↓
4. AlgorithmManager.Run()                [Engine/AlgorithmManager.cs ~L120]
   foreach TimeSlice in Synchronizer.StreamData():
   │
   ├─ SubscriptionSynchronizer.Sync()   [Engine/DataFeeds/SubscriptionSynchronizer.cs ~L88]
   │   frontierUtc = _timeProvider.GetUtcNow()          # L101
   │   foreach subscription:
   │     while current.EmitTimeUtc <= frontierUtc:      # L129  ← KEY GATE
   │       add data to packet
   │   ApplyUniverseSelection(universe, frontierUtc)    # L239
   │   yield TimeSlice(frontierUtc, data, changes)
   │
   ├─ algorithm.SetDateTime(time)        # advance algo clock
   ├─ security.Update(data)             # update Security.Cache price
   ├─ HandleDividends() / HandleSplits() → adjust cash/quantity, fire OnDividends/OnSplits
   ├─ transactions.ProcessSynchronousEvents() → evaluate pending orders vs current price
   ├─ realtime.SetTime()                → fire scheduled events
   ├─ consolidators.Update() / Scan()
   ├─ algorithm.OnData(slice)           ← ALGORITHM ENTRY POINT
   └─ results.Sample()

5. Post-run: StatisticsBuilder.RunOracle() → Sharpe, Sortino, Calmar, etc.
             [Common/Statistics/StatisticsBuilder.cs]
```

The critical insight: **`EmitTimeUtc` of a daily bar is the bar's `EndTime` (e.g., 16:00:00 on 2023-01-03).** When the frontier reaches 16:00:00, the algorithm receives the full 2023-01-03 OHLCV bar. Orders placed in `OnData` are evaluated in `transactions.ProcessSynchronousEvents()` on the SAME iteration — using prices from that SAME bar.

---

## A. Bias Handling

---

### A.1 Lookahead Bias (Time Alignment)

**What it is:** Using future information at decision time — the algorithm's effective knowledge horizon extends past its decision timestamp.

#### Where it can sneak in

**The fundamental timing gate** — [`Engine/DataFeeds/SubscriptionSynchronizer.cs` L129]:
```csharp
while (subscription.Current != null && subscription.Current.EmitTimeUtc <= frontierUtc)
```
A **daily** bar for 2023-01-03 has `EndTime = 2023-01-03 16:00:00`, so `EmitTimeUtc = 16:00:00`. When the frontier hits 16:00:00, `OnData` receives the full 2023-01-03 bar (Open, High, Low, **Close**, Volume). If you then place a `MarketOrder`, the fill model checks current prices *from that same bar.*

**Market order fill at current bar's close** — [`Common/Orders/Fills/FillModel.cs` L280-323]:
```csharp
// InternalMarketFill
fill.FillPrice = prices.Current;  // L302 — prices.Current = Security.Cache.GetData().Value = Close
```
And in the equity-specific override [`Common/Orders/Fills/EquityFillModel.cs` L144-148]:
```csharp
fill.FillPrice = GetBestEffortAskPrice(asset, order.Time, out fillMessage) + slip;
```
`GetBestEffortAskPrice` falls back to last trade price if no quote is available — which, for daily bars, is the bar's closing price. **Net effect: you compute a signal from the close price and immediately fill at that same close. This is the classic bar-close bias (1-bar lookahead).**

**Stale-price guard** (partial mitigation) — [`Common/Orders/Fills/FillModel.cs` L293-298]:
```csharp
if (pricesEndTimeUtc.Add(Parameters.StalePriceTimeSpan) < order.Time)
    fill.Message = "Filled at stale price...";  // warning only, still fills
```
This warns but does NOT block fills on stale/fill-forward data. The check for non-market orders (stop, limit) is stricter: `if (pricesEndTime <= order.Time) return fill;` (L349) — but this only applies after the first bar.

#### What the framework provides to prevent it

- **`MarketOnOpenOrder`** — [`Common/Orders/Fills/FillModel.cs` L724-777]: Correctly enforces that the fill cannot happen until the next open. Line L744: `if (currentBar == null || localOrderTime >= currentBar.EndTime) return fill;`. Line L747: same-day open check. Line L756: fills at `.Open` of the triggering bar on the NEXT session. This is the architecturally correct way to model next-open execution.
- **`MarketOnCloseOrder`** — [`Common/Orders/Fills/FillModel.cs` L785-829]: Waits until `asset.LocalTime >= nextMarketClose` (L801), then fills at `.Close` (L808). This models MOC correctly — the order placed at 3:50 PM fills at 4:00 PM close.
- **Scheduled events** (`Schedule.On()`): Allow signal computation at bar close and order submission deferred to next-bar open, cleanly separating signal and execution timestamps.

#### How to use it correctly

```csharp
// WRONG (bar-close lookahead):
void OnData(Slice slice) {
    if (SlowEma < FastEma) SetHoldings("SPY", 1.0m); // fills at today's close
}

// CORRECT (next-open execution):
void OnData(Slice slice) {
    if (SlowEma < FastEma) MarketOnOpenOrder("SPY", quantity); // fills at tomorrow's open
}

// ALSO CORRECT (scheduled, 30 min after open):
Schedule.On(DateRules.EveryDay("SPY"),
            TimeRules.AfterMarketOpen("SPY", 30),
            () => SetHoldings("SPY", targetWeight));
```

#### Gaps and footguns

- Default `FillModel.MarketFill` uses close prices. Unless you override with `EquityFillModel` (which at least uses ask/bid), you're assuming you trade at closing prices.
- `FillModel.InternalMarketFill` (L280) warns on stale price but still fills — silently leaking through fill-forward gaps.
- **History requests**: `algorithm.History<TradeBar>("SPY", 20, Resolution.Daily)` returns adjusted data using the factor file's current state, including adjustments for events that had NOT yet occurred at the historical date. This is backward adjustment (standard for backtesting) but means your historical feature set implicitly incorporates future corporate action knowledge. This is normally acceptable but should be documented.

---

### A.2 Survivorship Bias (Universe Membership Over Time)

**What it is:** Testing a strategy on a set of assets that existed and were successful, ignoring assets that were delisted, went bankrupt, or dropped from the index during the test period.

#### Where it can sneak in

**Manual symbol lists:** Any `AddEquity("TSLA")` or hardcoded list of today's index constituents will only include survivors. If you define a list of S&P 500 members as of today and backtest 10 years, you exclude everything that was in the index 10 years ago but is not today (delisted, acquired, restructured).

**CoarseFundamental filter without delisted data:** The Morningstar fundamental universe data includes delisted companies, but only if the data files are present. Users who filter by `HasFundamentalData` may inadvertently exclude pre-delisting periods.

#### What the framework provides to prevent it

**Map files** — [`Common/Data/Auxiliary/MapFile.cs`] and [`Common/Data/Auxiliary/LocalDiskMapFileProvider.cs`]:
- Each security has a map file with rows: `{date, ticker, primaryExchange}`
- `SubscriptionDataReader` resolves the correct ticker for each historical date by calling `mapFile.GetMappedSymbol(date)`, preventing any ticker from appearing before its listing date
- Delisted securities have a `DelistingType.Warning` (1 bar notice) then `DelistingType.Delisted` event — [`Common/Data/Market/Delisting.cs`]
- `AlgorithmManager` processes delistings: forces liquidation and removes from universe

**`Universe.CanRemoveMember`** — [`Common/Data/UniverseSelection/Universe.cs` L163-194]:
- Securities cannot be removed before `UniverseSettings.MinimumTimeInUniverse` (default: `TimeSpan.Zero` for custom; `TimeSpan.Zero` for fundamental)
- Prevents the engine from removing a freshly-added security before you have time to react

**`PendingRemovalsManager`** — [`Engine/DataFeeds/PendingRemovalsManager.cs`]:
- Delays actual subscription removal if open orders or positions exist in the security
- Prevents orphaned orders on removed securities

**Delisting handling in `SubscriptionSynchronizer`** — [L149-158]:
- Detects `DelistingType.Delisted` on universe subscriptions and disposes the universe
- Fires `HandleDelisting()` on `UniverseSelection` to clean up subscriptions

#### How to use it correctly

```csharp
// Survivorship-safe: use dynamic universe with historical membership
public override void Initialize()
{
    // Use AddUniverse with Fundamental data — LEAN provides historical membership
    AddUniverse(SelectCoarse, SelectFine);
    // OR use ETF constituents (historical)
    AddUniverse(Universe.ETF("SPY", Market.USA, UniverseSettings, ETFConstituentsFilter));
}

IEnumerable<Symbol> SelectCoarse(IEnumerable<CoarseFundamental> coarse)
{
    // At each selection date, 'coarse' contains only symbols that existed on that date
    return coarse.Where(c => c.HasFundamentalData && c.Volume > 1_000_000)
                 .OrderByDescending(c => c.DollarVolume)
                 .Take(500).Select(c => c.Symbol);
}
```

#### Gaps and footguns

- `ETFConstituentsUniverseSelectionModel` — [`Algorithm.Framework/Selection/ETFConstituentsUniverseSelectionModel.cs`]: uses ETF filing data which has its own publication lag; if the data provider's filing dates are approximate, you can have slight lookahead in constituent membership.
- Fundamental data from Morningstar has a known ~2-day publication lag but this is not modeled in the engine's fundamental universe selection timing. LEAN fires selection at market open with the previous day's fundamental file; the gap is not formally enforced in code, leaving room for data-availability assumptions.
- `Universe.Asynchronous = true` (L67-80) allows universe selection to run off the main data thread — this can cause race conditions with order state if not handled carefully.

---

### A.3 Data Leakage

**What it is:** Information that was not available at decision time being used to compute features or labels, inflating apparent predictive power.

#### Feature engineering leakage

**History requests return full-adjustment prices** — [`Algorithm/QCAlgorithm.History.cs`]:
- `History<TradeBar>(symbol, 252, Resolution.Daily)` returns data adjusted by the factor file as it exists NOW (at the time the code runs), not as it was at each historical date.
- This means a split in 2022 causes all 2021 prices in the history call to be retroactively halved — which is mathematically consistent (returns are preserved) but means your feature matrix uses a price scale that wasn't knowable in 2021.
- For return-based features (momentum, volatility), this is generally harmless. For price-level features (moving average crossovers with absolute thresholds, "price > $50"), it creates leakage.

**Warmup period data bleed** — [`Engine/Engine.cs` L193-225]:
- `SetWarmup(N, Resolution.Daily)` causes the engine to run N bars through the data feed before setting `IsWarmingUp = false`.
- During warmup, `OnData` still fires but is in warmup mode; indicators are populated. **The risk**: if you compute lookback features in `OnData` and store them in instance variables during warmup, those values carry forward into the live backtest — they were computed on pre-period data that "knows" what the algorithm will need.

#### Corporate action leakage

**Factor file lookup timing** — [`Engine/DataFeeds/Enumerators/PriceScaleFactorEnumerator.cs`]:
- Each bar is scaled by `FactorFile.GetPriceFactor(searchDate, normalizationMode)`
- `searchDate` is the bar's date. The factor file is a static file computed from ALL known corporate actions — past AND future relative to `searchDate`.
- Concretely: if a stock has a 2:1 split on 2023-01-15, and you're backtesting 2023-01-01, the factor file includes an entry for 2023-01-14 that changes the price scale. When the `PriceScaleFactorEnumerator` processes the 2023-01-01 bar, it queries the factor file at date 2023-01-01, and the factor at that date includes the backward propagation of the 2023-01-15 split. This is **correct behavior for adjusted prices** (it's what Bloomberg Adjusted does) but it means your price series implicitly knows about future splits.
- Factor values used for 2023-01-01 prices: `SplitFactor = 2.0` (because the split happened later in 2023). If you did NOT use adjusted prices (`DataNormalizationMode.Raw`), you would see a price discontinuity on 2023-01-15 — but then your raw price comparisons across that date are broken.

**`CorporateFactorRow.GetDividend()`** — [`Common/Data/Auxiliary/CorporateFactorRow.cs` L220]:
- Uses `GetNextTradingDay(Date)` to place the dividend on the correct event date. Factor is applied as `PriceFactor * (C - D) / C` — textbook backward adjustment, no leakage.

#### How to use it correctly

- Use `DataNormalizationMode.Adjusted` (default) for price-based features that are translation-invariant (returns, ratios). Be explicit about this choice.
- For cross-sectional price-level comparisons, use `DataNormalizationMode.Raw` and handle corporate action dates explicitly via `OnSplits()` and `OnDividends()`.
- Never use future-dated `History()` calls from within a scheduled event that is supposed to represent a past decision point.
- Explicitly document the assumption: "This strategy assumes backward-adjusted prices; factor file computation encodes future splits."

#### Gaps

- No built-in "point-in-time" price series where the factor is computed only using events known at each historical date. Building one requires custom `IDataProvider` or `IHistoryProvider` implementation.
- No warning when `History()` is called with a date range that overlaps with corporate actions that changed the price scale post-hoc.

---

### A.4 Corporate Actions / Price Adjustment Pitfalls

**Split warning system** — [`Engine/DataFeeds/Enumerators/SplitEventProvider.cs`] / [`Common/Data/Market/Split.cs`]:
- `SplitType.Warning` fires one bar before the actual split with `algorithm.OnSplits()`. This gives the algorithm one bar's notice to close/adjust options positions.
- `SplitType.SplitOccurred` fires on the split date; `AlgorithmManager.HandleSplits()` rescales `SecurityHolding.Quantity` and `AveragePrice`, and calls `IBrokerageModel.ApplySplit()` to rescale open orders.

**Dividend timing** — [`Engine/DataFeeds/Enumerators/DividendEventProvider.cs`]:
- Cash distribution credited on ex-dividend date; `CashBook` updated before `OnData` runs that bar.
- **Footgun**: If you are long shares and holding through dividend, the cash appears correctly but the stock price will drop by approximately the dividend amount (ex-dividend). If your fill model fills at pre-dividend close, your P&L will temporarily look inflated until the ex-div price drop appears in the next bar.

**Options on splits** — [`Engine/DataFeeds/Enumerators/LiveSplitEventProvider.cs`]:
- In live mode, option contracts may need to be adjusted for splits. The engine forwards split events to `algorithm.OnSplits()` with `SplitType.Warning` first.
- **Gap**: The engine does not automatically adjust existing option position quantities or strikes for splits. The algorithm must handle this in `OnSplits()`.

**Continuous futures mapping** — [`Common/Data/Auxiliary/MappingContractFactorProvider.cs`]:
- Futures roll handled by the Panama Canal or backward-ratio methods via `DataMappingMode` enum.
- A `SymbolChangedEvent` fires on the roll date — [`Engine/DataFeeds/Enumerators/MappingEventProvider.cs`].
- **Footgun**: If you use raw (unadjusted) continuous futures prices, the roll creates a price gap. Your carry calculation across the roll will be wrong. Use `DataNormalizationMode.BackwardsPanamaCanal` for price continuity.

---

### A.5 Rebalancing Timing Bias

**What it is:** Assuming execution occurs at a price that was not actually available when the order could have been submitted (close-to-close assumption, ignoring open-to-close cost, intraday drift, and execution delay).

#### Concrete cases

| Order type | Fill price | Appropriate when |
|---|---|---|
| `MarketOrder` in `OnData` (daily) | Bar's `Close` (or `Ask`) of the decision bar | Never — you can't submit and fill at the same close |
| `MarketOnOpenOrder` | Next session's `Open` | Signal computed at prior close; execute at next open |
| `MarketOnCloseOrder` | Same session's `Close` when time ≥ `nextMarketClose` | MOC auction participation; 20-min rule applies in real life |
| `LimitOrder` | `LimitPrice` if `tradeBar.Low < LimitPrice` (buy) | Aggressive limit close to mid; no partial fill modeled |

**`MarketOnOpenFill` implementation** — [`Common/Orders/Fills/FillModel.cs` L724-777]:
- Line L744: `if (currentBar == null || localOrderTime >= currentBar.EndTime) return fill;` — ensures the order was placed before the bar ended
- Line L747: If market was open when order was placed AND same day → waits for next session
- Line L756: `fill.FillPrice = GetPricesCheckingPythonWrapper(asset, order.Direction).Open`
- **Verdict**: Correct for the intended use case.

**`MarketOnCloseFill` implementation** — [`Common/Orders/Fills/FillModel.cs` L785-829]:
- Line L798: `nextMarketClose = GetNextMarketClose(localOrderTime, false)`
- Line L801: `if (asset.LocalTime < nextMarketClose) return fill;` — waits for market close
- Line L808: `fill.FillPrice = GetPricesCheckingPythonWrapper(asset, order.Direction).Close`
- **Issue**: For daily bars where `EndTime = 16:00:00`, if `localTime` is `16:00:00` and `nextMarketClose` is `16:00:00`, the condition passes and fills at today's close. This is correct *if* the order was placed before close; but if `OnData` fires at `16:00:00` and you immediately place an MOC, you're filling at the very close you just saw — 0 delay.
- **Mitigation**: In practice, place MOC orders earlier in the day (from a scheduled event at 15:30) to model the MOC window.

#### Recommended patterns for index rebalancing

```csharp
// Pattern 1: Close-to-open execution (clean separation)
// Signal at close → fill at next open
void OnData(Slice slice) {
    // Compute rebalance targets from today's close
    var targets = ComputeTargets(slice);
    foreach (var (symbol, weight) in targets)
        MarketOnOpenOrder(symbol, ComputeQuantity(symbol, weight));
}

// Pattern 2: Scheduled rebalancing with deliberate delay
void Initialize() {
    Schedule.On(
        DateRules.MonthStart("SPY"),
        TimeRules.AfterMarketOpen("SPY", 30),  // 30min after open
        Rebalance
    );
}

// Pattern 3: Limit orders for cost control (requires monitoring)
void Rebalance() {
    var targets = GetIndexTargets();
    var em = new EqualWeightingPortfolioConstructionModel();
    foreach (var t in em.CreateTargets(this, GenerateInsights()))
        LimitOrder(t.Symbol, ComputeQuantity(t), Security[t.Symbol].AskPrice * 1.001m);
}
```

#### Footguns

- `SetHoldings()` calls `MarketOrder` internally — daily-bar strategies calling `SetHoldings` in `OnData` get close-price fills.
- `EqualWeightingPortfolioConstructionModel.DetermineTargetPercent()` (L118-131) computes weights but does NOT emit orders directly — it only returns `PortfolioTarget` objects. The `IExecutionModel` then converts those to orders. Default execution model is `ImmediateExecutionModel` which uses `MarketOrder`. So a full Framework pipeline also has the close-price bias unless you override the execution model.

---

### A.6 Transaction Cost / Slippage Underestimation Bias

**Default cost models are zero:**
- Default brokerage model: `DefaultBrokerageModel.GetFeeModel()` returns `ConstantFeeModel(0)` — [`Common/Brokerages/DefaultBrokerageModel.cs`]
- Default slippage: `NullSlippageModel` (returns 0)
- Any backtest without explicitly setting `SetBrokerageModel(BrokerageName.InteractiveBrokersBrokerage)` or equivalent runs with zero costs.

**`VolumeShareSlippageModel`** — [`Common/Orders/Slippage/VolumeShareSlippageModel.cs`]:
```csharp
// Default constructor: volumeLimit=0.025, priceImpact=0.1
var volumeShare = Math.Min(order.AbsoluteQuantity / barVolume, _volumeLimit); // L84
slippagePercent = volumeShare * volumeShare * _priceImpact;                    // L86
return slippagePercent * lastData.Value;                                       // L89
```
At 2.5% participation (the cap): `0.025² × 0.1 = 0.00625%` of price. For a $100 stock this is 0.6 cents — extremely optimistic for illiquid stocks. This is a **quadratic model** (price impact ∝ (qty/volume)²) which grows quickly only for very large orders.

**Key issue**: `VolumeShareSlippageModel` uses `asset.GetLastData()` (L48) — the LAST BAR's volume, not the bar being traded. For strategies with significant position sizes relative to ADV, this understates impact because large orders executed over multiple bars are not modeled; each bar's fill is evaluated independently.

**`MarketImpactSlippageModel`** — [`Common/Orders/Slippage/MarketImpactSlippageModel.cs`]:
- More sophisticated: accounts for temporary and permanent impact components.
- Still uses bar-level volume, not intraday volume profile.

**Correct approach:**
```csharp
void Initialize() {
    SetBrokerageModel(BrokerageName.InteractiveBrokersBrokerage, AccountType.Margin);
    // IB model installs IB fee schedule + IB fill model + proper settlement

    // Add volume-share slippage explicitly
    Securities["SPY"].SlippageModel = new VolumeShareSlippageModel(
        volumeLimit: 0.10m,    // allow up to 10% of bar volume
        priceImpact: 0.1m      // 10bps per % participation squared
    );
}
```

---

### A.7 Overfitting Risks (Parameter Sweeps / Multiple Testing)

**What the optimizer provides** — [`Optimizer/LeanOptimizer.cs`] / [`Optimizer/Strategies/`]:

| Strategy | Class | Description |
|---|---|---|
| Grid search | `GridSearchOptimizationStrategy.cs` | Exhaustive enumeration of `OptimizationStepParameter` grid |
| Euler search | `EulerSearchOptimizationStrategy.cs` | Gradient-following local search |

Parameter types — [`Common/Optimizer/Parameters/`]:
- `OptimizationStepParameter`: `{name, min, max, step}` — defines a grid dimension
- `StaticOptimizationParameter`: Fixed value
- `ParameterSet`: One combination, passed to backtest as `algorithm.GetParameter("name")`

**What is NOT provided:**
- No multiple testing correction (no Bonferroni, Benjamini-Hochberg, or Holm)
- No walk-forward optimization (no out-of-sample reservation enforced by the engine)
- No combinatorial purging and embargo (CPCV, Lopez de Prado)
- No deflated Sharpe Ratio calculation to account for p-hacking
- No regularization / penalty for parameter complexity
- No cross-validation framework

**Overfitting entry points:**
1. Running grid search over (fastPeriod, slowPeriod, threshold) = 10×10×10 = 1000 backtests → selecting the best is massive p-hacking
2. Fitting universe selection criteria (momentum lookback, minimum market cap) on the full backtest period
3. Using `History()` to peek at data that "sets" indicator initial values based on full-period statistics

**Recommended practices (not provided by framework, must be implemented):**
```csharp
// Walk-forward: use separate in-sample/out-of-sample periods
// IS: 2010-2018 for optimization
// OOS: 2018-2023 for validation — run as a separate backtest

// Or implement a manual walk-forward:
if (algorithm.Time.Year < 2018)
    // In-sample: compute optimal params
else
    // Out-of-sample: use fixed params from IS period
```

---

## B. Index & Portfolio Construction Support

---

### B.1 Universe Definition & Membership Rules

**Best extension points:**
- `Universe` base class — [`Common/Data/UniverseSelection/Universe.cs`]
- `FundamentalUniverseSelectionModel` — [`Algorithm.Framework/Selection/FundamentalUniverseSelectionModel.cs`]
- `ETFConstituentsUniverseSelectionModel` — [`Algorithm.Framework/Selection/ETFConstituentsUniverseSelectionModel.cs`]

**Implementation pattern:**
```csharp
// Custom index: top 100 by market cap, price > $5, ADTV > $1M
public class MarketCapUniverseSelectionModel : FundamentalUniverseSelectionModel
{
    public override IEnumerable<Symbol> Select(QCAlgorithm algorithm, IEnumerable<Fundamental> fundamental)
    {
        return fundamental
            .Where(f => f.HasFundamentalData
                     && f.Price > 5m
                     && f.DollarVolume > 1_000_000m
                     && f.SecurityReference.ExchangeId == "NYS")   // NYSE only
            .OrderByDescending(f => f.MarketCap)
            .Take(100)
            .Select(f => f.Symbol);
    }
}
// RegisterSelection fires daily with fundamental data dated to yesterday (publication lag = T-1)
```

**Membership change callback:**
```csharp
public override void OnSecuritiesChanged(SecurityChanges changes)
{
    foreach (var added in changes.AddedSecurities)   { /* initialize indicator */ }
    foreach (var removed in changes.RemovedSecurities) { Liquidate(removed.Symbol); }
}
```

**Relevant modules:** `UniverseManager`, `Universe.PerformSelection()`, `UniverseSelection.ApplyUniverseSelection()`

---

### B.2 Weighting Rules

| Weighting scheme | Module/Class | Notes |
|---|---|---|
| Equal weight | `EqualWeightingPortfolioConstructionModel` | L118-131: `1/N` per active insight |
| Cap weight | Custom `IPortfolioConstructionModel` | Use `Fundamental.MarketCap` in `DetermineTargetPercent()` |
| Factor weight | `InsightWeightingPortfolioConstructionModel` | Uses `Insight.Weight` from alpha model |
| Confidence weight | `ConfidenceWeightedPortfolioConstructionModel` | Uses `Insight.Confidence` |
| Mean-variance | `MeanVarianceOptimizationPortfolioConstructionModel` | Covariance estimated from historical returns |
| Risk parity | `RiskParityPortfolioConstructionModel` | Inverse-vol weights |
| Black-Litterman | `BlackLittermanOptimizationPortfolioConstructionModel` | Combines views with market equilibrium |
| Vol targeting | Custom | Implement `IPortfolioConstructionModel`, use return series from `History()` |

**Cap-weight implementation pattern:**
```csharp
public class MarketCapWeightedPCM : PortfolioConstructionModel
{
    protected override Dictionary<Insight, double> DetermineTargetPercent(List<Insight> activeInsights)
    {
        var totalMktCap = activeInsights
            .Sum(i => Algorithm.ActiveSecurities[i.Symbol].Fundamentals.MarketCap);
        return activeInsights.ToDictionary(
            i => i,
            i => (double)(Algorithm.ActiveSecurities[i.Symbol].Fundamentals.MarketCap / totalMktCap)
        );
    }
}
```

**Volatility targeting pattern:**
```csharp
protected override Dictionary<Insight, double> DetermineTargetPercent(List<Insight> activeInsights)
{
    var targetVol = 0.10m; // 10% annual vol
    var result = new Dictionary<Insight, double>();
    foreach (var insight in activeInsights)
    {
        var hist = Algorithm.History<TradeBar>(insight.Symbol, 60, Resolution.Daily);
        var vol = hist.Select(b => (double)b.Close)
                      .ZipWithNext()
                      .Select((a,b) => Math.Log(b/a))
                      .StandardDeviation() * Math.Sqrt(252);
        result[insight] = (double)(targetVol / (decimal)vol) / activeInsights.Count;
    }
    return result;
}
```

---

### B.3 Liquidity Constraints / Eligibility Screens

**Built-in tools:**
- `Fundamental.DollarVolume` — 30-day ADTV from Morningstar, available in `SelectCoarse/SelectFine`
- `Fundamental.Price` — price filter
- `Fundamental.SecurityReference.IsPrimaryShare` — de-duplicate ADRs
- `ContractSecurityFilterUniverse` — [`Common/Securities/ContractSecurityFilterUniverse.cs`] for options/futures

**Pattern for liquidity gating at order time:**
```csharp
public override void OnData(Slice slice)
{
    foreach (var symbol in _targets.Keys)
    {
        // Gate by ADV: don't submit if order > 5% of 20-day ADV
        var adv = History<TradeBar>(symbol, 20, Resolution.Daily)
                      .Average(b => b.Volume * b.Close);
        var targetQty = ComputeQuantity(symbol);
        if (Math.Abs(targetQty) * Securities[symbol].Price < adv * 0.05m)
            MarketOrder(symbol, targetQty);
        // else: use VolumeWeightedAveragePriceExecutionModel to slice
    }
}
```

**Missing:** No built-in ADV-constrained order splitting in the execution layer. `VolumeWeightedAveragePriceExecutionModel` partially addresses this — it fires limit orders based on standard deviations of price — but it doesn't directly model multi-day execution relative to ADV.

---

### B.4 Rebalance Cadence and Drift

**Built-in cadence control:**
```csharp
// Framework: rebalance function drives portfolio construction
var pcm = new EqualWeightingPortfolioConstructionModel(
    rebalancingDateRules: DateRules.MonthStart("SPY"),
    portfolioBias: PortfolioBias.Long
);
SetPortfolioConstruction(pcm);
```

`PortfolioConstructionModel.ShouldRebalance()` (base class) returns true only when the rebalancing function fires — keeping the model from generating new targets on every bar.

**Drift-based rebalancing (manual):**
```csharp
void OnData(Slice slice)
{
    foreach (var (symbol, target) in _indexWeights)
    {
        var actualWeight = Portfolio[symbol].HoldingsValue / Portfolio.TotalPortfolioValue;
        if (Math.Abs(actualWeight - target) > _driftThreshold)
            SetHoldings(symbol, target); // MOO pattern recommended
    }
}
```

---

### B.5 Turnover Control and Constraints

**No built-in turnover penalty.** All portfolio construction models target a percent weight and generate orders to reach it exactly. You must implement constraints explicitly:

```csharp
public class TurnoverConstrainedPCM : PortfolioConstructionModel
{
    private readonly decimal _maxTurnoverPerRebalance;

    protected override IEnumerable<IPortfolioTarget> GetTargets(
        QCAlgorithm algorithm, IEnumerable<Insight> insights)
    {
        var idealTargets = base.GetTargets(algorithm, insights).ToList();
        var currentWeights = algorithm.Portfolio
            .ToDictionary(kvp => kvp.Key, kvp => kvp.Value.HoldingsValue / algorithm.Portfolio.TotalPortfolioValue);

        // Cap each trade at maxTurnoverPerRebalance of portfolio
        return idealTargets.Select(t =>
        {
            var current = currentWeights.GetValueOrDefault(t.Symbol, 0m);
            var delta = (decimal)t.Quantity - current;
            var capped = Math.Sign(delta) * Math.Min(Math.Abs(delta), _maxTurnoverPerRebalance);
            return PortfolioTarget.Percent(algorithm, t.Symbol, current + capped);
        });
    }
}
```

---

### B.6 Corporate Action Handling Specific to Indices

**Split handling for index weights:**
- On split date, `AlgorithmManager.HandleSplits()` rescales `SecurityHolding.Quantity` and `AveragePrice` automatically.
- Open orders are rescaled via `IBrokerageModel.ApplySplit()` — [`Common/Brokerages/IBrokerageModel.cs` L98].
- The `SplitType.Warning` event fires one bar early via `AlgorithmManager` (L329): `ProcessSplitSymbols()`. Use `OnSplits()` callback to close options before split.

**Dividend handling for index total-return calculation:**
- Cash credited automatically to `CashBook`; if you hold index constituents long, dividends appear as cash.
- For a total-return index, you need to reinvest dividends. No automatic reinvestment — implement in `OnDividends()`:
```csharp
public override void OnDividends(Dividends dividends)
{
    foreach (var kvp in dividends)
    {
        // Reinvest dividend cash into the same security
        var symbol = kvp.Key;
        var cashReceived = kvp.Value.Distribution * Portfolio[symbol].Quantity;
        var reinvestQty = Math.Floor(cashReceived / Securities[symbol].Price);
        if (reinvestQty > 0) MarketOrder(symbol, reinvestQty);
    }
}
```

---

### B.7 Benchmark Calculation and Attribution

**Benchmark API:**
```csharp
SetBenchmark("SPY");                          // price-return benchmark
SetBenchmark(SecurityType.Equity, "SPY");
```
Implemented via `SecurityBenchmark` — [`Common/Benchmarks/`]. The equity curve and benchmark are both sampled by `BacktestingResultHandler.Sample()` and emitted in the result packet.

**Attribution hooks (limited):**
- No built-in Brinson attribution (sector, selection, interaction effects)
- No factor exposure analysis
- `StatisticsBuilder.Generate()` computes `AlgorithmPerformance` and `BenchmarkPerformance` — `Alpha`, `Beta`, `Sharpe` relative to benchmark — [`Common/Statistics/StatisticsBuilder.cs`]
- `TradeBuilder` — [`Common/Statistics/TradeBuilder.cs`]: tracks round-trip trades with entry/exit timestamps, useful for post-processing attribution

**Custom attribution pattern:**
```csharp
// In OnEndOfAlgorithm():
var portfolioLog = new List<(DateTime, Symbol, decimal, decimal)>();
// Then process portfolioLog externally for Brinson attribution
// Or use ObjectStore to persist per-bar holdings:
ObjectStore.Save("holdings.json", JsonConvert.SerializeObject(_holdingSnapshots));
```

---

## C. Performance & Scalability

---

### C.1 Primary Performance Bottlenecks

| Bottleneck | Location | Notes |
|---|---|---|
| Zip decompression (disk I/O) | `ZipDataCacheProvider` | Opens zip per (symbol, date, resolution) tuple; caches for 10s |
| Per-bar C# object allocation | `SubscriptionDataReader`, `TimeSliceFactory` | New `TradeBar`/`Tick` per data point; GC pressure |
| Python GIL crossing | `AlgorithmPythonWrapper.cs` | Every `OnData`, `OnSecuritiesChanged`, indicator update crosses the CLR-Python boundary |
| Universe selection filter | `UniverseSelection.ApplyUniverseSelection()` | Runs user LINQ over entire universe on selection dates |
| Order evaluation loop | `BrokerageTransactionHandler.ProcessSynchronousEvents()` | Evaluates ALL open orders against current prices on each bar |
| Indicator computation | `Indicators/` | Not vectorized; each bar runs indicator chain sequentially |
| History requests in warmup | `SubscriptionDataReaderHistoryProvider` | Deserializes all historical data from disk synchronously |

**Zip cache** — [`Engine/DataFeeds/ZipDataCacheProvider.cs` L37-56]:
```csharp
// Default: evicts after 10 seconds (one zip = one day of minute data for one symbol)
_cacheSeconds = Config.GetDouble("zip-data-cache-provider", 10);
```
For daily-bar backtests, each day's data for 500 symbols = 500 zip lookups per bar. The 10s TTL means sequential access (chronological) gets good cache hits; random access does not.

**Python performance** — [`AlgorithmFactory/Python/Wrappers/AlgorithmPythonWrapper.cs`]:
- Every virtual method dispatch (OnData, Initialize, etc.) must acquire the Python GIL and marshal CLR objects to Python objects.
- Rough multiplier: Python algorithms run 5-15x slower than equivalent C# for CPU-bound work.
- Avoid calling C# indicators from Python in a tight loop; compute in bulk with `History()` + pandas/numpy.

---

### C.2 Scalability

| Dimension | Behavior | Limit |
|---|---|---|
| # Symbols (minute) | O(N) per bar — N subscriptions each emit data | `symbol-minute-limit: 10000` in config |
| # Bars | Linear in time × symbols | No hard limit; memory is the constraint |
| # Parallel strategies | Single-process; use `Optimizer` for parallel runs | Optimizer launches separate processes |
| # Parameter combinations | `GridSearchOptimizationStrategy` is embarrassingly parallel | CPU-core-limited via `Optimizer.Launcher` |
| Universe size | O(N) `SelectCoarse` filter on every selection day | Fundamental universe runs daily; 10K symbols = ~10K LINQ iterations |

---

### C.3 Caching, Parallelization, and Fast Backtest Patterns

**Built-in caching:**
- `ZipDataCacheProvider`: LRU-TTL for zip archives
- `SingleEntryDataCacheProvider`: Single-slot cache for live data
- `CachingOptionChainProvider` / `CachingFutureChainProvider`: Caches chain data by date
- `TextSubscriptionDataSourceReader.SetCacheSize()`: In-memory cache for text files (set to 40% of RAM in Engine.cs L95)

**Parallelism:**
- `parallelHistoryRequestsEnabled: true` (enabled in backtesting by default) — `SubscriptionDataReaderHistoryProvider` fetches multiple symbols' history in parallel threads
- Universe selection: `Universe.Asynchronous = true` — runs filter off main thread (risk: reentrancy)

**Fast backtest settings:**
```json
{
  "show-missing-data-logs": false,
  "zip-data-cache-provider": 60,        // cache zips for 60s instead of 10
  "maximum-data-points-per-chart-series": 100000,
  "algorithm-manager-time-loop-maximum": 5
}
```

**Fast backtest code patterns:**
```csharp
// 1. Use daily resolution (no intraday data)
AddEquity("SPY", Resolution.Daily);

// 2. Disable warmup if not needed (avoids loading extra data)
// Don't call SetWarmup() if indicators can initialize from OnSecuritiesChanged

// 3. Use ScheduledUniverse instead of CoarseFundamental for static lists
var symbols = new[] { "AAPL", "MSFT", "GOOG" }.Select(s => QuantConnect.Symbol.Create(s, SecurityType.Equity, Market.USA));
AddUniverseSelection(new ManualUniverseSelectionModel(symbols));

// 4. Avoid History() calls in OnData — pre-load in Initialize() or use indicators
// BAD: History<TradeBar>(symbol, 20, Resolution.Daily) in OnData every bar
// GOOD: Use SMA indicator, update it as data arrives
```

---

### C.4 Known Pitfalls

- **Per-symbol overhead**: Adding 1000 symbols at Tick resolution creates 1000 subscription enumerators; each enumerator opens its own file handle chain. Memory usage grows O(N).
- **Log verbosity**: Excessive `Log.Trace()` / `Log.Debug()` in custom code can become the bottleneck; set `"log-handler": "QuantConnect.Logging.NullLogHandler"` in CI runs.
- **`SetHoldings` recalculation**: Each `SetHoldings()` call computes buying power, max order quantity, and submits an order — these are not batch-optimized. For a 500-stock rebalance, call `SetHoldings` 500 times per rebalance. Prefer submitting all orders in a single loop.
- **Indicator on every bar**: Creating indicators inside `OnData` instead of `Initialize` causes O(N×bars) allocations.
- **Python `History()` returning DataFrames**: The conversion from C# IEnumerable to Python DataFrame happens via PythonNet reflection — avoid calling `History()` in a hot path.

---

## D. Data Quality, Validation & Governance

---

### D.1 Built-in Validation and Sanity Checks

**Factor file integrity** — [`Common/Data/Auxiliary/CorporateFactorRow.cs` L100-131]:
```csharp
if (line.Contains("inf") || line.Contains("e+")) continue; // L112 — skip precision-loss rows
if (row.PriceScaleFactor > 0) rows.Add(row);               // L120 — skip zero-factor rows
```
The comment on L109: "Exponential notation is treated as inf because of the loss of precision." Known symbols with overflow: `GBSN, JUNI, NEWL` — documented in `FactorFile.cs` L43-50.

**Stale price warning** — [`Common/Orders/Fills/FillModel.cs` L295-298]:
```csharp
if (pricesEndTimeUtc.Add(Parameters.StalePriceTimeSpan) < order.Time)
    fill.Message = "Filled at stale price...";
```
Logged but not blocked.

**Missing data logging** — config key `show-missing-data-logs: true` emits warnings when a source file cannot be found. Default is `false` (silent).

**Exchange hours validation** — `AlgorithmManager` and fill models check `IsExchangeOpen()` before filling, preventing fills during market-closed periods.

---

### D.2 Adding Custom Validation

**Custom data source validation** — Override `BaseData.Reader()`:
```csharp
public override BaseData Reader(SubscriptionDataConfig config, string line, DateTime date, bool isLive)
{
    var csv = line.Split(',');
    if (csv.Length < 5) return null;                        // schema check
    if (!decimal.TryParse(csv[4], out var volume)) return null; // type check
    if (volume < 0) return null;                            // outlier: negative volume

    var bar = new TradeBar { ... };
    if (bar.Close <= 0 || bar.High < bar.Low) return null; // internal consistency
    return bar;
}
```
Returning `null` from `Reader()` causes the engine to skip that data point (the bar is dropped entirely).

**Custom IDataProvider for validation wrapping:**
```csharp
public class ValidatingDataProvider : IDataProvider
{
    private readonly IDataProvider _inner;
    private readonly ILogger _auditLog;

    public Stream Fetch(string key)
    {
        var stream = _inner.Fetch(key);
        if (stream == null)
        {
            _auditLog.LogWarning($"Missing data: {key}");
            return null;
        }
        // Wrap stream with checksums, byte-count validation, etc.
        return new ValidatedStream(stream, key, _auditLog);
    }
}
// Register: "data-provider": "MyNamespace.ValidatingDataProvider" in config
```

**Stale data detection:**
```csharp
public override void OnData(Slice slice)
{
    foreach (var (symbol, bar) in slice.Bars)
    {
        // Check if bar is fill-forward (volume = 0 on fill-forward bars)
        if (bar.IsFillForward)
        {
            _staleDayCount[symbol] = _staleDayCount.GetValueOrDefault(symbol) + 1;
            if (_staleDayCount[symbol] > 5)
                Log($"WARN: {symbol} has {_staleDayCount[symbol]} consecutive stale bars");
        }
        else
        {
            _staleDayCount[symbol] = 0;
        }
    }
}
```
`TradeBar.IsFillForward` — [`Common/Data/Market/TradeBar.cs`] — set by `FillForwardEnumerator`.

**Outlier detection:**
```csharp
// Price spike filter: flag if price moves > N sigma
public class PriceSpikeFilter : IDataConsolidator { ... }
// Or in a custom indicator that tracks rolling Z-score
```

---

### D.3 Audit Trails and Logging

**Built-in:**
- `IResultHandler` emits all order events, fills, and portfolio snapshots to the result packet
- `StatisticsBuilder.Generate()` produces `TradeStatistics`, `PortfolioStatistics`, `AlgorithmPerformance`
- `TransactionHandler` logs every order submission, update, cancellation

**Custom audit trail via ObjectStore:**
```csharp
private readonly List<string> _auditLog = new();

public override void OnOrderEvent(OrderEvent orderEvent)
{
    _auditLog.Add($"{Time},{orderEvent.Symbol},{orderEvent.Status},{orderEvent.FillPrice},{orderEvent.FillQuantity}");
}

public override void OnEndOfAlgorithm()
{
    ObjectStore.SaveBytes("audit_trail.csv", Encoding.UTF8.GetBytes(string.Join("\n", _auditLog)));
}
```
`IObjectStore` — [`Engine/Storage/LocalObjectStore.cs`] — persists to `./storage/` directory. Survives algorithm restarts in live mode.

---

### D.4 Documentation Conventions and Config Governance

**Config layering** — [`Launcher/config.json`]:
- Base keys → environment overrides. No further hierarchy exists in core.
- No environment variable substitution built in (unlike Spring/twelve-factor apps). Must patch `Configuration.cs` or override at the `IDataProvider` level.
- **Gap**: No schema validation of `config.json`. A typo in `"data-folder"` silently produces 0 data, not an exception.

**Recommended governance patterns:**
```json
// config.local.json (git-ignored, per-developer overrides)
{
  "data-folder": "C:/QuantConnect/Data/",
  "api-access-token": "${QC_API_TOKEN}"  // not supported natively; requires wrapper
}
```

**Algorithm parameter governance:**
```csharp
// Instead of magic numbers, use GetParameter() with defaults and validation
public override void Initialize()
{
    var fastPeriod = GetParameter("ema-fast", 10);
    var slowPeriod = GetParameter("ema-slow", 20);

    if (fastPeriod >= slowPeriod)
        throw new ArgumentException($"ema-fast ({fastPeriod}) must be < ema-slow ({slowPeriod})");

    // Log all parameters for reproducibility audit
    Log($"Config: ema-fast={fastPeriod}, ema-slow={slowPeriod}, start={StartDate}, end={EndDate}");
}
```

**Reproducibility package (recommended manual practice):**
- Commit `config.json` alongside algorithm code
- Record `git rev-parse HEAD` in `OnEndOfAlgorithm()` via `ObjectStore`
- Save factor file checksums for each traded symbol
- Pin `algorithm-location` to a specific build artifact hash

---

## E. How I Would Talk About This in an Interview

---

### E.1 Sharp Talking Points

1. **Time alignment is the engine's deepest design choice.** LEAN's synchronizer gates data at `EmitTimeUtc ≤ frontier`. For daily bars, `EmitTimeUtc = bar.EndTime = 16:00:00`. Algorithm executes at 16:00:00 with that bar's close. Default `MarketFill` fills at that same close — so every signal-to-fill is zero-lag. Real backtests must use `MarketOnOpenOrder` or scheduled events after open.

2. **Survivorship bias prevention is data-driven, not engine-driven.** LEAN's map files and delisting events ensure symbols appear and disappear at the correct historical dates. But survivorship bias still enters through the front door if you hardcode today's index constituents or filter by `HasFundamentalData` without accounting for companies that lost fundamental coverage before going bankrupt.

3. **Price adjustment is backward-looking by design.** Factor files are pre-computed with all known splits/dividends (past and future relative to any historical date). The `PriceScaleFactorEnumerator` applies these retroactively. This is standard practice but means your price series implicitly encodes future corporate action knowledge. For a rigorous point-in-time simulation, you'd need a custom `IHistoryProvider` that reconstructs factors as they would have been known at each date.

4. **The optimizer has no multiple testing awareness.** Grid search over 100 parameter combinations and selecting the best Sharpe is textbook p-hacking. The framework provides no Bonferroni correction, no deflated Sharpe Ratio, no walk-forward partitioning. These must be implemented at the study design level.

5. **Transaction cost defaults are near-zero.** `DefaultBrokerageModel` returns `ConstantFeeModel(0)` and `NullSlippageModel`. Any backtest that doesn't explicitly call `SetBrokerageModel()` is running at zero cost. The `VolumeShareSlippageModel` with default parameters gives ~0.6bps for a 2.5% participation rate — optimistic for all but the most liquid stocks.

6. **The Framework layer cleanly separates alpha from sizing from execution.** Alpha model emits `Insight` objects with direction and confidence. Portfolio construction converts these to `PortfolioTarget` percent weights. Risk management modifies the targets. Execution model converts targets to orders. Each layer is swappable via a single `Set*()` call. This is the right architecture for institutional-quality strategy development.

7. **Index construction is supported but turnover and capacity are not out of the box.** `EqualWeightingPortfolioConstructionModel` gives 1/N weights. Cap weighting, turnover constraints, and ADV-gated execution require custom `IPortfolioConstructionModel` and `IExecutionModel` implementations — but the extension points are clean and well-defined.

8. **Python performance is a real constraint.** Every callback crosses the CLR-Python boundary via PythonNet. Python strategies run 5-15x slower than C# for equivalent logic. For large universes (500+ symbols at minute resolution), Python backtests are not practically viable in a single machine; prefer C# for production and Python for prototyping.

9. **Determinism is guaranteed for C# strategies, conditional for Python.** The engine's time advancement is purely data-driven. Fill prices are deterministic given a fill model and data. But Python's dictionary iteration order (pre-3.7), non-seeded `random`, and external library state can break reproducibility silently.

10. **Corporate action handling is event-driven, not retroactive.** Splits fire `Warning` one bar early, then `Occurred` on split date. The engine rescales open positions and orders. Options writers must handle assignment manually in `OnOptionAssignment()`. This is the right design for live trading parity.

11. **The config system is powerful but has no schema enforcement.** A typo in `config.json` produces a silent failure (null data feed, wrong handler class) rather than a startup error. Production deployments should add a config validation layer before launching the engine.

12. **`ObjectStore` is the right persistence primitive.** Use it to store intermediate results, parameter snapshots, holdings history, and audit logs across live trading sessions. It's the only built-in mechanism for stateful resumption.

---

### E.2 War Story Examples

**War Story 1: Catching bar-close lookahead in a momentum strategy**

> "I built a 12-1 month momentum strategy in LEAN. Initial results showed a Sharpe of 1.8 with near-zero drawdowns — suspiciously good. I traced the fills back to `EquityFillModel.MarketFill` and found it was filling at `GetBestEffortAskPrice()` which uses the closing bid/ask of the daily bar — the same bar that triggered the signal. The strategy was effectively buying today's close using today's close as the signal, which is impossible in reality. Switching to `MarketOnOpenOrder` dropped the Sharpe to 0.9 and added realistic drawdowns — a much more honest simulation. The fix was one line: `MarketOnOpenOrder(symbol, quantity)` instead of `MarketOrder(symbol, quantity)` in `OnData`."

**War Story 2: Survivorship bias in sector rotation**

> "I ran a sector rotation backtest using a static list of S&P 500 sector ETFs with 10 years of history. Returns looked great. Then I noticed one of the ETFs — XLE for energy — had completely different volatility characteristics pre-2016. I was accidentally including a reconstituted version of the ETF that had gone through significant NAV resets. The framework's `MapFile` correctly tracked ticker changes, but I had bypassed it by using hardcoded symbols from today's ETF universe. The right approach was to use `ETFConstituentsUniverseSelectionModel` which reads historical constituent files, or to explicitly validate that each ticker existed and traded continuously through the full backtest window by checking `Security.DelistingDate` and `Security.Exchange.Hours`."

**War Story 3: Factor file adjustment creating apparent alpha**

> "A long-short equity model showed remarkable P&L in 2020-2021. When I decomposed the returns, I found a disproportionate gain clustered around several biotech stocks. The strategy was using adjusted closing prices with `DataNormalizationMode.Adjusted`. Several stocks had 10:1 forward splits in 2021 that caused the 2020 price history to be retroactively divided by 10. The strategy's trend signal interpreted this factor-adjusted price decline as a short signal in 2020 — but the decline was entirely artificial, caused by a split that happened after the decision date. Switching to `DataNormalizationMode.Raw` and handling split discontinuities explicitly via `OnSplits()` eliminated the spurious alpha. The lesson: never use adjusted prices for price-level signals; use them only for return-based computations."

---

### E.3 Recommended Mini Project: Survivorship-Free Index Backtest with Governance

**Scope: 1–2 weeks**

**Goal:** Build a market-cap-weighted top-100 US equity index backtest with full bias controls, transaction cost modeling, and an audit trail.

**Week 1: Core strategy and bias controls**

```
Day 1-2: Universe setup
  - FundamentalUniverseSelectionModel, daily selection
  - Validate: print selected symbols on each selection date, confirm delisted names exit
  - Implement OnSecuritiesChanged: liquidate removals, set up indicators on adds

Day 3: Weighting and execution
  - Custom MarketCapWeightedPCM
  - ImmediateExecutionModel overridden to use MarketOnOpenOrder
  - Monthly rebalance via Schedule.On(DateRules.MonthStart)

Day 4: Transaction costs
  - SetBrokerageModel(InteractiveBrokers)
  - VolumeShareSlippageModel(volumeLimit: 0.05m, priceImpact: 0.1m)
  - Cap single-name order at 5% of 20-day ADV

Day 5: Benchmark and drift
  - SetBenchmark("SPY") for SPY total-return comparison
  - Implement drift-triggered rebalance (>2% deviation triggers partial rebalance)
```

**Week 2: Governance, validation, and analysis**

```
Day 6-7: Audit trail
  - ObjectStore: log every rebalance date, target weights, actual fills, realized costs
  - Log factor file min/max for each symbol to detect adjustment anomalies

Day 8: Data quality gates
  - BaseData.Reader() override to flag zero-volume, stale bars, negative prices
  - Count fill-forward bars per symbol per quarter; alert if >20%

Day 9: Bias validation
  - Run two versions: MarketOrder vs MarketOnOpenOrder — document Sharpe difference
  - Run with 0 slippage vs VolumeShareSlippage — document impact
  - Run with ConstantFeeModel(0) vs InteractiveBrokersFeeModel — document impact

Day 10: Out-of-sample validation
  - IS: 2010-2018 (optimize sector weights)
  - OOS: 2018-2023 (fixed weights from IS)
  - Compare IS vs OOS performance degradation — document honestly
```

**Key files to implement against:**
- [`Algorithm.Framework/Selection/FundamentalUniverseSelectionModel.cs`] — base class for universe
- [`Algorithm.Framework/Portfolio/PortfolioConstructionModel.cs`] — base class for weighting
- [`Common/Orders/Slippage/VolumeShareSlippageModel.cs`] — copy and customize
- [`Engine/Storage/LocalObjectStore.cs`] — persistence API
- [`Common/Statistics/StatisticsBuilder.cs`] — understand output metrics

**Deliverables:**
1. Source code in C# with clear comments on every bias-control decision
2. A one-page comparison table: Biased run vs. Clean run (Sharpe, max DD, turnover, cost-adjusted CAGR)
3. Audit log sample: first 10 rebalance events with weights, fills, realized slippage
4. OOS degradation analysis: "IS Sharpe 0.92 → OOS Sharpe 0.71, 23% degradation within expected range for a long-only systematic strategy"
