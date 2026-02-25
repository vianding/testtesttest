# LEAN Algorithmic Trading Engine — Architecture Overview

> Developer-facing reference. Generated from codebase analysis of LEAN v2.0.

---

## Table of Contents

1. [System Goals & Scope](#1-system-goals--scope)
2. [High-Level Component Diagram](#2-high-level-component-diagram)
3. [Core Runtime Flow — End to End](#3-core-runtime-flow--end-to-end)
4. [Data Architecture](#4-data-architecture)
5. [Strategy Abstraction Layer](#5-strategy-abstraction-layer)
6. [Extensibility Points](#6-extensibility-points)
7. [Concurrency Model](#7-concurrency-model)
8. [Testing & Reproducibility](#8-testing--reproducibility)
9. [Key Files Quick Reference](#9-key-files-quick-reference)

---

## 1. System Goals & Scope

### In Scope

- Event-driven backtesting over historical tick/bar data (full resolution fidelity)
- Live and paper trading via external brokerages or exchange APIs
- Research/notebook environment (`QuantBook`) for exploratory analysis
- Multi-asset class support: Equities, Options, Futures, Forex, CFD, Crypto, CryptoFutures, Index Options, ETFs
- Multi-currency portfolios with real-time FX conversion
- Universe selection (static, fundamental-driven, dynamic filter, ETF constituents, option/futures chains)
- Parameter optimization over backtest runs (`Optimizer/`)
- Signal export to external platforms (Collective2, NumerAI, CrunchDAO)

### Explicitly Out of Scope

- Prime brokerage clearing or custody operations
- Market microstructure simulation (order-book depth, queue priority)
- Production-grade OMS/EMS routing with exchange connectivity (those live in separate brokerage plugin assemblies)
- Real-time risk limits beyond margin/drawdown checks (no VaR, scenario, or stress models in core)
- Cloud infrastructure, identity, and billing (handled by QuantConnect's SaaS layer)

---

## 2. High-Level Component Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                        Launcher / config.json                       │
│         Selects environment (backtesting / live-*)                  │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
                ┌──────────────▼──────────────┐
                │         Engine.cs           │
                │  LeanEngineSystemHandlers   │◄── IJobQueueHandler
                │  LeanEngineAlgorithmHandlers│◄── IMessagingHandler
                └──────────────┬──────────────┘
                               │
            ┌──────────────────▼──────────────────┐
            │           AlgorithmManager           │
            │  Main event loop: foreach TimeSlice  │
            └───┬───────┬──────┬────────┬──────────┘
                │       │      │        │
    ┌───────────▼─┐  ┌──▼──┐  ┌▼────┐  ┌▼──────────────┐
    │ Synchronizer│  │Real │  │Trans│  │ ResultHandler │
    │ (TimeSlice) │  │Time │  │Hndlr│  │ (metrics/log) │
    └───────┬─────┘  │Hndlr│  └───┬─┘  └───────────────┘
            │        └──┬──┘      │
    ┌───────▼──────┐    │     ┌───▼────────┐
    │   DataFeed   │    │     │  Brokerage │
    │ (File/Live)  │    │     │(IBrokerage │
    └──────┬───────┘ Schedules│/IFillModel)│
           │                  └────────────┘
    ┌──────▼───────────────────────────────────────┐
    │              DataManager                     │
    │  (Subscriptions, UniverseSelection,          │
    │   Factor/Map files, Data Permissions)        │
    └──────────────────────────────────────────────┘
           │
    ┌──────▼──────────────────────────────────────────────┐
    │                    IAlgorithm                       │
    │  QCAlgorithm: Portfolio, Securities, Schedule,      │
    │  SubscriptionManager, HistoryProvider, TradeBuilder │
    │                                                     │
    │  [Optional Framework Layer]                         │
    │  Alpha → PortfolioConstruction → Risk → Execution   │
    └─────────────────────────────────────────────────────┘
```

### Handler Swap Boundaries

|       Interface       | Backtesting Implementation |     Live Implementation     |
|-----------------------|----------------------------|-----------------------------|
| `IDataFeed`           | `FileSystemDataFeed`       | `LiveTradingDataFeed`       |
| `ISynchronizer`       | `Synchronizer`             | `LiveSynchronizer`          |
| `ITransactionHandler` | `BacktestingTransactionHandler` | `BrokerageTransactionHandler` |
| `IResultHandler`      | `BacktestingResultHandler` | `LiveTradingResultHandler`  |
| `IRealTimeHandler`    | `BacktestingRealTimeHandler` | `LiveTradingRealTimeHandler` |
| `ISetupHandler`       | `BacktestingSetupHandler`  | `BrokerageSetupHandler`     |
| `IBrokerage`          | `BacktestingBrokerage`     | `InteractiveBrokersBrokerage`, `CoinbaseBrokerage`, etc. |
| `IHistoryProvider`    | `SubscriptionDataReaderHistoryProvider` | `BrokerageHistoryProvider` |

All handler types are resolved at runtime via MEF (`Composer.Instance`) using fully-qualified class name strings from `config.json`. Swapping a component requires only a config change — no source modifications to the engine core.

---

## 3. Core Runtime Flow — End to End

### Step-by-Step

```
① config.json → Engine.Run(AlgorithmNodePacket)
│   • Load MarketHoursDatabase (async, parallel with init)
│   • Initialize messaging system
│   • Initialize result handler
│
② Engine: Initialize All Handlers
│   • CreateAlgorithmInstance()    [AlgorithmFactory / Python via PythonNet]
│   • CreateBrokerage()            [IBrokerageFactory]
│   • SecurityService              [builds Security objects per subscription]
│   • DataManager                  [IDataFeed + UniverseSelection wired together]
│   • Synchronizer.Initialize()
│   • HistoryProvider.Initialize()
│   • SetupHandler.Setup()         → calls algorithm.Initialize()
│
③ AlgorithmManager.Run()           [the main loop]
│   foreach TimeSlice in Synchronizer.StreamData():
│   │
│   ├─ realtime.ScanPastEvents()       [fire any missed scheduled events]
│   ├─ SubscriptionManager.ScanPastConsolidators()
│   ├─ algorithm.SetDateTime(time)     [advance algorithm clock]
│   │
│   ├─ [if IsTimePulse] → continue    [no market data; just time advance]
│   │
│   ├─ security.Update(data)           [update price cache, TradeBuilder.SetMarketPrice()]
│   ├─ cash.Update()                   [FX conversion rate refresh]
│   ├─ portfolio.InvalidateTotalPortfolioValue()
│   │
│   ├─ HandleDividends()
│   │   • Distribute cash to CashBook
│   │   • Fire algorithm.OnDividends()
│   │
│   ├─ HandleSplits()
│   │   • Rescale holding quantity and average price
│   │   • Rescale open orders via IBrokerageModel.ApplySplit()
│   │   • Fire algorithm.OnSplits()
│   │
│   ├─ algorithm.OnSecuritiesChanged() / OnFrameworkSecuritiesChanged()
│   │
│   ├─ transactions.ProcessSynchronousEvents()
│   │   • Evaluate all pending non-market orders against current prices
│   │   • Generate OrderEvent fills
│   │   • Update SecurityHolding (quantity, average price, realized PnL)
│   │   • Update CashBook
│   │
│   ├─ realtime.SetTime()              [fire scheduled events at their exact time]
│   │
│   ├─ consolidators.Update() / Scan() [feed data through user consolidators]
│   │
│   ├─ custom data handlers            [reflection-based dispatch to OnData<T>()]
│   │
│   ├─ algorithm.OnData(Slice)         ← PRIMARY STRATEGY ENTRY POINT
│   │
│   ├─ margin call check (every 5 min):
│   │   MarginCallModel.GetMarginCallOrders() → algorithm.OnMarginCall()
│   │
│   └─ results.Sample()                [daily equity curve sample point]
│
④ Post-run
    • ResultHandler.SendFinalResult()
    • StatisticsBuilder compiles StatisticsResults
      (Sharpe, Sortino, Calmar, CAR, Drawdown, Win/Loss ratios, etc.)
    • ObjectStore / Report persistence
```

### State Storage

| State | Owner | Notes |
|---|---|---|
| Per-security price | `Security.Cache` (`DynamicSecurityData`) | Overwritten each time step |
| Portfolio positions | `SecurityPortfolioManager` | In-memory; persisted via result handler |
| Cash balances | `CashBook` (settled) + `UnsettledCashBook` | Multi-currency |
| Open orders | `SecurityTransactionManager` | In-memory concurrent dictionary |
| Order history | `BrokerageTransactionHandler` | Serialized on request |
| Equity curve / charts | `BaseResultsHandler` | Downsampled, streamed to disk/cloud |
| Subscription metadata | `DataManager` | Rebuilt on each run |
| User data | `IObjectStore` | Local file or cloud-backed; persists across runs |

### Determinism

| Factor | Guarantee |
|---|---|
| Time advancement | Deterministic — data-driven; no `DateTime.UtcNow` in engine core |
| Data ordering | `SubscriptionSynchronizer` sorts by `EndTime`, then stable enumerator order |
| Fill price | Configurable but deterministic — `ImmediateFillModel` uses current bar prices |
| Price adjustments | Static factor files on disk; same file → same adjustments |
| Indicators | Pure functions of data; deterministic given same input |

**Reproducibility-breakers to watch for:**

- `System.Random` / `numpy.random` in strategy code without an explicit seed
- External data downloads at runtime (cache results in `IObjectStore`)
- Live warmup using brokerage history (may differ from stored historical data)
- Python interop non-determinism from CPython's dict ordering (rare, Python 3.7+)

---

## 4. Data Architecture

### 4.1 Data Sources & Types

| Type | Class | Granularity | Notes |
|---|---|---|---|
| Trade candle | `TradeBar` | Tick → Daily | OHLCV |
| Quote candle | `QuoteBar` | Tick → Daily | Bid/Ask OHLCV with size |
| Raw tick | `Tick` | Sub-second | `TickType`: Trade / Quote / OpenInterest |
| Open interest | `OpenInterest` | Daily | Options / Futures |
| Dividend | `Dividend` | Event | `Distribution` amount + `ReferencePrice` |
| Split | `Split` | Event | `SplitFactor` ratio + `ReferencePrice` |
| Delisting | `Delisting` | Event | Warning + removal |
| Symbol change | `SymbolChangedEvent` | Event | Ticker rename; triggers order cancellation |
| Option Greeks | `Greeks` | Per contract | Model-based (`IndicatorBasedOptionPriceModelProvider`) |
| Fundamentals | `Fundamental` | Daily | Morningstar data via `FineFundamentalUniverse` |
| Custom data | `BaseData` subclass | User-defined | Override `GetSource()` + `Reader()` |
| ETF constituents | `ETFConstituentData` | Periodic | Via `ETFConstituentsUniverseSelectionModel` |

**Key files:**
- [`Common/Data/Market/TradeBar.cs`](Common/Data/Market/TradeBar.cs)
- [`Common/Data/Market/Tick.cs`](Common/Data/Market/Tick.cs)
- [`Common/Data/Market/QuoteBar.cs`](Common/Data/Market/QuoteBar.cs)
- [`Common/Data/Market/Dividend.cs`](Common/Data/Market/Dividend.cs)
- [`Common/Data/Market/Split.cs`](Common/Data/Market/Split.cs)

### 4.2 Corporate Actions & Adjustments

#### Map Files (ticker rename tracking)
- `MapFile` + `LocalDiskMapFileProvider` store dated ticker→cusip mappings
- `SubscriptionDataReader` resolves the correct filename for a given date
- On a ticker change, a `SymbolChangedEvent` fires; open orders for the old symbol are automatically cancelled

#### Factor Files (price adjustment)
- `FactorFile` stores cumulative backward adjustment factors per security per date
- One factor covers splits; another covers dividends
- `PriceScaleFactorEnumerator` wraps the raw data enumerator and multiplies prices by the factor

#### Normalization Modes (`DataNormalizationMode`)

| Mode | Effect |
|---|---|
| `Raw` | No adjustment |
| `SplitAdjusted` | Splits only |
| `Adjusted` | Splits + dividends (default for equities) |
| `TotalReturn` | Includes dividend reinvestment in price |
| `ForwardPanamaCanal` / `BackwardsPanamaCanal` | Futures roll-adjusted prices |

#### Corporate Action Handling at Runtime

1. `DividendEventProvider` / `SplitEventProvider` inject events into the data stream
2. `AlgorithmManager.HandleDividends()`:
   - Adds `Distribution` cash to `CashBook`
   - Calls `algorithm.OnDividends()`
3. `AlgorithmManager.HandleSplits()`:
   - Rescales `SecurityHolding.Quantity` and `AveragePrice`
   - Rescales open order quantities via `IBrokerageModel.ApplySplit()`
   - Calls `algorithm.OnSplits()`

**Key files:**
- [`Common/Data/Auxiliary/FactorFile.cs`](Common/Data/Auxiliary/FactorFile.cs)
- [`Common/Data/Auxiliary/MapFile.cs`](Common/Data/Auxiliary/MapFile.cs)
- [`Engine/DataFeeds/Enumerators/PriceScaleFactorEnumerator.cs`](Engine/DataFeeds/Enumerators/PriceScaleFactorEnumerator.cs)
- [`Engine/DataFeeds/Enumerators/DividendEventProvider.cs`](Engine/DataFeeds/Enumerators/DividendEventProvider.cs)
- [`Engine/DataFeeds/Enumerators/SplitEventProvider.cs`](Engine/DataFeeds/Enumerators/SplitEventProvider.cs)

### 4.3 Data Pipeline Internals

```
Data Source (zip files on disk / broker websocket)
    ↓
IDataFeed (FileSystemDataFeed or LiveTradingDataFeed)
    ↓
SubscriptionDataReader  (reads & parses raw bytes per subscription)
    ↓
PriceScaleFactorEnumerator  (applies factor file adjustments)
    ↓
FillForwardEnumerator  (propagates last price across gaps)
    ↓
AuxiliaryDataEnumerator  (injects dividends, splits, delistings)
    ↓
SubscriptionSynchronizer  (merges all subscriptions by time → frontier)
    ↓
TimeSliceFactory  (builds TimeSlice: groups data, classifies updates)
    ↓
AlgorithmManager  (dispatches to algorithm callbacks)
```

### 4.4 Missing Data & Fill-Forward

- `FillForwardEnumerator`: If no data exists for a bar, the last known price is replicated forward
- Controlled per-subscription via `SubscriptionDataConfig.FillDataForward`
- Disabled automatically for non-tradable times (market closed) unless `ExtendedMarketHours = true`
- `show-missing-data-logs: true` in config emits warnings for missing source files
- No built-in outlier/bad-tick rejection in core engine; filter at the `IDataQueueHandler` level for live data

---

## 5. Strategy Abstraction Layer

### 5.1 Algorithm Callbacks (Entry Points)

| Callback | When Called | Typical Use |
|---|---|---|
| `Initialize()` | Once at startup | Subscribe data, set dates, configure brokerage model |
| `OnData(Slice)` | Every time step with market data | Signal generation, order placement |
| `OnWarmupFinished()` | After warmup period completes | Initialize strategy state |
| `OnSecuritiesChanged(SecurityChanges)` | Universe membership change | Subscribe indicators on add, liquidate on remove |
| `OnDividends(Dividends)` | Dividend event | Custom cash/position handling |
| `OnSplits(Splits)` | Split event | Override automatic rescaling |
| `OnDelistings(Delistings)` | Delisting event | Exit positions before removal |
| `OnOrderEvent(OrderEvent)` | Fill / rejection / update | Confirm execution, chain orders |
| `OnMarginCall(List<SubmitOrderRequest>)` | Margin breach | Override/cancel forced liquidations |
| `OnMarginCallWarning()` | Near-margin breach | Preemptive risk reduction |
| `OnBrokerageMessage(BrokerageMessageEvent)` | Brokerage notification | Log, alert, reconnect logic |
| `OnEndOfDay(Symbol)` | End of trading session | EOD cleanup |
| `OnEndOfAlgorithm()` | Run complete | Final reporting, cleanup |
| Scheduled events | User-defined time rules | Rebalancing, parameter updates |

**Key files:**
- [`Algorithm/QCAlgorithm.cs`](Algorithm/QCAlgorithm.cs)
- [`Algorithm/QCAlgorithm.Trading.cs`](Algorithm/QCAlgorithm.Trading.cs)
- [`Algorithm/QCAlgorithm.History.cs`](Algorithm/QCAlgorithm.History.cs)
- [`Algorithm/QCAlgorithm.Framework.cs`](Algorithm/QCAlgorithm.Framework.cs)
- [`Algorithm/QCAlgorithm.Universe.cs`](Algorithm/QCAlgorithm.Universe.cs)

### 5.2 Trading API

```csharp
// Basic orders
Order(symbol, quantity)
Buy(symbol, quantity)
Sell(symbol, quantity)
LimitOrder(symbol, quantity, limitPrice)
StopMarketOrder(symbol, quantity, stopPrice)
StopLimitOrder(symbol, quantity, stopPrice, limitPrice)
MarketOnOpenOrder(symbol, quantity)
MarketOnCloseOrder(symbol, quantity)
TrailingStopOrder(symbol, quantity, trailingAmount)
OptionExerciseOrder(optionSymbol, quantity)

// Position sizing
SetHoldings(symbol, percentOfPortfolio)
CalculateOrderQuantity(symbol, targetPercent)

// Order management
Liquidate(symbol)
Transactions.GetOpenOrders()
Transactions.CancelOrder(orderId)

// History
History<TradeBar>(symbol, periods, resolution)
History(symbols, start, end, resolution)
SetWarmup(timeSpan)
SetWarmup(barCount, resolution)
```

### 5.3 Framework Layer — Separation of Concerns

The `Algorithm.Framework` namespace provides an optional but recommended four-layer pipeline:

```
Raw Market Data
       ↓
  Alpha Model  (IAlphaModel)
       ↓  emits Insight objects
       │  Insight: Symbol, Direction, Magnitude, Confidence, Period, Weight
       ↓
Portfolio Construction  (IPortfolioConstructionModel)
       ↓  emits IPortfolioTarget objects
       │  PortfolioTarget: Symbol, Percent
       ↓
Risk Management  (IRiskManagementModel)
       ↓  modifies / removes targets
       ↓
Execution Model  (IExecutionModel)
       ↓  converts targets to orders over time
       ↓
Brokerage → Fills → Portfolio Update
```

#### Built-in Alpha Models
- `EmaCrossAlphaModel` — EMA crossover signals
- `MacdAlphaModel` — MACD crossover signals
- `RsiAlphaModel` — RSI overbought/oversold
- `HistoricalReturnsAlphaModel` — Momentum from historical returns
- `PearsonCorrelationPairsTradingAlphaModel` — Statistical arbitrage pairs

#### Built-in Portfolio Construction Models
- `EqualWeightingPortfolioConstructionModel`
- `BlackLittermanOptimizationPortfolioConstructionModel`
- `RiskParityPortfolioConstructionModel`
- `MeanVarianceOptimizationPortfolioConstructionModel`
- `MaximumSharpeRatioPortfolioOptimizer`
- `ConfidenceWeightedPortfolioConstructionModel`
- `AccumulativeInsightPortfolioConstructionModel`

#### Built-in Risk Models
- `MaximumDrawdownPercentPortfolio`
- `MaximumDrawdownPercentPerSecurity`
- `MaximumUnrealizedProfitPercentPerSecurity`
- `TrailingStopRiskManagementModel`
- `MaximumSectorExposureRiskManagementModel`

#### Built-in Execution Models
- `ImmediateExecutionModel` — Market orders immediately
- `VolumeWeightedAveragePriceExecutionModel` — VWAP-based slicing
- `StandardDeviationExecutionModel` — Executes when spread is small
- `SpreadExecutionModel` — Monitors bid-ask spread

**Key files:**
- [`Algorithm.Framework/`](Algorithm.Framework/)
- [`Common/Algorithm/Framework/Alphas/Insight.cs`](Common/Algorithm/Framework/Alphas/Insight.cs)
- [`Common/Algorithm/Framework/Portfolio/PortfolioTarget.cs`](Common/Algorithm/Framework/Portfolio/PortfolioTarget.cs)

### 5.4 Multi-Asset Instrument Representation

All instruments share the `Symbol` struct which encodes:
- Ticker, Market, SecurityType
- For derivatives: Underlying symbol, Expiry, Strike, Right (Call/Put)

`Security` is the uniform base class; asset-specific behavior is in subclasses:

| Asset Class | Security Subclass | Notes |
|---|---|---|
| Equity | `Equity` | Split/dividend adjusted |
| Option | `Option` | Greeks, exercise, assignment |
| Future | `Future` | Continuous mapping, roll logic |
| Forex | `Forex` | 24-hour market, lot sizing |
| CFD | `Cfd` | Margin-based, no delivery |
| Crypto | `Crypto` | 24/7, fee-in-base-currency |
| Crypto Future | `CryptoFuture` | Perpetual swaps |
| Index | `Index` | Non-tradable reference price |
| Index Option | `IndexOption` | Cash-settled |

Derivative chains are exposed via `OptionChain` / `FuturesChain` in the `Slice`, populated by `IOptionChainProvider` / `IFutureChainProvider`. Continuous futures mapping uses `MappingContractFactorProvider` and fires `SymbolChangedEvent` on each roll.

---

## 6. Extensibility Points

| What to Extend | Interface | Registration |
|---|---|---|
| New live data feed | `IDataQueueHandler` | `data-queue-handler` in config |
| New historical data source | `IHistoryProvider` | `history-provider` in config; MEF `Composer` |
| New data type | Subclass `BaseData`, override `GetSource()` + `Reader()` | `algorithm.AddData<T>()` |
| New brokerage (live) | `IBrokerage` + `IBrokerageFactory` + `IBrokerageModel` | `live-mode-brokerage` in config; `[BrokerageFactory]` attribute |
| Fee model | `IFeeModel` | `IBrokerageModel.GetFeeModel()` |
| Fill model | `IFillModel` | `IBrokerageModel.GetFillModel()` |
| Slippage model | `ISlippageModel` | `IBrokerageModel.GetSlippageModel()` |
| Settlement model | `ISettlementModel` | `IBrokerageModel.GetSettlementModel()` |
| Margin interest model | `IMarginInterestRateModel` | `IBrokerageModel.GetMarginInterestRateModel()` |
| Buying power / margin model | `IBuyingPowerModel` | `security.BuyingPowerModel` |
| Margin call model | `IMarginCallModel` | `algorithm.Portfolio.MarginCallModel` |
| Universe | Subclass `Universe` | `algorithm.AddUniverse()` |
| Benchmark | `IBenchmark` | `IBrokerageModel.GetBenchmark()` or `algorithm.SetBenchmark()` |
| Alpha model (framework) | `IAlphaModel` | `algorithm.SetAlpha()` |
| Portfolio construction (framework) | `IPortfolioConstructionModel` | `algorithm.SetPortfolioConstruction()` |
| Risk model (framework) | `IRiskManagementModel` | `algorithm.SetRiskManagement()` |
| Execution model (framework) | `IExecutionModel` | `algorithm.SetExecution()` |
| Consolidator | Subclass `DataConsolidator<T>` | `algorithm.Consolidate()` |
| Object persistence | `IObjectStore` | `object-store` in config |
| Signal export | `ISignalExportTarget` | `algorithm.SignalExport.AddSignalExportProviders()` |
| Data permission control | `IDataPermissionManager` | Injected via `DataPermissionsManager` |
| Security initializer | `ISecurityInitializer` | `algorithm.SetSecurityInitializer()` |
| Option price model | `IOptionPriceModel` | `option.PriceModel` |

**Key files:**
- [`Common/Interfaces/`](Common/Interfaces/) — all extensible interfaces
- [`Common/Brokerages/IBrokerageModel.cs`](Common/Brokerages/IBrokerageModel.cs) — brokerage model contract

---

## 7. Concurrency Model

LEAN's threading design follows a strict **single-writer, multiple-reader** pattern.
The algorithm code always runs on one thread; all other components use background threads
that communicate with the algorithm loop via thread-safe queues and events.

### 7.1 Thread Map

| Thread | Name (as set in code) | Count | Mode | Owner file : line |
|---|---|---|---|---|
| **Algorithm loop** | (caller thread / Isolator) | 1 | Both | `Engine/Engine.cs:345` → `AlgorithmManager.cs` |
| **Transaction processor** | `"Transaction Thread {i}"` | 1 (backtest) / 1–4 (live) | Both | `BrokerageTransactionHandler.cs:244` |
| **Result handler** | `"Result Thread"` | 1 | Both | `BaseResultsHandler.cs:496` |
| **RealTime handler** | `"RealTime Thread"` | 1 | Live only | `LiveTradingRealTimeHandler.cs:136` |
| **Data exchange** | `"CustomDataExchange"` (or exchange name) | 1 per exchange | Live only | `BaseDataExchange.cs:147` |
| **Work scheduler pool** | `"WeightedWorkThread{i}"` | N (default 4) | Both | `WeightedWorkScheduler.cs:74` |
| **Live pulse timer** | `"RealTimeScheduleEventService"` | 1 | Live only | `RealTimeScheduleEventService.cs:50` |
| **DB preload task** | ThreadPool | 1 | Both | `Engine/Engine.cs:77` (`Task.Run`) |

### 7.2 The Algorithm Loop Is Single-Threaded

`AlgorithmManager.Run()` is invoked directly by the Isolator inside `Engine.Run()` — on the **same thread that called `Engine.Run()`** (the Launcher's main thread):

```
Engine.Run()
  └─ Isolator.ExecuteWithTimeLimit(...)   ← wraps with timeout/RAM guard
       └─ AlgorithmManager.Run(...)       ← BLOCKS here (no new thread)
            └─ foreach (TimeSlice in synchronizer.StreamData())
```

All algorithm callbacks — `OnData`, `OnOrderEvent`, `OnSecuritiesChanged`,
`OnDividends`, scheduled events — are dispatched on this **one thread** in strict
time-slice order. There is no concurrency within strategy code.

**Key implication:** Strategy code never needs locking. Any access to `Portfolio`,
`Securities`, `Transactions` from `OnData()` is safe without synchronization.

### 7.3 How Components Cross Threads

#### Orders: algorithm → transaction handler
1. Algorithm calls `MarketOrder(...)` → `SecurityTransactionManager.ProcessRequest()`
2. Request is enqueued into `BusyBlockingCollection<OrderRequest>` (blocking, thread-safe queue)
3. `"Transaction Thread"` picks it up, validates with brokerage, places the order
4. `WaitForOrderSubmission()` blocks the algorithm loop for up to 1 second until the
   ticket's `OrderSet.WaitOne()` signals back — making synchronous order submission safe

```
Algorithm Thread                    Transaction Thread
     │                                       │
     ├─ MarketOrder()                        │
     ├─ Enqueue(request) ──────────────────► ├─ Dequeue(request)
     ├─ WaitOne(1s)                          ├─ HandleOrderRequest()
     │                                       ├─ ticket.OrderSet.Set()
     ├─ [unblocks] ◄─────────────────────── │
     └─ returns OrderTicket                  │
```

File: [`Engine/TransactionHandlers/BrokerageTransactionHandler.cs`](Engine/TransactionHandlers/BrokerageTransactionHandler.cs) L225–380

#### Fills: brokerage → algorithm loop
1. Brokerage fires `OrdersStatusChanged` event on **its own thread** (network/IO thread)
2. `HandleOrderEvents()` runs on that brokerage thread, protected by `lock (_lockHandleOrderEvent)` (L832, L1177, L1584)
3. Fill results are written to `ConcurrentQueue<OrderEvent> _orderEvents` (L76) and `ConcurrentDictionary<int, Order> _completeOrders` (L82)
4. On the next time step, the algorithm loop calls `ProcessSynchronousEvents()` which drains `_orderEvents` and fires `algorithm.OnOrderEvent()` — **back on the algorithm thread**

```
Brokerage Thread (network)          Algorithm Thread
     │                                       │
     ├─ OrdersStatusChanged event            │
     ├─ lock(_lockHandleOrderEvent)          │
     ├─ _orderEvents.Enqueue(fill) ─────────►│ (read next time step)
     └─ lock released                        ├─ ProcessSynchronousEvents()
                                             ├─ algorithm.OnOrderEvent(fill)
```

#### Result sampling: algorithm loop → result thread
1. `AlgorithmManager` calls `results.Sample()` at each time step — adds a `Packet` to `ConcurrentQueue<Packet> Messages`
2. `"Result Thread"` drains the queue every 50 ms, serializes charts, streams to disk or cloud
3. No lock required; `ConcurrentQueue` is wait-free for single-producer / single-consumer patterns

#### Live data arrival: brokerage → LiveSynchronizer → algorithm loop
1. Brokerage pushes a tick — fires `Subscription.NewDataAvailable` event on the brokerage thread
2. `LiveSynchronizer.OnSubscriptionNewDataAvailable()` (L226) calls `_newLiveDataEmitted.Set()`
3. `StreamData()` loop is blocked on `_newLiveDataEmitted.Wait()` (L112) — it wakes up, pulls all available data into the next `TimeSlice`, yields to `AlgorithmManager`

```
Brokerage Thread                    LiveSynchronizer Thread
     │                                       │
     ├─ tick arrives                         ├─ _newLiveDataEmitted.Wait()  ← blocking
     ├─ NewDataAvailable event               │
     └─ _newLiveDataEmitted.Set() ─────────►├─ [unblocks]
                                             ├─ Sync() → TimeSlice
                                             └─ yield → AlgorithmManager
```

File: [`Engine/DataFeeds/LiveSynchronizer.cs`](Engine/DataFeeds/LiveSynchronizer.cs) L41, L98–115

### 7.4 Thread-Safety Mechanisms at a Glance

| Mechanism | Where used | Purpose |
|---|---|---|
| `ConcurrentDictionary<int, Order>` | `BrokerageTransactionHandler.cs:82` | Lock-free order storage shared between algorithm and transaction threads |
| `ConcurrentDictionary<int, OrderTicket>` | `BrokerageTransactionHandler.cs:95` | Lock-free ticket storage |
| `ConcurrentQueue<OrderEvent>` | `BrokerageTransactionHandler.cs:76` | Fill events from brokerage thread to algorithm loop |
| `lock (_lockHandleOrderEvent)` | `BrokerageTransactionHandler.cs:109,832,1177,1584` | Serializes fill processing from concurrent brokerage threads |
| `Interlocked.Increment` | `BrokerageTransactionHandler.cs:325,788` | Atomic order counter |
| `Interlocked.Exchange` | `BrokerageTransactionHandler.cs:1287` | Atomic last-fill-time update |
| `ManualResetEventSlim` | `LiveSynchronizer.cs:41` | Efficient wait for new live data |
| `ManualResetEventSlim` | `BaseDataExchange.cs:39` | Thread-safe data exchange signaling |
| `volatile bool ExitTriggered` | `BaseResultsHandler.cs:191` | Shutdown flag visible across threads |
| `BusyBlockingCollection<OrderRequest>` | `BrokerageTransactionHandler.cs:71` | Blocking hand-off of order requests to transaction threads |
| `AutoResetEvent _newWorkEvent` | `WeightedWorkScheduler.cs:50` | Signals pool threads when enumerator work is available |

### 7.5 Backtesting vs Live: Key Differences

| Aspect | Backtesting | Live |
|---|---|---|
| Time provider | `SubscriptionFrontierTimeProvider` — data-driven | `RealTimeProvider` — wall clock |
| Synchronizer wait | None — runs as fast as data allows | Blocks on `ManualResetEventSlim` per tick |
| Transaction threads | 1 (`ConcurrencyEnabled = false` on `BacktestingBrokerage`) | 1–4 (default 4, configurable via `maximum-transaction-threads`) |
| RealTime thread | Not spawned | Spawned on first `SetTime()` call |
| Heartbeat | N/A | Guaranteed time step every ≤ 1 second (`RealTimeScheduleEventService`) |
| Data exchange threads | Not spawned | 1 per `BaseDataExchange` instance |
| Data source | `WeightedWorkScheduler` pool reads zip files | Network-pushed via `IDataQueueHandler` events |

### 7.6 Live Data Burst Behaviour

When multiple ticks arrive between two algorithm loop iterations:

1. Each tick sets `_newLiveDataEmitted` (harmlessly idempotent on `ManualResetEventSlim`)
2. When the synchronizer wakes up, `SubscriptionSynchronizer.Sync()` drains **all** data whose `EmitTimeUtc <= frontierUtc` from all subscriptions into a single `TimeSlice`
3. Only one `TimeSlice` is yielded per loop iteration — data burst is fully absorbed without dropping ticks
4. If processing takes longer than 1 second, the `RealTimeScheduleEventService` pulse fires again; the synchronizer catches up on the next iteration

**Batching delay:** `consumer-batching-timeout-ms` in `config.json` (default `0`) adds an optional sleep before consuming, allowing more ticks to accumulate for coarser-grained time steps under very high tick rates.

File: [`Engine/DataFeeds/LiveSynchronizer.cs`](Engine/DataFeeds/LiveSynchronizer.cs) L36, L98–160

### 7.7 Python Algorithm Concurrency

Python algorithms run via PythonNet. Key implications:

- The **Python GIL** is held during every C#→Python call (each algorithm callback)
- Since all callbacks happen on the single algorithm loop thread, there are no GIL deadlock risks from LEAN itself
- `async`/`threading` in Python strategy code **will** cause issues — those threads cannot call LEAN C# APIs safely because the GIL is not managed by LEAN's thread model
- Universe selection with `Asynchronous = true` can run off the main thread; Python selectors should not be used with that flag

### 7.8 Shutdown Sequence

`Engine.cs` coordinates clean termination after `AlgorithmManager.Run()` returns:

```
1. AlgorithmHandlers.Transactions.Exit()   ← signals BusyBlockingCollection.CompleteAdding()
2. AlgorithmHandlers.RealTime.Exit()       ← cancels RealTime token source
3. AlgorithmHandlers.DataFeed.Exit()       ← sets IsActive = false
4. AlgorithmHandlers.Results.Exit()        ← sets ExitTriggered = true
5. Poll all IsActive flags, 10ms sleep, up to 30 second timeout
```

All background threads are `IsBackground = true` — they are killed by the OS if the
process exits before they drain, making the 30-second timeout a safety net only.

**Key files:**
- [`Engine/Engine.cs`](Engine/Engine.cs) — thread lifecycle orchestration (L345–437)
- [`Engine/TransactionHandlers/BrokerageTransactionHandler.cs`](Engine/TransactionHandlers/BrokerageTransactionHandler.cs) — transaction threads, order queue, locks
- [`Engine/DataFeeds/LiveSynchronizer.cs`](Engine/DataFeeds/LiveSynchronizer.cs) — live data wait/signal
- [`Engine/DataFeeds/BaseDataExchange.cs`](Engine/DataFeeds/BaseDataExchange.cs) — data exchange thread
- [`Engine/DataFeeds/WorkScheduling/WeightedWorkScheduler.cs`](Engine/DataFeeds/WorkScheduling/WeightedWorkScheduler.cs) — backtesting enumerator thread pool
- [`Engine/Results/BaseResultsHandler.cs`](Engine/Results/BaseResultsHandler.cs) — result thread
- [`Engine/RealTime/LiveTradingRealTimeHandler.cs`](Engine/RealTime/LiveTradingRealTimeHandler.cs) — real-time event thread

---

## 8. Testing & Reproducibility

### 7.1 Test Coverage

- **~772 test files** in [`Tests/`](Tests/) covering all major subsystems
- **Regression tests** (`IRegressionAlgorithmDefinition`): full algorithm runs compared against stored expected statistics; run in both C# and Python via CI
- **Unit tests**: per-class coverage for orders, fills, fees, statistics, data parsing, indicators, consolidators, universe selection, scheduling
- **Brokerage model tests** ([`Tests/Brokerages/Models/`](Tests/Brokerages/Models/)): validate order/margin rules per brokerage without network calls
- **Data tests** ([`Tests/Common/Data/`](Tests/Common/Data/)): parsing, normalization, fill-forward, factor file application
- **Integration tests**: `AlgorithmManagerTests`, `AlgorithmHistoryTests`, `DataFeedTests` with in-process data feeds

### 7.2 Determinism Guarantees

| Factor | Guarantee |
|---|---|
| Time advancement | Deterministic — data-driven in backtesting; no `DateTime.UtcNow` in engine core loop |
| Data ordering | `SubscriptionSynchronizer` sorts subscriptions by `EndTime` (stable) |
| Fill price | Configurable but deterministic — `ImmediateFillModel` uses current bar's OHLCV |
| Price adjustments | Static factor files on disk; same file → identical adjustment factors |
| Indicators | Pure functions of data; deterministic given same inputs |
| Python interop | Single-threaded GIL in algorithm callbacks (PythonNet) |

### 7.3 Known Reproducibility-Breakers

- `System.Random` / `numpy.random` in strategy code without an explicit seed
- `DateTime.UtcNow` calls in custom code (engine uses `algorithm.UtcTime` instead)
- External HTTP calls at runtime — cache results in `IObjectStore`
- Live warmup using brokerage history (may differ from stored historical data files)
- Parallel history requests enabled in backtesting (use `parallelHistoryRequestsEnabled: false` if needed)

### 7.4 Configuration Management

- Single JSON file: [`Launcher/config.json`](Launcher/config.json)
- **Layered**: top-level keys are base defaults; `environments` block overrides per named environment
- Runtime resolution: `Config.Get(key)` / `Config.GetDouble()` throughout — no compile-time config baking
- Handler types resolved by fully-qualified class name string → MEF `Composer.Instance`
- Per-algorithm parameters live under `"parameters"` key; accessed in code via `GetParameter<T>("key")`
- Credentials stored as plain-text keys; override with environment variables for production deployments

#### Backtesting Environment (config excerpt)

```json
"backtesting": {
  "live-mode": false,
  "setup-handler":       "QuantConnect.Lean.Engine.Setup.BacktestingSetupHandler",
  "result-handler":      "QuantConnect.Lean.Engine.Results.BacktestingResultHandler",
  "data-feed-handler":   "QuantConnect.Lean.Engine.DataFeeds.FileSystemDataFeed",
  "real-time-handler":   "QuantConnect.Lean.Engine.RealTime.BacktestingRealTimeHandler",
  "history-provider":    ["QuantConnect.Lean.Engine.HistoricalData.SubscriptionDataReaderHistoryProvider"],
  "transaction-handler": "QuantConnect.Lean.Engine.TransactionHandlers.BacktestingTransactionHandler"
}
```

#### Live Trading Environment (config excerpt)

```json
"live-interactive": {
  "live-mode": true,
  "live-mode-brokerage": "InteractiveBrokersBrokerage",
  "data-queue-handler":  ["InteractiveBrokersBrokerage"],
  "setup-handler":       "QuantConnect.Lean.Engine.Setup.BrokerageSetupHandler",
  "result-handler":      "QuantConnect.Lean.Engine.Results.LiveTradingResultHandler",
  "data-feed-handler":   "QuantConnect.Lean.Engine.DataFeeds.LiveTradingDataFeed",
  "real-time-handler":   "QuantConnect.Lean.Engine.RealTime.LiveTradingRealTimeHandler",
  "transaction-handler": "QuantConnect.Lean.Engine.TransactionHandlers.BrokerageTransactionHandler",
  "history-provider":    ["BrokerageHistoryProvider", "SubscriptionDataReaderHistoryProvider"]
}
```

---

## 9. Key Files Quick Reference

### Engine Bootstrap & Loop

| File | Purpose |
|---|---|
| [`Engine/Engine.cs`](Engine/Engine.cs) | Entry point; wires all handlers, calls `AlgorithmManager.Run()` |
| [`Engine/AlgorithmManager.cs`](Engine/AlgorithmManager.cs) | Main per-time-step event loop |
| [`Engine/LeanEngineAlgorithmHandlers.cs`](Engine/LeanEngineAlgorithmHandlers.cs) | Container for algorithm-level handler bundle |
| [`Engine/LeanEngineSystemHandlers.cs`](Engine/LeanEngineSystemHandlers.cs) | Container for system-level handler bundle |
| [`Launcher/config.json`](Launcher/config.json) | All configuration; environment switching |

### Algorithm API

| File | Purpose |
|---|---|
| [`Algorithm/QCAlgorithm.cs`](Algorithm/QCAlgorithm.cs) | Main strategy base class |
| [`Algorithm/QCAlgorithm.Trading.cs`](Algorithm/QCAlgorithm.Trading.cs) | Order placement API |
| [`Algorithm/QCAlgorithm.History.cs`](Algorithm/QCAlgorithm.History.cs) | Historical data requests |
| [`Algorithm/QCAlgorithm.Framework.cs`](Algorithm/QCAlgorithm.Framework.cs) | Framework layer integration |
| [`Algorithm/QCAlgorithm.Universe.cs`](Algorithm/QCAlgorithm.Universe.cs) | Universe selection API |
| [`Common/Interfaces/IAlgorithm.cs`](Common/Interfaces/IAlgorithm.cs) | Full algorithm contract |

### Data Pipeline

| File | Purpose |
|---|---|
| [`Engine/DataFeeds/DataManager.cs`](Engine/DataFeeds/DataManager.cs) | Subscription lifecycle orchestrator |
| [`Engine/DataFeeds/Synchronizer.cs`](Engine/DataFeeds/Synchronizer.cs) | Backtesting time synchronizer |
| [`Engine/DataFeeds/LiveSynchronizer.cs`](Engine/DataFeeds/LiveSynchronizer.cs) | Live trading synchronizer |
| [`Engine/DataFeeds/FileSystemDataFeed.cs`](Engine/DataFeeds/FileSystemDataFeed.cs) | Backtesting data source |
| [`Engine/DataFeeds/LiveTradingDataFeed.cs`](Engine/DataFeeds/LiveTradingDataFeed.cs) | Live data source |
| [`Engine/DataFeeds/SubscriptionSynchronizer.cs`](Engine/DataFeeds/SubscriptionSynchronizer.cs) | Merges subscriptions into frontier |
| [`Engine/DataFeeds/TimeSlice.cs`](Engine/DataFeeds/TimeSlice.cs) | Per-time-step data bundle |
| [`Engine/DataFeeds/UniverseSelection.cs`](Engine/DataFeeds/UniverseSelection.cs) | Dynamic symbol add/remove |
| [`Engine/DataFeeds/SubscriptionDataReader.cs`](Engine/DataFeeds/SubscriptionDataReader.cs) | Reads/parses raw data per subscription |

### Data Types

| File | Purpose |
|---|---|
| [`Common/Data/Market/TradeBar.cs`](Common/Data/Market/TradeBar.cs) | OHLCV candle |
| [`Common/Data/Market/QuoteBar.cs`](Common/Data/Market/QuoteBar.cs) | Bid/Ask OHLCV |
| [`Common/Data/Market/Tick.cs`](Common/Data/Market/Tick.cs) | Raw tick (trade / quote) |
| [`Common/Data/Market/Dividend.cs`](Common/Data/Market/Dividend.cs) | Dividend corporate action |
| [`Common/Data/Market/Split.cs`](Common/Data/Market/Split.cs) | Split corporate action |
| [`Common/Data/Market/OptionChain.cs`](Common/Data/Market/OptionChain.cs) | Option chain snapshot |
| [`Common/Data/Market/FuturesChain.cs`](Common/Data/Market/FuturesChain.cs) | Futures chain snapshot |

### Corporate Actions & Adjustments

| File | Purpose |
|---|---|
| [`Common/Data/Auxiliary/FactorFile.cs`](Common/Data/Auxiliary/FactorFile.cs) | Split + dividend factors |
| [`Common/Data/Auxiliary/MapFile.cs`](Common/Data/Auxiliary/MapFile.cs) | Ticker rename history |
| [`Common/Data/Auxiliary/LocalDiskFactorFileProvider.cs`](Common/Data/Auxiliary/LocalDiskFactorFileProvider.cs) | Factor file loader |
| [`Engine/DataFeeds/Enumerators/PriceScaleFactorEnumerator.cs`](Engine/DataFeeds/Enumerators/PriceScaleFactorEnumerator.cs) | Applies factors to price stream |
| [`Engine/DataFeeds/Enumerators/DividendEventProvider.cs`](Engine/DataFeeds/Enumerators/DividendEventProvider.cs) | Injects dividend events |
| [`Engine/DataFeeds/Enumerators/SplitEventProvider.cs`](Engine/DataFeeds/Enumerators/SplitEventProvider.cs) | Injects split events |

### Portfolio & Orders

| File | Purpose |
|---|---|
| [`Common/Securities/SecurityPortfolioManager.cs`](Common/Securities/SecurityPortfolioManager.cs) | Portfolio state (positions, cash, PnL) |
| [`Common/Securities/CashBook.cs`](Common/Securities/CashBook.cs) | Multi-currency cash ledger |
| [`Common/Securities/SecurityTransactionManager.cs`](Common/Securities/SecurityTransactionManager.cs) | Order routing and tracking |
| [`Common/Orders/Order.cs`](Common/Orders/Order.cs) | Order base class |
| [`Engine/TransactionHandlers/BrokerageTransactionHandler.cs`](Engine/TransactionHandlers/BrokerageTransactionHandler.cs) | Live order processing |
| [`Engine/TransactionHandlers/BacktestingTransactionHandler.cs`](Engine/TransactionHandlers/BacktestingTransactionHandler.cs) | Backtesting order simulation |

### Execution Cost Models

| File | Purpose |
|---|---|
| [`Common/Orders/Fills/FillModel.cs`](Common/Orders/Fills/FillModel.cs) | Base fill model |
| [`Common/Orders/Fills/ImmediateFillModel.cs`](Common/Orders/Fills/ImmediateFillModel.cs) | Default instant fill |
| [`Common/Orders/Fills/EquityFillModel.cs`](Common/Orders/Fills/EquityFillModel.cs) | Equity-specific fills |
| [`Common/Orders/Fees/FeeModel.cs`](Common/Orders/Fees/FeeModel.cs) | Base fee model |
| [`Common/Orders/Slippage/VolumeShareSlippageModel.cs`](Common/Orders/Slippage/VolumeShareSlippageModel.cs) | Volume-weighted slippage |

### Brokerage

| File | Purpose |
|---|---|
| [`Common/Interfaces/IBrokerage.cs`](Common/Interfaces/IBrokerage.cs) | Brokerage interface contract |
| [`Common/Brokerages/IBrokerageModel.cs`](Common/Brokerages/IBrokerageModel.cs) | Brokerage model interface |
| [`Common/Brokerages/DefaultBrokerageModel.cs`](Common/Brokerages/DefaultBrokerageModel.cs) | Default fill/fee/margin defaults |

### Framework Layer

| File | Purpose |
|---|---|
| [`Algorithm.Framework/Alphas/`](Algorithm.Framework/Alphas/) | Built-in alpha models |
| [`Algorithm.Framework/Portfolio/`](Algorithm.Framework/Portfolio/) | Built-in portfolio construction models |
| [`Algorithm.Framework/Risk/`](Algorithm.Framework/Risk/) | Built-in risk models |
| [`Algorithm.Framework/Execution/`](Algorithm.Framework/Execution/) | Built-in execution models |
| [`Common/Algorithm/Framework/Alphas/Insight.cs`](Common/Algorithm/Framework/Alphas/Insight.cs) | Insight signal object |
| [`Common/Algorithm/Framework/Portfolio/PortfolioTarget.cs`](Common/Algorithm/Framework/Portfolio/PortfolioTarget.cs) | Allocation target object |

### Statistics & Results

| File | Purpose |
|---|---|
| [`Common/Statistics/Statistics.cs`](Common/Statistics/Statistics.cs) | Core metric calculations |
| [`Common/Statistics/StatisticsBuilder.cs`](Common/Statistics/StatisticsBuilder.cs) | Assembles final statistics report |
| [`Common/Statistics/PortfolioStatistics.cs`](Common/Statistics/PortfolioStatistics.cs) | Portfolio-level metrics |
| [`Engine/Results/BacktestingResultHandler.cs`](Engine/Results/BacktestingResultHandler.cs) | Backtest result streaming |
| [`Engine/Results/BaseResultsHandler.cs`](Engine/Results/BaseResultsHandler.cs) | Shared result handler logic |

### All Interfaces

| File | Purpose |
|---|---|
| [`Common/Interfaces/`](Common/Interfaces/) | Every extensible interface in the engine |
