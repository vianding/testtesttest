# SPIRE | French OAT€i collateral liquidation calibration

Companion methodology to the SPIRE B_liq and CVA working papers | Final research edition · notation-aligned | 15 October 2026 | Senior quant / desk review | Not yet production approved


## Executive conclusion and recommended model input

We recommend an explicit **conditional-on-French-default, scenario-based recovery on indexed principal**. This separates market accessibility from realized sale value, captures the positive-part loss nonlinearity, and is auditable. It is a proportionate improvement: no unvalidated sovereign macro forecast or elaborate stochastic default model is required.

| Use | Mean recovery / indexed face | No-access assumption | Illustrative expected loss |
|---|---|---|---|
| Central (Balanced) | 36.00% | 10% | EUR 3.50m |
| Downside (Conservative) | 19.75% | 25% | EUR 8.24m |
| Upside (Aggressive) | 53.75% | 5% | EUR 1.40m |

**Decision:** adopt Balanced as a **provisional working conditional recovery distribution**, not as a statistical prediction or a signed-off production parameter. Retain downside/upside as sensitivity sets, not confidence intervals. The 36% scalar is useful for communication; CVA must average losses across states. There is no statistically defensible France-specific macro uplift from the current sample. Closing the research at this point is preferable to adding unsupported complexity.

**Normalization:** recovery is measured against indexed pool face F_τ = N_τ^pool × IR_{τ−}, not pre-default market value. The recovery distribution represents accessible proceeds within the applicable close-out/disposal convention, not ultimate creditor recovery. Distinguish indexed principal, accrued coupons and legal claim recognition; do not infer the recovery denominator from a bond clean-price quotation.


## Notation and integration interface

This companion adopts the *event-state* symbols of the SPIRE CVA methodology. It deliberately does **not** redefine the performing bond model, CSA mechanics, swap close-out, or waterfall.

| Symbol | Meaning in the consolidated methodology | Calibration-paper use |
|---|---|---|
| τ | Original Collateral Default / contractual loss-relevant event time | Conditioning event for the recovery distribution |
| N_t^pool | Base nominal face of the entire original collateral pool | Recovery denominator; not the CSA-posted quantity |
| N_t^post | Base nominal amount posted or credited under the CSA | Performing collateral / FVA quantity; **not** substituted for the pool |
| IR_{τ−} | Relevant OAT€i inflation index ratio immediately before the event | Indexed-face normalization; legal recognition unverified |
| F_τ = N_τ^pool × IR_{τ−} | Indexed face of the relevant pool | Reporting denominator, **not** pre-default MV |
| B_t | Performing-state bond market value (definition depends on main paper) | Not the default-state liquidation amount |
| B_τ^liq | Net proceeds available to Dealer under the liquidation and waterfall assumptions | Calibrated default-state collateral resource |
| R_τ^liq(k) | B_τ^liq(k) / F_τ | State-specific recovery fraction of indexed face |
| ETA_τ | Dealer's contractual swap close-out entitlement | Downstream CVA input; not estimated here |
| L_τ^Dealer | Dealer's loss at the event after resources are credited | Computed by CVA paper, not a second recovery haircut |

**Integration convention.** The simplified formula **B_τ^liq(k) = F_τ R_τ^liq(k)** is the output of this calibration, conditional on the resource and priority assumptions. The calibration paper does not define a new **LGD_SPV** or a new sovereign default curve. If the main paper uses **B_τ^def/restr** for the intermediate distressed claim value, keep it separate from **B_τ^liq**: only the latter is the accessible liquidation resource. **R_eff = Σ_k w_k R_τ^liq(k)** is a reporting scalar, not a replacement for statewise loss.

**Source alignment status.** Notation was cross-checked against the available SPIRE CVA revision methodology and companion default-state realization material. Agent B's actual main B_liq v0.3 manuscript was not available for direct line-by-line verification; the final consolidated author should reconcile its exact symbol definitions before merging. This limitation concerns editorial consistency, not the numerical calibration.

## 1 | Scope, contractual boundary and quantities

Transaction: SPIRE Series XS3411888731, underlying French inflation-linked OAT EUR i FR0013209871, 0.10%, July 2047. The task is to estimate **net collateral value accessible for the relevant close-out** after hypothetical French sovereign distress, under applicable disposal provisions. It is not a prediction of French default probability, nor a revaluation of the entire XVA engine.

Use the CVA paper's event time **τ** and distinguish the **whole collateral pool** from the **CSA-posted amount**. Define indexed pool face **F_τ = N_τ^pool × IR_{τ−}**, where **N_τ^pool** is base face nominal and **IR_{τ−}** is the applicable pre-trigger inflation index ratio. For liquidation state **k**, define **R_τ^liq(k) = B_τ^liq(k) / F_τ**, so **B_τ^liq(k) = F_τ × R_τ^liq(k)**. Here **B_τ^liq** denotes *net Dealer-accessible collateral proceeds after the applicable waterfall*, not the performing bond mark **B_t** or merely gross auction receipts.

The downstream CVA paper defines the swap close-out entitlement **ETA_τ** and Dealer event loss **L_τ^Dealer**. Under the simplified, verified-priority case in which the modeled proceeds are available to satisfy that entitlement, **L_τ^Dealer(k) = [ETA_τ(k) − B_τ^liq(k)]⁺** and **E[L_τ^Dealer | τ] = Σ_k w_k L_τ^Dealer(k)**. The *actual* loss expression must follow the executed Series waterfall and any other available resources. Do not compute **[ETA_τ − E(B_τ^liq)]⁺** or apply a second collateral LGD. The close-out entitlement and indexed face may co-move with the default state.

The 2025 SPIRE Base Prospectus establishes a series-dependent liquidation period. The 30 TARGET-business-day window is the **research convention**, not verified executed-Series election; the actual Reference Business Day definition, sale rights, CSA, waterfall and agent obligations remain contractual verification gates. A zero-access state means no cash credited within the relevant close-out window; it does **not** assert zero ultimate creditor recovery.


## 2 | Evidence objects and their roles

| Quantity / estimand | Data / statistical unit | Permitted use | Not supported |
|---|---|---|---|
| Default-window indicative bid | Bloomberg BVAL daily bids; bond observations nested in 7 events | Market stress anchors, time path, quote coverage | Executable block sale or French default probability |
| Ultimate restructuring NPV recovery | April 2025 haircut database; restructuring records | Severity distribution, external plausibility checks | 30-day liquidation price |
| French default-state OAT EUR i market value | France-specific transfer assumptions informed by historical evidence | Conditional market-value scenario range | A fitted France point prediction |
| Net SPIRE accessible proceeds | Scenario accessibility, sale execution and contract | CVA collateral input and pathwise loss | Inference directly from BVAL or haircut data |

The descriptive Bloomberg statistic is the average of **available** T0-T30 TARGET bids, not a hypothetical optimal or realized sale policy. Restructuring recovery = 1 minus the published NPV haircut, under the authors' discounting and legal conventions. Matching both by sovereign/year is a **diagnostic crosswalk**, not a generic bid-to-haircut spread estimator. Neither historical quote absence nor exchange-driven cessation is coded as zero liquidation proceeds.


## 3 | Bloomberg sample: adequate for anchors, not statistically representative

The supplied Bloomberg research summary and exports establish **22 distinct ISINs in seven event clusters**: 6 original Zambia/Ghana and 16 additional Greece/Ecuador/Argentina/Ukraine securities. The summary discussed 17 windows but 16 distinct new instruments were supplied; the 2024 Ukraine ISIN substitutions reflect actual query availability. BVAL-availability selection is nonrandom and may favor priceable bonds. Sovereign events, not individual bonds or days, are the effective independent units.

| Episode | Bonds | Tier | T0-T30 mean BVAL bid | Role |
|---|---|---|---|---|
| Greece 2012 | 1 | C | 18.6 | English-law bond; not Greek-law PSI representative |
| Ecuador 2020 | 2 | B | 34.0 | Different claim structures; COVID overlap |
| Argentina 2020 | 3 | A | 37.2 | Default-window primary comparator |
| Zambia 2020 | 3 | A | 49.0 | Default-window primary comparator |
| Ghana 2022 | 3 | A | 37.0 | Default-window primary comparator |
| Ukraine 2015 | 5 | C | 76.9 | Post-agreement repricing; not initial default |
| Ukraine 2024 | 5 | B | 33.7 | Substituted ISINs; exchange-driven missingness |

A/B/C are qualitative **research-use tiers**, not probability weights. Equal-event mean bid is 41.1 for Tier A (n=3 events), 38.2 for A+B (n=5), and 40.9 for all seven; corresponding medians 37.2, 37.0 and 37.0. The wide changes and noncomparable episodes argue against a single pooled regression. All 22 bonds are retained in the appendix inventory, with corrected ISIN identifiers.


## 4 | Ultimate restructuring outcomes: independent check, not liquidation calibration

The April 2025 haircut workbook contains 329 individual restructuring records; the specified modern filter (1970-2024) has 193 records and 184 with NPV haircuts bounded between 0 and 100%. Median NPV haircut is 34.25%, hence median implied recovery 65.75%. This is a **restructuring-outcome** statistic, not the expected proceeds from a 30-day forced sale.

| Episode | Market-window mean bid | Published implied NPV recovery |
|---|---|---|
| Greece 2012 | 18.6 | 35.4% |
| Ecuador 2020 | 34.0 | 58.8% |
| Argentina 2020 | 37.2 | 65.4% |
| Zambia 2020 | 49.0 | 36.4% |
| Ghana 2022 | 37.0 | 54.6% |
| Ukraine 2015 | 76.9 | 78.8% |
| Ukraine 2024 | 33.7 | No matched 2024 record |

These are **country/event matches, not identical security and valuation-date matches**. Market bids incorporate expected negotiation, time-to-exchange, liquidity and legal claims; restructuring NPV incorporates later outcomes and study discount rates. Their numerical differences cannot be interpreted as an execution haircut. Cruces-Trebesch 2014 and BoC-BoE DEBT_2025 provide historical-methodology and default classification cross-checks; they do not identify saleability.


## 5 | Transfer to France: deliberate and limited

**Economic/fiscal channel.** Pre-event debt/GDP versus the seven market-window prices has Pearson r = -0.51 for all seven, +0.54 for A+B, and +0.68 for Tier A. The sign reversal across event selections invalidates a mechanically fitted French adjustment. The broad haircut panel lacks a harmonized point-in-time fiscal covariate panel in the current research package; no robust macro-to-France restructuring predictor has been estimated. Debt/GDP alone does not identify willingness to pay, creditor seniority, monetary regime or default resolution.

**Legal/monetary channel.** France is a euro-area sovereign, unlike most of the comparator events. Normal-market OAT liquidity and euro-area institutions may support pricing, while systemic euro stress, domestic-law restructuring, creditor discrimination and dealer withdrawal may reduce realizability. We cannot credibly assign a single numerical French uplift/downlift from these opposing channels.

**Indexed-claim channel.** For the first-order model, normalize proceeds by indexed face F=N x IR immediately before the trigger. This is not a claim that indexed and nominal obligations must recover identically. Recognition of indexation, coupon/accrued claims, index discontinuity, and the non-automatic application of the maturity principal floor in default are legal/model assumptions. The illustrative IR 1.3195 is dated 14 October 2026, not a future fixed parameter.

**Execution/accessibility channel.** BVAL is indicative, not a firm executable bid. A staged liquidation and possible sale restrictions require judgment about realized price and whether proceeds enter the contractual waterfall on time. No direct data identify the probability of zero cash access. This is explicitly an expert-judgment tail scenario, to be challenged by the desk and legal documentation.


## 6 | Recommended liquidation calibration and CVA interface

The scenario distribution is intentionally small: inaccessible/delayed, severe sale, distressed orderly sale, resilient sale. These states combine French market value and disposal accessibility in a **reduced-form recovery**; do not additionally deduct an unquantified execution haircut or reapply a haircut elsewhere. All weights and recovery levels below are judgmental, constrained qualitatively by event-window market prices and restructuring tails.

| Conservative scenario | Probability | Accessible recovery / F |
|---|---|---|
| Market inaccessible / legal delay | 25% | 0% |
| Severe liquidation | 35% | 15% |
| Distressed orderly | 30% | 30% |
| Resilient restructuring | 10% | 55% |
| Balanced scenario | Probability | Accessible recovery / F |
|---|---|---|
| Market inaccessible / legal delay | 10% | 0% |
| Severe liquidation | 25% | 20% |
| Distressed orderly | 45% | 40% |
| Resilient restructuring | 20% | 65% |
| Aggressive scenario | Probability | Accessible recovery / F |
|---|---|---|
| Market inaccessible / legal delay | 5% | 0% |
| Severe liquidation | 15% | 25% |
| Distressed orderly | 40% | 50% |
| Resilient restructuring | 40% | 75% |

Balanced: 10% no access; conditional mean recovery when accessible 40%; unconditional mean 0.9 x 40% = 36%. Conservative: 25% no access and 26.33% conditional recovery, giving 19.75%. Aggressive: 5% no access and 56.58% conditional recovery, giving 53.75%. These are **not statistical confidence bounds**.

Illustration only: N_τ^pool=EUR 51m, IR_{τ−}=1.3195, F_τ=EUR 67.2945m, ETA_τ=EUR 19.62m (illustrative close-out entitlement); break-even R_τ^liq=29.16%. Balanced losses: no-access state contributes EUR 1.962m and severe 20%-recovery state contributes EUR 1.540m; expected loss EUR 3.502m. Conservative EUR 8.239m; Aggressive EUR 1.400m. A 36% mean recovery would yield zero loss if incorrectly applied before the positive part, which demonstrates why scenario averaging is essential.


## 7 | Implementation boundary, validation and desk decision

**Minimal implementation:** obtain the engine's event close-out entitlement ETA_τ and indexed *whole-pool* face F_τ at each relevant simulated French-default state; apply the four-state conditional R_τ^liq distribution to derive B_τ^liq; compute the contractual statewise Dealer loss L_τ^Dealer after the applicable waterfall; then aggregate using the existing CVA default-weighting and discounting machinery. Do not substitute CSA-posted nominal N_t^post for N_τ^pool without verifying the Series entitlement and resource allocation. Do not redesign XVA observation grids, event ordering or numeraire conventions for this calibration. Keep scalar 36% for reporting/sanity checks, not as a substitute for the distribution.

**Required before production:** (1) confirm executed Series liquidation election, Reference Business Day calendar, collateral ownership/CSA and waterfall; (2) obtain desk sign-off on scenario values and especially 10% no-access judgment; (3) verify OAT EUR i default-claim/indexation treatment; (4) independently replicate the inputs and stress CVA across the three modes. The absence of these approvals does not require further historical-data collection to complete this research paper.

**Conclusion:** Balanced 36% is a transparent, scenario-based working calibration of short-window accessible collateral proceeds. It is not a data-fitted France estimate. The calibrated object is accessible proceeds in a short contractual window, not eventual restructuring value. The standalone research is complete for decision and incorporation, subject to the enumerated desk/legal/model-validation gates.


## Appendix A | Evidence hierarchy and selection controls

| Parameter / quantity | Evidence classification | Control |
|---|---|---|
| BVAL quotes, event dates | Observed descriptive data | Retain source, event T0, missingness, quote type |
| Event means / tiers | Calculated / judgmental tier | Equal-event comparisons, no pseudo-replication |
| NPV haircut panel | Observed study outcomes | Source methodology, bounded modern filter |
| Debt/GDP scatter | Exploratory diagnostic | No France forecast, event selection sensitivity |
| OAT EUR i indexed face | Market/contractual input | Refresh IR, validate claim recognition |
| Scenario recovery and weights | Expert judgment informed by evidence | Explicit sign-off, sensitivity, no double haircut |
| 30 TARGET-day window | Analytical assumption | Executed-Series confirmation |


## Appendix B | Complete 22-security selection inventory

| Event | ISIN | Tier | Coverage | Mean bid |
|---|---|---|---|---|
| Argentina 2020 | US040114HQ69 | A | 94% | 38.5 |
| Argentina 2020 | US040114HR43 | A | 94% | 37.1 |
| Argentina 2020 | XS0205537581 | A | 100% | 36.0 |
| Ecuador 2020 | US27927WAD48 | B | 100% | 40.5 |
| Ecuador 2020 | XS1707041429 | B | 100% | 27.6 |
| Ghana 2022 | US37443GAA94 | A | 100% | 38.9 |
| Ghana 2022 | US37443GAB77 | A | 100% | 36.5 |
| Ghana 2022 | US37443GAC50 | A | 100% | 35.5 |
| Greece 2012 | XS0191352847 | C | 100% | 18.6 |
| Ukraine 2015 | US126826AH97 | C | 97% | 76.9 |
| Ukraine 2015 | US603674AA04 | C | 100% | 77.2 |
| Ukraine 2015 | US903724AB80 | C | 100% | 76.7 |
| Ukraine 2015 | US903724AF94 | C | 97% | 76.9 |
| Ukraine 2015 | XS0330917617 | C | 97% | 76.8 |
| Ukraine 2024 | US903724AP76 | B | 71% | 36.5 |
| Ukraine 2024 | US903724AR33 | B | 71% | 33.7 |
| Ukraine 2024 | US903724AS16 | B | 71% | 32.5 |
| Ukraine 2024 | US903724BW19 | B | 71% | 36.2 |
| Ukraine 2024 | XS2015264778 | B | 74% | 29.8 |
| Zambia 2020 | US988895AA69 | A | 100% | 48.3 |
| Zambia 2020 | US988895AE81 | A | 100% | 49.8 |
| Zambia 2020 | US988895AF56 | A | 100% | 49.0 |

Selection register is derived from supplied normalized Bloomberg histories. The final register is reconciled to original daily-history ISIN values. Refer to selection_register_corrected.csv for instrument descriptions and detailed restrictions.


## Appendix C | Definitions, data provenance and reproducibility

Bloomberg source histories: SPIRE_refHistData(1).csv (Zambia/Ghana), bliq_calibration10a_histData(1).csv, 10b and 10c (five additional episodes), plus the supplied Bloomberg research summary. The new Ukraine 2024 ISINs are replacements driven by historical quote availability; they are not the same instruments as those originally requested in the summary. Dates and TARGET offsets are independently recalculated; missing quotes are not interpolated. Accrued fields exist in newer exports but cannot automatically be added to flat-traded/defaulted quotes without convention verification.

Public source files: Master_Haircuts_Publication April 2025.xlsx; Cruces-Trebesch-Haircuts-2014-update.xlsx; DEBT_2025.csv. Macro event-year crosswalk is assembled from prior research iterations, with mixed source vintages and definitions. Supporting CSVs in this package preserve the original derived records, event selection, sensitivity, scenario calculations and daily quotes. Statistical independence is at the sovereign-event level; no inference assumes 22 independent sovereign defaults.

The accompanying Markdown manuscript is section-modular: Sections 1-2 define the model interface; Sections 3-5 supply empirical and transfer support; Section 6 contains portable calibration parameters; Section 7 supplies the implementation and sign-off boundary. These sections may be folded into the main B(liq) white paper without relying on earlier calibration drafts.
