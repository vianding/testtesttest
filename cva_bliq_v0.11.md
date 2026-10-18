# Agent B — Bliq v0.11: Final Methodology Consolidation and Modeling Recommendation

## 0. Mandate

Revise the existing **Bliq v0.10 — Liquidation Value Methodology and CVA Loss Interface** into **v0.11**, a standalone, technically rigorous, readable working paper suitable for senior quant, trading and model-risk review.

This is a substantive methodology consolidation, not a formatting exercise or an instruction to append more research.

The paper must answer four questions convincingly:

1. **What exactly are we valuing?** The collateral realization amount relevant to the Dealer's loss following a hypothetical French sovereign credit event.
2. **What determines that amount?** The underlying legal claim, distressed market price, restructuring expectations, SPIRE disposal mechanics, execution conditions and applicable waterfall.
3. **What model should we actually use?** A parsimonious, operationally usable recovery representation that preserves the nonlinear economics relevant to CVA.
4. **Why is the recommendation defensible?** The contractual evidence, historical observations, France-specific applicability analysis, mathematical properties, limitations and sensitivity tests must form a coherent chain of reasoning.

The final result should be **simple, not simpler**. Remove complexity that does not improve the decision, but preserve distinctions essential to the valuation.

Use v0.10 as the manuscript baseline. Review its actual contents before editing. Preserve correct analysis, equations, verified evidence and useful conclusions; do not rebuild the paper unnecessarily.

The latest historical CSVs and metadata register supersede earlier Bloomberg narrative summaries where they conflict.

---

# Part I — The conclusions the paper should reach

## 1. Recommended valuation object

The target is not:

- The performing OAT€i mark.
- The sovereign's eventual restructuring recovery in isolation.
- A generic sovereign LGD.
- A CDS auction recovery.
- The lowest observed historical bond bid.
- A forecast of French default probability.

The target is the **event-date economic value of collateral resources that can be realized and credited to the Dealer under the applicable SPIRE contractual arrangements**.

Preserve the distinction:

\[
B_{\tau^-}^{perf}
\longrightarrow
B_{\tau}^{def/restr}
\longrightarrow
B_{\tau}^{liq}
\longrightarrow
B_{\tau}^{avail}.
\]

Explain each transition succinctly:

- Performing market value before the event.
- Distressed claim or restructuring entitlement after the event.
- Economic realization under the disposal mechanism.
- Resources ultimately credited to the Dealer after applicable priority and collateral accounting.

Do not create four independent pricing models. This is a conceptual decomposition of one recovery problem.

For the simplified recovery interface, retain:

\[
F_\tau=N_\tau^{pool}IR_{\tau^-},
\]

\[
B_\tau^{liq}(k)=F_\tau R_\tau^{liq}(k),
\]

subject to the documented normalization, execution and contractual assumptions.

Use the existing v0.10 definitions consistently. In particular, distinguish gross liquidation proceeds, net liquidation proceeds and Dealer-available resources if the contractual waterfall makes them different.

The indexed-face denominator is a provisional economic convention, not a verified legal determination of the default claim. Preserve the OAT€i-specific qualifications concerning indexation, accrued amounts and restructuring treatment.

## 2. Recommended model architecture: one trader input, governed internal economics

The recommended direction is:

**M1 — One trader-facing effective recovery parameter, with a centrally governed internal recovery distribution where needed to reproduce nonlinear CVA loss.**

The objective is to avoid asking traders to maintain six or more independently marked scenario probabilities and recoveries.

However, do not simply replace the scenario distribution with its arithmetic mean.

The model must distinguish three quantities:

**A. Expected recovery**

\[
\bar R=\sum_k w_kR_k.
\]

This is a descriptive statistic. It is not generally loss-equivalent.

**B. Scenario-based conditional expected loss**

For a fixed event-date exposure and indexed face:

\[
\mathcal L(V,F)
=
\sum_k w_k[V-FR_k]^+.
\]

More generally, allow state-dependent exposure, face, collateral credit and waterfall resources where required by the companion CVA methodology.

**C. Loss-equivalent scalar recovery**

For a fixed positive \(V\) and \(F\), define a scalar that reproduces the same conditional expected loss:

\[
[V-FR_{\mathrm{LE}}]^+
=
\mathcal L(V,F).
\]

Where the solution lies in the positive-loss region:

\[
R_{\mathrm{LE}}
=
\frac{V-\mathcal L(V,F)}{F}.
\]

This quantity is **exposure-dependent** and should not automatically be treated as a universal sovereign recovery mark.

The old v0.8 illustration produced approximately:

- Mean recovery: 36%.
- Expected default-conditioned Dealer loss: €3.502275m.
- Loss-equivalent scalar recovery: 23.95%.

These figures are useful to demonstrate the distinction but are **not** approved v0.11 calibration results.

Recalculate all illustrative values using v0.10's current assumptions.

### The important design question

Define precisely what the proposed trader-facing \(R_{\mathrm{eff}}\) means.

Do not use the same symbol interchangeably for:

- Mean physical recovery.
- Risk-neutral expected recovery.
- A scenario-distribution control parameter.
- A deterministic recovery applied directly to the payoff.
- A loss-equivalent recovery inferred for a particular exposure.

Evaluate two implementations:

**M0 — Deterministic scalar**

\[
B_\tau^{liq}=F_\tau R_{\mathrm{eff}}.
\]

This is operationally simple but may misrepresent the positive-part loss near the collateral coverage threshold.

**M1 — Single trader-facing input with centrally governed distribution**

Define a transparent mapping:

\[
R_{\mathrm{eff}}
\longmapsto
\{w_k(R_{\mathrm{eff}}),R_k(R_{\mathrm{eff}})\}.
\]

The distribution should reproduce the intended recovery severity and remain coherent under trader adjustments.

Prefer the simplest defensible mapping. A fixed central distribution with a single shift or scale parameter is a possible design, but not automatically the best one.

Test any proposed mapping for:

- Monotonicity: improving the trader's recovery input should not increase expected loss, holding other inputs fixed.
- Recovery bounds and probability normalization.
- Stability across different exposure levels.
- Behavior around the pathwise break-even threshold.
- Whether it can reproduce the current calibration without introducing hidden degrees of freedom.
- Whether state-dependent close-out or indexation invalidates a simple scalar representation.

A mean-preserving distribution shift, for example, should not be described as preserving expected loss.

**Recommendation:** Favor M1 if the nonlinear effect is material across the relevant exposure profiles. Permit M0 as an explicitly validated simplification when the loss discrepancy is immaterial.

Do not recommend M1 solely because it is more sophisticated.

The final paper should make a clear recommendation rather than leave M0 and M1 as equally weighted alternatives.

---

# Part II — The economic and contractual rationale

## 3. SPIRE disposal mechanics and restructuring timing

The contractual liquidation process is central to the valuation target.

Use the applicable SPIRE 2025 Base Prospectus and transaction-specific evidence.

The main text should summarize:

- The relevant trigger and liquidation commencement process.
- The Series' stated 30 Reference Business Day period, while identifying any unresolved starting-date or calendar election.
- Condition 13(b): orderly disposal and the residual-sale obligation, subject to the contractual exceptions.
- Condition 13(k): possible non-disposal where sale is illegal, prohibited or impossible, subject to the relevant conditions.
- The distinction between a contractual right to dispose, practical market execution, and the Dealer's entitlement to proceeds.

Do not interpret the 30-business-day provision as permission to wait until sovereign restructuring is completed.

Likewise, do not assume a guaranteed sale by the deadline.

**Key modeling conclusion:** Restructuring timing affects the distressed market price and the available liquidation opportunities, but the model should not automatically require a separate stochastic restructuring-timing process.

Distinguish:

1. Restructuring information available before disposal.
2. Disposal while terms remain uncertain.
3. Exceptional legal or operational inability to dispose.

The first two are primarily price-severity mechanisms. The third is an accessibility and contractual-rights question.

A failure to sell within 30 business days is not automatically equivalent to zero event-date economic recovery if subsequent proceeds remain contractually accessible and economically attributable to the Dealer.

Conversely, do not discount future receipts into Bliq unless the contractual and valuation framework supports doing so.

Clarify the relevant realization horizon and whether delayed proceeds can be credited against the close-out claim.

Keep detailed clause references, notice procedures and unresolved document questions in the contractual appendix.

## 4. Central recovery states versus exceptional non-realization

Do not retain the old four-state design or its 10% zero-recovery weight merely because they appeared in an earlier version.

Equally, do not eliminate the non-realization risk simply to simplify the model.

Evaluate a central distribution of **positive-recovery sale-severity states**:

- Severe distressed execution.
- Distressed orderly execution.
- Relatively resilient execution.

These should describe economically distinguishable liquidation outcomes rather than arbitrary numerical buckets.

For each state, identify the principal drivers:

- Expected sovereign claim impairment.
- Restructuring uncertainty and timing.
- Market depth and available buyers.
- Legal transferability and settlement conditions.
- Block size and urgency.
- OAT€i-specific investor demand and indexation uncertainty.

Avoid double counting the same factor as both a recovery reduction and an execution haircut.

**Preferred direction:** Use positive-recovery severity states for the central calibration, and separately report exceptional non-realization or inaccessible-proceeds stress until its central probability and economic treatment can be justified.

If v0.10 already contains a better-supported state construction, assess it fairly rather than replacing it automatically.

The key principle is that state definitions, recovery levels and probabilities must be economically coherent and independently explainable.

---

# Part III — Historical evidence and applicability to France

## 5. Update the historical evidence using the latest authoritative files

Use:

- `bliq_calibration4_histDefaultWindowBondPrice.csv`
- `bliq_calibration10a_histData(2).csv`
- `bliq_calibration10b_histData(2).csv`
- `bliq_calibration10c_histData(2).csv`
- `bliq_calibration10z_histDataSummary.csv`

The four price-history CSVs contain confirmed Series 1 legacy-bond observations.

Use the summary register for the latest event metadata, instrument classifications and exclusion decisions.

Preserve the distinction between:

- Historical credit event.
- Research-selected observation anchor \(T_0\).
- Restructuring announcement.
- Restructuring settlement.

In particular, distinguish the Ukraine 2015 August price anchor from its October ISDA determination, and the Ukraine 2022 distress/default-related window from the 2024 restructuring settlement.

Do not present a trough-selected window as an unbiased post-default execution window.

Recompute descriptive statistics from the raw observations.

Use bond-level and event-level aggregation correctly, document missing data and avoid treating bonds or daily observations within one sovereign event as independent crisis samples.

The historical evidence should establish **a plausible severity range and price behavior under stress**, not a statistically fitted French recovery distribution.

## 6. France applicability: two different meanings of historical similarity

Add a clearly articulated section titled approximately:

**Applicability of Historical Sovereign Episodes to a French OAT€i Default**

The section should address an important conceptual issue:

**The historically closest sovereign crisis is not necessarily the most useful numerical liquidation-price observation.**

Separate two dimensions of comparability.

### A. Institutional and structural similarity

Consider:

- Eurozone membership and monetary arrangements.
- Domestic versus foreign governing law.
- CAC and restructuring mechanisms.
- Sovereign market infrastructure.
- Central-bank and official-sector involvement.
- Legal transferability and settlement.
- Inflation-linked versus nominal bond structure.

Greece 2012 is an especially relevant eurozone institutional precedent, but its restructuring design and historical circumstances differ materially from France.

Do not imply that Greece's recovery price can be directly transplanted to a French OAT€i.

### B. Empirical liquidation-price relevance

Consider:

- Whether the observation is genuinely a legacy-bond price.
- Proximity to the chosen default or liquidation anchor.
- Observation coverage and quote reliability.
- Price basis and instrument characteristics.
- Market liquidity and distressed execution conditions.
- Restructuring timing and information available at the observation date.

Greece's sparse observations may be less useful for numerical calibration than better-covered non-eurozone episodes.

Other sovereign crises remain relevant as evidence of the range and mechanisms of distressed trading.

### Recommended treatment

Retain all seven historical episodes, but do not pretend they form a homogeneous sample.

In the main paper, explain the two-dimensional distinction and provide a compact qualitative comparison.

In the appendix, provide the full event-by-event assessment and a transparent comparability matrix.

If useful, define qualitative or ordinal similarity categories. A numerical distance metric may be introduced only if its features, scaling and weights have a defensible interpretation.

Do not manufacture precision by assigning arbitrary mathematical distances or deriving recovery probabilities from them.

Test whether the calibration narrative is robust when emphasizing Greece, excluding Greece, or considering the full sample. Such comparisons are sensitivities, not formal estimators.

## 7. Separate the French macro backdrop from default-conditioned recovery

Incorporate the latest France-specific fiscal, political, sovereign-spread and market-liquidity context from the accompanying research where supported by sources.

However, clearly separate:

- Probability that France experiences a sovereign credit event.
- Severity of the claim impairment conditional on such an event.
- Market liquidity and execution severity conditional on the event.
- SPIRE-specific contractual realization and Dealer loss.

This working paper concerns primarily the last three, not estimation of the first.

Current French sovereign conditions may inform plausible scenarios, but must not be converted into numerical default-conditioned recovery weights without a defensible model.

Distinguish ordinary sovereign spread widening, fiscal stress, restructuring, redenomination and outright default.

Do not imply that all such outcomes are interchangeable.

A French OAT€i has specific indexation, maturity, governing-law and investor-base characteristics. Explain the important differences from nominal Greek sovereign bonds.

Keep the macro discussion concise in the main text and place detailed evidence and scenario transfer reasoning in the appendix.

---

# Part IV — Calibration: what can and cannot be concluded

## 8. Explain recovery numbers and weights separately

The paper must make an explicit distinction between two calibration problems.

### Recovery severity

Historical default-window prices, restructuring outcomes and distressed-market mechanics can provide empirical support for the **range and relative ordering** of scenario recovery levels.

The observed evidence does not automatically determine the executable value of a €51m nominal French OAT€i position.

### Scenario probabilities

Seven historical sovereign events do not provide statistically credible risk-neutral probabilities for hypothetical French liquidation regimes.

The selected weights remain model judgments or governance assumptions unless an independent market calibration is demonstrated.

For every selected state, the appendix must explain:

- Why the state exists.
- Why its recovery level is plausible.
- Which observations are relevant.
- How those observations are transferred to France.
- Why the assigned probability is chosen.
- Which part is empirical and which part is judgmental.
- What would justify revising the assumption.

Do not claim the probabilities are statistically calibrated if they are not.

Do not preserve an earlier weighted mean as a target merely for continuity.

Retain Conservative/Balanced/Aggressive sensitivity sets if useful, but distinguish parameter uncertainty and policy stress from statistical confidence intervals.

## 9. Avoid unsupported execution overlays

The historical Bloomberg BVAL data are indicative evaluated bids.

They are not:

- Firm executable quotes.
- Actual €51m block transactions.
- A measured market-impact function.
- A guaranteed liquidation floor.

Do not apply an automatic 2–5-price-point deduction without independent evidence.

If execution concessions are modeled, explain whether they are already embedded in state recovery levels or applied separately.

Use a clean accounting boundary:

\[
\text{Distressed claim value}
\rightarrow
\text{execution proceeds}
\rightarrow
\text{net Dealer-available resources}.
\]

Each cost or haircut should appear only once.

Keep the numerical execution sensitivities in the appendix unless they materially change the central recommendation.

---

# Part V — Quantitative validation and CVA interface

## 10. Demonstrate the nonlinearity and operational trade-off

This is a core part of the recommendation and belongs in the main text.

For the current illustrative transaction, retain the useful reference values where still applicable:

- Base pool nominal: €51m.
- Illustrative index ratio: 1.3195.
- Indexed face: €67.2945m.
- Illustrative Dealer close-out: €19.62m.
- Corresponding break-even recovery: approximately 29.16%.

Reconcile all inputs with v0.10 and the companion CVA paper before using them.

Show how the positive-part loss behaves around the break-even recovery.

Explain why:

\[
\mathbb E[(V-FR)^+]
\ne
[V-F\mathbb E(R)]^+
\]

in general.

Then compare M0 and M1 using the same calibrated economic assumptions.

Provide a compact quantitative comparison showing:

- Central conditional expected loss.
- Deterministic-mean-recovery loss.
- Loss-equivalent recovery for the illustrative exposure.
- Error introduced by M0.
- Whether the error remains material across plausible exposure-to-collateral ratios.

The test should not be restricted to a single favorable or unfavorable exposure point.

Where the companion CVA model generates stochastic or state-dependent exposures, test the simplified recovery model against the relevant exposure distribution or representative exposure profiles.

Do not imply that a scalar calibrated to one trade's expected loss will remain loss-equivalent for every trade, date or exposure.

## 11. Preserve the CVA accounting boundary

Use the companion CVA paper's actual notation for Dealer close-out and credited collateral.

The general loss interface is:

\[
L_\tau^{Dealer}(k)
=
[V_\tau^{co}(k)-B_\tau^{avail}(k)]^+,
\]

with any necessary treatment of posted collateral, prior credits, waterfall priority and state-dependent exposure.

Retain the distinction between the entire collateral pool and the portion posted under a CSA.

Do not automatically treat the posted nominal as the maximum collateral recovery available to the Dealer.

Do not double count collateral credit or add a second LGD to a loss already calculated net of available resources.

Do not redesign the swap close-out, CSA mechanics, FVA model, exposure observation grid or broader XVA engine.

The paper should specify the Bliq input and its modeling implications, not attempt to rebuild the entire valuation infrastructure.

---

# Part VI — Required document structure

## 12. Recommended main-paper organization

Use the following structure as the editorial target. Adjust headings if v0.10 already has a cleaner organization, but preserve the logical flow.

### Executive conclusion — approximately one page

Answer immediately:

- What is the valuation object?
- What model is recommended?
- Why is it preferable to both a naive scalar and six independent trader marks?
- What historical evidence supports it?
- What remains judgmental or unverified?
- What is required before production approval?

Include one compact recommendation table if helpful.

Avoid opening with a long history of the research.

### 1. Problem definition and contractual economics

Explain the SPIRE transaction, relevant collateral and Dealer loss mechanism.

Define Bliq and distinguish it from the performing bond price, defaulted claim market value and eventual restructuring recovery.

Introduce only the necessary equations.

### 2. Evidence and applicability to France

Summarize the seven historical episodes, the observed recovery range and the limits of the data.

Explain why Greece is structurally relevant but not automatically the best numerical anchor.

Discuss French OAT€i-specific differences and restructuring timing.

### 3. Model alternatives and recommendation

Present M0 and M1.

Explain the positive-part nonlinearity, trader-marking constraints and why the selected architecture is appropriate.

Define the effective recovery parameter precisely.

### 4. Calibration and quantitative illustration

Explain the selected severity states and central distribution at a high level.

Present the numerical loss illustration and M0-versus-M1 comparison.

Do not bury the modeling decision under extensive historical tables.

### 5. Implementation boundary and validation requirements

State the trader-facing interface, central calibration governance, sensitivities, remaining legal verification and conditions for accepting a simpler deterministic model.

Finish with a concise final recommendation.

The main paper should be readable sequentially without requiring the reader to inspect appendices to understand the conclusion.

## 13. Recommended appendix organization

### Appendix A — Contractual and transaction verification

Include:

- SPIRE 2025 Conditions and clause references.
- Series-specific 30 Reference Business Day wording and unresolved trigger/calendar details.
- Disposal obligations and exceptions.
- Unsold-collateral treatment.
- OAT€i claim/indexation issues.
- Posted collateral, Dealer waterfall and senior costs.
- Verification status and outstanding executed documents.

### Appendix B — Historical data and provenance

Include:

- Latest source-file inventory.
- Instrument register and classification.
- Event dates and alternative observation anchors.
- Data coverage and missingness.
- Legacy-bond bid statistics.
- Clean/flat quotation issues.
- Exclusions and source corrections.

Keep raw daily records in supporting CSVs rather than reproducing them in the PDF.

### Appendix C — Historical applicability to France

Include:

- Seven-event comparison matrix.
- Institutional/legal similarity.
- Empirical price-data comparability.
- France-specific transfer limitations.
- Greece-only, ex-Greece and full-sample sensitivities where informative.
- Why a numerical similarity score or probability-weighting scheme is not justified without stronger evidence.

### Appendix D — Calibration rationale

Include:

- State definitions.
- Recovery severity evidence.
- Selected recovery values.
- Weight rationale and limitations.
- Execution concessions and possible double counting.
- Restructuring-timing effects.
- Exceptional non-realization stress.
- Conservative and Aggressive sensitivities.

Provide an explicit evidence-to-parameter table.

### Appendix E — Mathematical and numerical validation

Include:

- Statewise expected-loss equations.
- Derivation of the loss-equivalent scalar.
- Conditions under which deterministic recovery is equivalent or approximately equivalent.
- M0-versus-M1 sensitivity across exposure levels.
- Effective-recovery parameterization.
- Monotonicity and bound checks.
- State-dependent exposure qualifications.

### Appendix F — Source and model governance register

Keep only material references and verification gates.

Clearly distinguish:

- Verified contractual statements.
- Observed historical market data.
- External economic research.
- Model assumptions.
- Expert judgments.
- Open issues.

---

# Part VII — Material to remove, consolidate or demote

## 14. Remove outdated or misleading material

Review v0.10 and remove or correct the following wherever present:

**Obsolete data**

- Earlier historical statistics superseded by the latest CSVs.
- Misclassified replacement-bond observations in legacy-bond calculations.
- Incorrect or conflated Ukraine event dates.
- Unqualified equal-weight event averages used as French recovery estimates.
- Unreconciled historical price, haircut and package-value comparisons.

**Unsupported calibration**

- Any implication that seven events statistically estimate French risk-neutral probabilities.
- Any unsupported numerical French macro uplift.
- An arbitrary 10% zero-recovery probability presented as empirically calibrated.
- Automatic 2–5-point execution deductions without evidence.
- An assumption that the historical mean is a production recovery parameter.

**Redundant methodology**

- Repeated explanations of Bliq versus performing market value.
- Multiple restatements of the same legal uncertainty.
- Excessive alternative recovery-denominator discussions that do not affect the decision.
- Generic sovereign credit-model tutorials.
- Unnecessary macro regressions, especially unstable debt/GDP correlations.
- Extensive discussion of full stochastic bond dynamics unrelated to the proposed Bliq input.
- Repeated demonstrations of the same positive-part nonlinearity.

**Unnecessary implementation detail**

- Low-level code, time-grid and numeraire discussions belonging to the companion XVA methodology.
- Reimplementation of the CSA or close-out engine.
- Excessive mathematical machinery that does not change the model recommendation.

Move useful supporting details to appendices; remove material that adds neither evidence nor decision value.

Do not delete a qualification if removing it would make a contractual or quantitative conclusion misleading.

---

# Part VIII — Quality and acceptance criteria

## 15. Technical quality

Before finalizing:

1. Recompute all historical summary statistics directly from the latest files.
2. Verify every numerical illustration.
3. Reconcile notation with v0.10 and the companion CVA methodology.
4. Ensure scenario weights sum to one and recovery levels remain within justified bounds.
5. Check that all scalar-equivalence claims specify their exposure assumptions.
6. Verify that historical physical observations are not mislabeled as risk-neutral probabilities.
7. Confirm that delayed recovery and non-disposal are treated consistently with the valuation definition.
8. Ensure the model does not double count liquidation costs, collateral or Dealer recovery.
9. Separate observed evidence from assumptions and legal interpretation.
10. Preserve traceable references for material empirical and contractual claims.

## 16. Editorial quality

The paper should read like a single argument, not a collection of accumulated research notes.

Every section should contribute to the recommendation.

Use the following logic:

**Contractual economic problem → Historical evidence → Applicability to France → Model alternatives → Recommended architecture → Calibration rationale → Quantitative consequences → Implementation and validation.**

Prefer concise paragraphs, informative section headings and a small number of purposeful tables.

Introduce concepts before equations.

Explain what each important equation means economically.

Avoid unsupported precision, excessive caveats and defensive repetition.

Do not overload the executive conclusion with methodology details.

A senior reader should be able to understand the recommendation and its rationale from the executive conclusion and main text alone.

The appendices should make the work auditable without making the main paper cumbersome.

## 17. Deliverables

Produce:

**A. Complete v0.11 standalone PDF**

Title: **Bliq — Liquidation Value Methodology and CVA Loss Interface**

Subtitle may identify the SPIRE French OAT€i transaction.

Mark as a working paper, not production approved.

**B. Concise revision log**

Summarize:

- What changed from v0.10.
- Which conclusions were retained.
- Which recommendations were modified.
- Which data or assumptions were superseded.
- Numerical consequences.
- Remaining issues requiring verification.

**C. Supporting calculations**

Retain or generate reproducible summary tables and model-comparison calculations where appropriate.

Do not modify the underlying historical observations.

## 18. Final decision standard

The final paper must not conclude merely that "more research is needed."

It should make the strongest modeling recommendation supported by the available evidence, clearly distinguish provisional calibration from validated production assumptions, and identify the limited set of issues that could materially change the recommendation.

The preferred direction is **one trader-facing effective recovery input with centrally governed recovery-state economics**, but this must survive the mathematical and numerical M0-versus-M1 comparison.

If the evidence shows that a deterministic scalar is sufficiently accurate for the relevant exposures, explain the conditions under which it is acceptable.

If the scenario distribution materially changes loss, preserve it internally without transferring unnecessary calibration complexity to traders.

**The desired result is not the most elaborate sovereign recovery model. It is the simplest defensible model for the actual SPIRE CVA decision, with a clear economic interpretation, sound mathematical behavior, credible supporting evidence and practical trader usability.**


## Amendment to Bliq v0.11 — Updated Numerical Illustration

Please update the v0.11 numerical illustration to use:

- Pool nominal: €51m
- Index ratio: 1.3195
- Indexed collateral face: €67.2945m
- **Present value of Dealer close-out: €24.3m**, replacing the previous €19.62m illustrative close-out.

The €24.3m is a revised illustrative input, not a newly verified transaction mark.

### Required updates

1. Recalculate the break-even recovery threshold:

   \[
   R^*=\frac{24.3}{67.2945}\approx36.11\%.
   \]

2. Recalculate all statewise losses, probability-weighted expected losses, deterministic-recovery comparisons and loss-equivalent recovery figures using the updated input and the final v0.11 recovery calibration.

3. Update the M0-versus-M1 comparison and any charts or sensitivity tables dependent on the close-out value.

4. Explain that the revised threshold falls within the central range of historical distressed recovery observations, making the positive-part loss nonlinearity particularly relevant to the model-selection decision.

5. Use €24.3m consistently throughout the executive summary, main text, appendices and numerical examples. Remove obsolete €19.62m figures unless explicitly retained as a labeled historical comparison.

### Valuation consistency

The supplied €24.3m is a **present value**. Ensure that the close-out and liquidation proceeds are expressed on the same valuation date and discounting basis before applying the positive-part loss formula.

If the existing methodology defines \(V_\tau^{co}\) at the default-event date rather than today, distinguish that event-date value from its present value. Do not substitute today's PV directly into an event-date loss formula without the appropriate valuation-date reconciliation.

Do not discount the same amount twice.

Keep the revised €24.3m illustration separate from any independently verified transaction exposure or production CVA calculation.
