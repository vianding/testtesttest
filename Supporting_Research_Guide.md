# B_liq calibration — supporting research and data guide

**Status:** final standalone research package for senior quant / desk challenge, **not** legal verification or model production approval. The main calibration paper is `SPIRE_Bliq_Calibration_Final.pdf` (editable `*.md`). Agent B's integration brief is a separate file.

## Research question and answer

Estimate **net cash accessible from French OAT€i collateral within the applicable contractual disposal/close-out window conditional on French sovereign default**. The proposed model uses a four-state recovery distribution normalized by indexed principal. The central Balanced mean is 36%; downside/upside sensitivity means are 19.75% and 53.75%. All scenario probabilities and recovery levels are judgmental, constrained by historical evidence; they are not statistically fitted French parameters.

## Evidence and limitations by source

| Evidence | File(s) | Valid inference | Important restriction |
|---|---|---|---|
| Daily Bloomberg indicative bids | `normalized_daily_history.csv`, `selection_register_corrected.csv`, `event_evidence_register.csv` | Seven sovereign-event stress price anchors, 22 distinct securities, window quote coverage | Not executable prices, not a random sovereign sample, not French bonds |
| Bloomberg source extracts | `source_data/Bloomberg_*` | Original quote blocks, event dates, metadata | Check quote units, bid/ask, accrued/flat trading; original exports are preserved |
| Restructuring haircut observations | `individual_restructurings_clean.csv`, `source_data/Master_Haircuts_Publication April 2025.xlsx` | Eventual restructuring NPV haircut/recovery plausibility | Not short-window sale proceeds; heterogeneous methodologies |
| Historical haircut cross-check | `source_data/Cruces-Trebesch-Haircuts-2014-update.xlsx` | Published historical reference | Not independent executable quote evidence |
| Sovereign defaults cross-check | `source_data/DEBT_2025.csv` | Default events/amounts | Not recovery or saleability dataset |
| Event-to-restructuring mapping | `event_market_haircut_crosswalk.csv` | Diagnostic contrast of different estimands | Country/event-year links, not matched securities and dates |
| Fiscal diagnostics | `macro_event_crosswalk.csv`, `market_macro_diagnostics.csv`, `event_selection_sensitivity.csv` | Sensitivity to event selection | No supported fitted French forecast; correlations unstable |
| Scenario parameters | `scenario_parameters.csv`, `calibration_decomposition.csv`, `balanced_sensitivity.csv` | Reproducible model inputs and illustration | Expert judgment, not fitted probabilities or confidence bounds |
| Research summary | `source_data/Bloomberg_research_summary.txt` | Original Bloomberg research request and findings | Summary mentions 17 additional windows; raw exports contain 16 distinct additional ISINs |

## Selection and source policy

The 22 securities are not 22 independent default episodes. Three primary Tier A events (Argentina 2020, Zambia 2020, Ghana 2022); two conditional Tier B events (Ecuador 2020, Ukraine 2024); and two supplementary Tier C events (Greece 2012, Ukraine 2015). Tiering is qualitative, not probability weighting. Greece's observed bond is English-law and does not represent Greek-law PSI. Ukraine 2015 T0 is a restructuring agreement date, so its high post-agreement bids cannot be read as initial default prices. Ukraine 2024 uses substituted ISINs due quote availability. Missing quotes are not zero-valued sales. Historical availability creates selection bias.

The public haircut dataset reports **eventual NPV restructuring outcomes**. A median implied recovery of about 65.75% among 184 bounded modern observations is not an estimate of French 30-day accessible proceeds. A cross-country regression with fiscal variables was not validated: the seven-event debt/GDP diagnostic reverses sign under tier restrictions and the larger restructuring panel lacks a harmonized point-in-time macro covariate join. Do not manufacture a French macro transfer coefficient.

## Model definition and checks

F(t)=N×IR(t). For mode m and state k, accessible collateral cash B_liq(t,k)=F(t)×R(m,k) subject to executed legal and waterfall conventions. Conditional loss L(t,k)=max(V_closeout(t,k)−B_liq(t,k),0). Aggregate statewise losses with mode-specific weights; full CVA is computed by the existing default-time/discounting framework. The no-access state means no timely proceeds, not zero ultimate claim value.

Balanced weights `[.10,.25,.45,.20]` and recoveries `[0,.20,.40,.65]` give 36% mean. Conservative `[.25,.35,.30,.10]` × `[0,.15,.30,.55]` gives 19.75%. Aggressive `[.05,.15,.40,.40]` × `[0,.25,.50,.75]` gives 53.75%.

Illustrative inputs N=EUR 51m, IR=1.3195, V_closeout=EUR 19.62m yield F=EUR 67.2945m and Balanced expected positive-part loss EUR 3.502m. These are illustration inputs, not verified transaction cash flows. Do not apply positive part after averaging recovery or a second LGD/haircut.

## Legal and implementation gates

Confirm executed SPIRE Series XS3411888731 Final Terms, swap confirmation, CSA, waterfall, actual Reference Business Day calendar and disposal window/election; determine permitted/required sale timing and settlement; validate the legal treatment of inflation-indexed OAT FR0013209871 claims, coupon/accrual and index discontinuity. Validate desk scenario sign-off and independent implementation tests. A 30 TARGET-day window is a **research convention** until Series-specific terms are confirmed. No generic disposal charges are assumed.

## Package usage

Use `SPIRE_Bliq_Calibration_Final.md` for portable, modular sections; use the PDF for human review. Agent B should import concise model definitions and the scenario table into the main paper and cross-reference the empirical appendices rather than reproduce all raw rows. `build_calibration.py` recreates the paper from the derived v0.7 CSVs if the original workspace hierarchy is retained; this package's supplied PDF/MD/CSVs are the fixed reviewed snapshot. For a fully standalone reproducible rerun, first adjust its `src` directory to this package's data folder. Original Bloomberg extracts and public datasets are copied without modification into `source_data`.
