# Agent B — consolidate B_liq working paper v0.3 → v0.5

## Assignment and authority

Revise the **existing main B_liq working paper v0.3 directly into v0.5**. **Skip v0.4**; do not treat any intermediate v0.4 draft as the baseline. Use the attached **SPIRE_Bliq_Calibration_Final.pdf / .md** as the authoritative standalone liquidation-recovery calibration and the accompanying research files as supporting evidence. You have access to the main paper v0.3, transaction documentation, code, and prior FVA/CVA discussions. Preserve validated v0.3 mechanics and notation unless an identified error or a clearer formulation warrants a change. Do not assume the standalone calibration supersedes transaction-specific contractual terms or verified engine behavior.

**Target deliverable:** one conceptually clean, technically defensible, production-*oriented* working paper for senior quants and desk readers, titled **B_liq — Transaction Mechanics, Valuation and CVA Implementation (v0.5)** (adjust to the established main-paper naming convention). It must be readable end-to-end without consulting the calibration paper, while using concise cross-references to its appendices and source package. The document is not to claim production approval until all verification gates are demonstrably closed.

## 1. Organize around the reader's decision path

Open with a one-page executive conclusion: transaction purpose, payoff/close-out mechanism, precise definition of B_liq, why B_liq matters for CVA, proposed central calibration, key limitations and unresolved production gates. Then build the argument in the following order (adjust subsection titles to avoid duplication with existing v0.3):

1. **Transaction and contractual mechanics:** issuer, note, French OAT€i collateral, swap counterparties and economic flows; trigger, close-out, collateral disposal and cash waterfall. Distinguish verified executed terms, Base Prospectus generic provisions and research assumptions. Do not assert the Series-specific 30 Reference Business Day election without executed evidence. Explain whether sale is mandatory/discretionary only as far as the actual documentation supports.
2. **Economic quantities and model boundary:** define close-out amount/exposure, indexed collateral face F(t)=N×IR(t), net *accessible* disposal proceeds B_liq(t,k), shortfall L(t,k)=max(V_closeout(t,k)−B_liq(t,k),0), and the relationship to the existing CVA calculation. State sign conventions, currency, event time, discounting and what is outside scope. Do not conflate liquidation proceeds, defaulted bond market price, ultimate restructuring recovery, contractual claim, or bond MV.
3. **Valuation methodology and implementation mapping:** show the minimum equations required to map the legal payoff to the existing XVA engine, including the established exposure grid and payoff recovery configuration where verified. Preserve validated code-level time grid, numeraire/discounting, weights and close-out/event ordering; do not redesign the engine for this paper. Confirm each code mapping from source rather than extrapolating from another trade. Do not introduce MPOR unless contractual or implementation evidence makes it relevant.
4. **B_liq calibration:** concise integration of the final standalone calibration, including target estimand, evidence hierarchy, French transfer limits, scenario distribution and choice of central/downside/upside modes. The full empirical study belongs in the appendix/supporting calibration paper, not duplicated in the main narrative.
5. **Numerical illustration and CVA consequences:** use the same stated hypothetical inputs, ensure exact arithmetic and statewise positive-part losses; distinguish scenario-conditional expected shortfall from full CVA (which additionally requires default probabilities, time dependence and discounting). Present scenario sensitivities as *judgmental alternatives*, never statistical confidence intervals.
6. **Verification, controls and readiness:** a short, explicit table separating verified facts, model conventions, desk assumptions, unresolved legal terms, validation tests and sign-off owner. Include a realistic checklist for moving the working paper to production, without suggesting missing approvals are complete.

## 2. Calibration inputs to integrate exactly

Recovery is the fraction of **indexed face**, not of pre-default market value: F=N×IR and B_liq(k)=F×R_k, after the defined accessibility/execution convention. The four states are no accessible cash in the contractual window; severe liquidation; distressed orderly sale; resilient sale. Do not apply an additional generic liquidation haircut or collateral LGD to these already net/reduced-form recoveries.

| Mode | Scenario weights (in state order) | Accessible recovery fractions (same order) | Mean recovery |
|---|---|---|---|
| Conservative | 25%, 35%, 30%, 10% | 0%, 15%, 30%, 55% | 19.75% |
| **Balanced (central)** | **10%, 25%, 45%, 20%** | **0%, 20%, 40%, 65%** | **36.00%** |
| Aggressive | 5%, 15%, 40%, 40% | 0%, 25%, 50%, 75% | 53.75% |

The central mean decomposes into 10% no-access probability and 40% recovery *conditional on access*: 0.90×0.40=0.36. These parameters are **expert judgment informed by empirical stress anchors**, not an econometrically estimated France distribution. The no-access state is zero *timely accessible cash*, **not** a prediction of zero ultimate legal recovery. The 36% scalar is a summary, **not** a substitute for pathwise/statewise loss aggregation.

Example (illustrative, not verified trade economics): N=EUR 51m, IR=1.3195, F=EUR 67.2945m, V_closeout=EUR 19.62m. Break-even R≈29.16%. Balanced conditional expected loss ≈EUR 3.502m; Conservative ≈EUR 8.239m; Aggressive ≈EUR 1.400m. Verify numerical precision and use one consistent rounding policy.

Use only necessary high-level calibration evidence in the main paper: 22 distinct Bloomberg BVAL ISINs across seven sovereign event clusters, heterogeneous research-use tiers, missing-not-at-random quote availability, indicative not executable bids; restructuring NPV recovery database measures eventual exchange outcomes rather than disposal cash; debt/GDP correlations reverse sign under event selection, preventing a fitted French macro adjustment. The full selection register, country/event crosswalk, diagnostics, provenance and caveats remain in the **standalone calibration paper and supporting package**.

## 3. Technical rules and important traps

- **Never** use max(V−E[B_liq],0) in place of E[max(V−B_liq,0)] when statewise shortfalls are required. Do not multiply the resulting shortfall by another 100% LGD or re-haircut recovered proceeds.
- Respect default-conditioned quantities versus unconditional CVA. Do not equate the illustrative expected shortfall with final CVA.
- The 30 TARGET-business-day research window is a convention until the executed Series terms confirm the actual **Reference Business Day** calendar, trigger date and election. Do not silently equate TARGET days with Reference Business Days.
- The inflation-indexed denominator is a first-order modeling convention. Verify enforceability of the indexed claim, index discontinuity, coupon/accrued components and any principal floor on early termination/default. Do not automatically import the maturity redemption floor.
- BVAL bids are indicative. No firm execution, market depth or no-access probability is empirically observed in these exports. Do not fit or claim a precise French-specific regression forecast from seven events.
- Do not silently import disposal costs, liquidation fees or terms from a different SPIRE series. Trace all such amounts to executed documents.
- Distinguish collateral disposal, settlement and waterfall cash timing from the bond's eventual restructuring recovery.
- Do not discuss or benchmark against any legacy desk marking convention. This is a methodology paper, not a comparison against an incumbent mark.

## 4. Writing, structure and visuals

Write for senior quant and desk readers: **simple, not simplistic**. Each section should answer one question; avoid duplicate explanations and overly detailed mechanics where a reference to validated engine behavior suffices. Use consistent definitions and symbols from the main v0.3 paper wherever possible, introducing new notation only when it resolves ambiguity. Prefer compact tables and a small number of genuinely useful graphics:

- A clean transaction/cash-flow diagram, distinguishing collateral, swap and note flows, only if supported by documents.
- A short trigger → disposal window → accessible proceeds → shortfall timeline, with the contractual uncertainty visibly labeled.
- One scenario table and, optionally, a chart of statewise loss or scenario sensitivities.

No ornamental plots, dense regression pages or repeated appendices in the main narrative. Put security-level evidence, data cleaning, selection rationale, detailed stress diagnostics and source provenance in appendices or in the separate calibration supporting package. Preserve legibility: avoid clipped titles, microscopic labels and tables extending beyond page margins.

## 5. Outputs and acceptance criteria

Deliver: (i) **B_liq v0.5** in editable source and polished PDF; (ii) a short **v0.3→v0.5 change log**, noting preserved sections, replaced claims and why; (iii) a **verification/open-items register** with evidence links and named owners/required sign-offs where known; (iv) a cross-reference to the final standalone calibration and its supporting package, without republishing its entire appendices.

Before delivery, self-review for contractual provenance, formula consistency, scenario arithmetic, indexed-face denominator, default conditioning, double counting, engine-code mapping, and figure/table rendering. Any unsupported production assertion must be downgraded to an explicit assumption or open verification gate. **Production-ready working-paper quality** means complete enough for review and implementation planning; it does not mean the model has been production-approved.

Do not produce a v0.4 intermediary. Start from v0.3 and deliver v0.5 directly.
