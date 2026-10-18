Agent B — Bliq v0.12: Final Methodology Consolidation, XVA Implementation Alignment and Editorial Refinement

0. Assignment and objective

Revise the existing Bliq — Liquidation Value Methodology and CVA Loss Interface, version v0.11, into v0.12.

This is intended to be the final substantive methodology revision. A subsequent v0.13 should be necessary only for residual validation, documentary updates or editorial corrections.

Use v0.11 as the baseline. Preserve its substantive strengths. Do not rewrite the paper from scratch.

The objective is a standalone working paper that:

1. Makes a clear, defensible recommendation for modeling liquidation proceeds following a hypothetical French sovereign default.
2. Explains why that recommendation follows from the transaction mechanics, economic reasoning, historical evidence and XVA requirements.
3. Provides a simple, operationally practical trader interface.
4. Distinguishes mathematical conclusions, empirical evidence and expert judgment.
5. Keeps sufficient supporting evidence, assumptions, calculations and provenance for independent review.
6. Is coherent, concise and easy to follow.

Editorial principle: simple, not simpler.

The reader should understand the recommended methodology without first reading the appendices. The appendices should allow a quant or model reviewer to reconstruct, challenge and validate it.

Avoid expanding the research scope unless a genuine methodological gap requires it.

⸻

PART I — Decisions to incorporate into v0.12

1. Preserve the existing CVA model-family nomenclature

The companion CVA paper already uses M0 to identify the face-value-based Bliq modeling approach. It also uses M1 for a different CVA model family.

Therefore:

* Do not use M0 and M1 to distinguish deterministic and distributional recovery within Bliq.
* Do not introduce M0a/M0b unless an actual implementation requirement makes those labels necessary.
* Refer to the recommended methodology as the M0 indexed-face-based liquidation-recovery approach.
* Within that approach, distinguish a deterministic scalar calculation from a distributional recovery calculation using descriptive terminology, not new model-family names.

Make the terminology consistent throughout the executive conclusion, main text, formulas, appendices and tables.

This is a nomenclature correction, not a change in the underlying economic valuation object.

2. Recommended economic model: indexed-face-normalized liquidation recovery

Retain:

[
F_\tau=N_\tau^{pool}IR_{\tau^-}
]

[
B_\tau^{liq}(k)=F_\tau R_\tau^{liq}(k)
]

subject to the existing legal, contractual and valuation-basis qualifications.

The model’s object is economically realizable proceeds from the defaulted collateral claim, not the pre-default performing bond price and not ultimate sovereign restructuring recovery.

Make an important conceptual refinement:

Face value defines the recovery denominator; market and restructuring evidence inform the liquidation proceeds.

The paper should not claim that face-value recovery is inherently a more accurate valuation model than market-value recovery.

For the same liquidation proceeds, a properly calibrated face-value-normalized and market-value-normalized model are mathematically equivalent representations.

Our preference for indexed-face normalization follows from:

* A clear interpretation as recovery against the impaired principal claim.
* Relative stability of the economic recovery parameter against unrelated changes in performing bond marks.
* Compatibility with much of the historical distressed-bond quotation evidence.
* A manageable trader-facing issuer-recovery mark.
* Avoidance of mechanically scaling default proceeds with a potentially inappropriate performing-market-value reference.

Explain that a market-value-based representation is not wrong. The specific approach we do not recommend as the central model is:

[
B_\tau^{liq}=B_{\tau^-}^{perf}R^{MV}
]

with a fixed recovery fraction that mechanically inherits performing bond valuation movements without independent economic justification.

Retain the discussion of alternative normalizations, including market-value-based recovery, but condense the main-text comparison. Detailed normalization examples can remain in an appendix.

Inflation-linked bond qualification

The underlying instrument is the French OAT€i:

* ISIN: FR0013209871
* Maturity: July 2047
* Coupon: 0.10%

The SPIRE Series is:

* ISIN: XS3411888731

Do not assume without verification that the indexed face convention is necessarily identical to the legally recognized default claim under every restructuring or acceleration scenario.

Explicitly distinguish:

1. The selected model normalization.
2. The actual legal claim.
3. The expected liquidation realization.
4. The amount ultimately available to the Dealer after applicable transaction mechanics.

Keep the indexed-face convention as the recommended working basis, subject to transaction-specific verification.

⸻

3. One trader-facing recovery mark

The desk should maintain one economic recovery input, denoted:

[
R_{\mathrm{eff}}
]

for each relevant issuer or appropriately defined collateral-risk category.

It should not maintain three recoveries plus three weights.

The recovery distribution should be centrally governed.

For the v0.11 illustrative reference configuration:

Recovery state	Recovery	Weight
Severe distressed realization	20%	30%
Distressed orderly realization	40%	50%
Resilient realization	65%	20%

The reference mean is:

[
\bar R=0.30(20%)+0.50(40%)+0.20(65%)=39%.
]

At this reference configuration:

[
R_{\mathrm{eff}}=39%.
]

For a general trader mark, retain the existing additive mapping as the initial candidate:

[
R_k(R_{\mathrm{eff}})

R_k^0+(R_{\mathrm{eff}}-\bar R^0)
]

where (\bar R^0=39%).

This mapping:

* Preserves the reference state weights.
* Preserves absolute recovery-state spacing.
* Shifts the distribution mean to the trader’s mark.
* Is monotonic in the trader input.
* Is simple to implement and explain.

However, these properties do not prove that it is the uniquely correct economic mapping.

Explain the assumption: a change in the trader’s recovery view shifts the recovery severity of all states equally, without changing the probabilities of those states.

Provide a concise comparison against proportional recovery scaling and probability-weight adjustment in the appendix. Do not introduce a second trader-facing parameter merely to address this issue.

Distinguish mathematical bounds from economically permitted marking ranges.

The current 19–74% mathematical interval follows from the reference states remaining between zero and 100%. It should not automatically become the routine desk marking range.

Any narrower operational range requires governance and economic justification.

Multiple issuers

The implementation should be reusable across issuers:

[
R_{\mathrm{eff}}^{(i)}
\longrightarrow
{R_k^{(i)},w_k^{(i)}}.
]

One issuer-level trader mark is the intended operating interface.

However, do not assume the French sovereign recovery distribution is automatically appropriate for every issuer, currency, instrument or legal regime.

Issuer-class recovery shapes and exceptional instrument adjustments should be centrally configured where necessary.

Avoid expanding the paper into a general corporate and sovereign recovery-modeling treatise. A short implementation-design paragraph is sufficient.

⸻

PART II — Critical new XVA implementation information

4. Confirmed existing infrastructure

The existing XVA infrastructure supports:

A single deterministic scalar recovery input, independent of simulated market paths.

It has not been confirmed that the engine can directly consume a discrete recovery distribution within each path.

This fact must be reflected in the recommended implementation.

Do not write as though pathwise stochastic recovery is already supported or is required for the initial implementation.

Also do not assume, without code verification, that the scalar is applied at the correct point in the collateral liquidation and Dealer-loss calculation.

The precise recovery-input semantics remain an implementation verification item.

5. Preferred implementation: reuse deterministic recovery runs

Subject to verifying the engine’s recovery semantics, the preferred implementation is to reuse the existing deterministic-recovery engine for each centrally governed recovery state.

Calculate:

[
\boxed{
\mathrm{CVA}_{dist}

\sum_{k=1}^{K}w_k,\mathrm{CVA}(R_k)
}
]

For the reference three-state configuration:

[
\mathrm{CVA}_{dist}

0.30,\mathrm{CVA}(20%)
+
0.50,\mathrm{CVA}(40%)
+
0.20,\mathrm{CVA}(65%).
]

For a trader mark other than 39%, substitute the mapped state recoveries (R_k(R_{\mathrm{eff}})).

This approach:

* Requires only one trader-maintained mark.
* Reuses the existing scalar-recovery interface.
* Retains the positive-part loss nonlinearity.
* Does not require the engine to sample recovery states path by path.
* Provides a transparent reference calculation against which simpler approximations can be tested.

Conditions for validity

Do not claim that weighted deterministic CVA runs are universally exact.

The weighted-run identity is exact for the intended discrete-mixture model when:

1. Recovery-state weights are fixed for the relevant calculation and do not depend on simulated market paths or default time, unless the calculation explicitly conditions and aggregates them correctly.
2. Recovery states do not alter the market-path distribution, default-time distribution or other model inputs in ways omitted by the separate runs.
3. The engine applies recovery to the intended collateral-realization amount before the relevant positive-part Dealer loss is calculated.
4. Exposure, discounting, close-out conventions, netting and collateral accounting are consistent across runs.
5. The resulting CVA outputs are additive under the specified scenario mixture.

If the intended recovery distribution is conditional on default date, market state or other risk factors, the simple fixed-weight formula may not be sufficient.

State this limitation clearly but briefly.

Implementation verification checklist

Inspect the existing XVA source code or obtain confirmation of:

* The exact parameter receiving the recovery input.
* Whether the recovery applies to collateral proceeds, payoff recovery, counterparty LGD or another quantity.
* Whether the recovery enters before or after the positive-part exposure calculation.
* Whether recovery is applied once, and whether any other recovery or haircut is already embedded.
* Whether multiple scalar runs can be executed consistently and their CVA outputs aggregated.
* Whether the relevant exposure and close-out calculations are reused or recomputed consistently.
* Whether the existing engine’s default timing, exposure sampling and discounting remain unchanged.

Do not conflate collateral liquidation recovery with counterparty contractual recovery or LGD.

Do not recommend an engine modification until this verification establishes that it is needed.

If source-code verification cannot be completed, identify it as an open implementation gate rather than presenting an unverified architecture as operationally confirmed.

⸻

6. Loss-equivalent recovery: important, but not a universal shortcut

Introduce and explain:

[
R_{\mathrm{LE}}
]

as a loss-equivalent scalar recovery, distinct from the economic trader mark (R_{\mathrm{eff}}).

For fixed positive close-out (V), indexed face (F>0), and a recovery distribution:

[
L_{dist}(V,F)

\sum_k w_k[V-FR_k]^+
]

the scalar recovery reproducing that loss can be written as:

[
R_{\mathrm{LE}}(V,F)

\frac{V-L_{dist}(V,F)}{F}.
]

This formula selects a valid loss-equivalent scalar under the usual recovery bounds, with non-uniqueness possible in zero-loss regions.

The central distinction:

* (R_{\mathrm{eff}}) is the trader’s economic view and controls the mean of the governed recovery distribution.
* (R_{\mathrm{LE}}) is derived from the distribution to reproduce a specified loss calculation.
* (R_{\mathrm{LE}}) is generally exposure-dependent.

Do not present (R_{\mathrm{LE}}) as a universal issuer recovery mark.

Portfolio-CVA-equivalent scalar

For a full stochastic XVA calculation, a portfolio-equivalent scalar (R_{\mathrm{LE}}^{CVA}) may be defined implicitly by:

[
\mathrm{CVA}(R_{\mathrm{LE}}^{CVA})

\sum_k w_k\mathrm{CVA}(R_k).
]

This is generally a one-dimensional inversion of the deterministic-recovery CVA function, not a universal closed-form arithmetic conversion.

An exact solution may be non-unique, and numerical calibration requires checking the range, monotonicity and numerical stability of the CVA function.

The value can depend on:

* Portfolio composition.
* Exposure distribution.
* Default-time weighting.
* Indexed face and contractual cash-flow evolution.
* Close-out and collateral conventions.

Therefore:

Do not calibrate one production-wide scalar from the €24.3m static illustration and assume it reproduces portfolio CVA.

Recommended hierarchy

1. Reference methodology: governed distribution with weighted deterministic-recovery CVA runs.
2. Permitted simplification: direct scalar recovery where the difference from the distributional benchmark is immaterial.
3. Potential computational optimization: a calibrated portfolio-CVA-equivalent scalar where repeated runs are costly and the approximation or inversion is validated.

This is an implementation hierarchy, not three separate model families.

The main paper should explain it succinctly. Mathematical details and equivalence conditions belong in Appendix E.

⸻

PART III — Calibration and evidence

7. Preserve the historical-data work

Retain the corrected v0.11 historical evidence base:

* Seven sovereign crisis episodes.
* Nineteen legacy-bond instruments.
* Original legacy-bond price series, rather than replacement-bond prices.
* The updated data selection and coverage register.
* Explicit treatment of missing indicative bids.
* Distinction between event anchor, observation date and actual restructuring or settlement date.

The seven episodes remain:

1. Greece 2012.
2. Ecuador 2020.
3. Argentina 2020.
4. Zambia 2020.
5. Ghana 2022.
6. Ukraine 2015.
7. Ukraine 2022/2024.

Preserve the corrected Ukraine event chronology.

Do not revert to outdated event anchors or earlier, superseded price datasets.

Evidence interpretation

The Bloomberg indicative-price observations are market evidence, not verified executable liquidation prices.

The 0–30-business-day window is a descriptive observation window, not a proven SPIRE execution schedule.

A window mean does not establish an expected disposal price unless the execution-timing model justifies the corresponding averaging convention.

Do not forward-fill missing bids or treat missing observations as zero recovery.

Preserve the distinction between:

* Original distressed-bond quotations.
* Subsequent restructuring instruments.
* Ultimate restructuring recovery.
* Realized sale proceeds.

Detailed ISIN coverage, event chronology, alternative anchors and provenance belong in the appendices.

The main text should present only the evidence necessary to motivate the methodology.

8. Historical comparability to France

Retain the two-dimensional comparability framework:

Institutional similarity: legal framework, monetary regime, restructuring institutions, market structure and creditor treatment.

Numerical evidence relevance: instrument comparability, observation coverage, quote quality and proximity to a plausible liquidation window.

Greece remains the strongest eurozone institutional reference among the seven episodes, but its special PSI mechanics and sparse price observations prevent it from serving as a dominant numerical calibration anchor.

Other episodes provide broader distressed-price and market-functioning evidence, but are less directly comparable institutionally.

Avoid arbitrary statistical distance scores or unsupported quantitative France adjustment factors.

Do not interpret the seven historical episodes as a statistically representative sample of future French default outcomes.

France-specific transfer

Explain how the following may affect the transfer:

* Eurozone institutional framework.
* French sovereign debt-market depth.
* OAT€i inflation-linked claim characteristics.
* Domestic-law and restructuring considerations.
* Liquidity deterioration around a sovereign credit event.
* The SPIRE contractual disposal window and actual executable market conditions.

The main paper should summarize the implications, not reproduce a lengthy sovereign-crisis narrative.

Retain supporting detail in the comparability appendix.

⸻

9. Strengthen the historical-price-to-OAT€i-recovery bridge

This is a substantive improvement required in v0.12.

Historical distressed nominal sovereign bond prices quoted per 100 original face are not automatically identical to recovery percentages of the indexed face of the French OAT€i.

Explicitly identify differences arising from:

* Inflation indexation.
* Accrued coupon or other claims.
* Legal principal and acceleration treatment.
* Coupon, maturity and instrument characteristics.
* Clean versus dirty price conventions.
* Governing law and restructuring treatment.
* Indicative quote versus executable sale price.

Explain why historical prices are useful severity anchors despite these differences.

Do not present the historical percentages as directly estimated French OAT€i recoveries.

A compact bridge table in the appendix is appropriate:

Historical observation → relevant economic information → required normalization or caveat → use in the French model.

Avoid inventing numerical conversion factors unsupported by the data.

⸻

10. Recovery states and weights

Retain the v0.11 reference configuration provisionally:

* Recoveries: 20%, 40%, 65%.
* Weights: 30%, 50%, 20%.
* Mean: 39%.

Do not automatically replace the parameters or reopen the entire calibration merely because the historical mean differs slightly.

However, improve the reasoning.

Recovery levels

Explain the economic interpretation of each state:

20% — Severe distressed realization: substantial market impairment, constrained liquidity or strongly adverse execution conditions.

40% — Distressed orderly realization: a functioning but deeply distressed market permitting economically meaningful realization.

65% — Resilient realization: a comparatively favorable realization regime, without assuming a full recovery or uninterrupted market functioning.

Tie the broad severity range to historical observations while distinguishing the observed evidence from French-specific judgment.

Weights

The 30/50/20 weights are not empirically identified French probabilities.

Do not claim that plausible narratives uniquely justify those precise percentages.

State that they are a provisional, centrally governed reference mixture used to represent material dispersion in liquidation outcomes.

Explain why the chosen configuration is reasonable as a working model, while acknowledging alternative configurations.

Preserve the weight sensitivities and show their effect on expected loss.

In particular, retain the important conclusion that the severe-state weight can materially change the calculated loss.

Do not describe the 39% mean as a statistically calibrated estimate derived from the approximately 39.6% historical all-event average.

Their numerical proximity is at most a plausibility cross-check.

Probability interpretation

Clarify the distinction between:

* Historical frequency.
* Subjective scenario weighting.
* Pricing-measure conditional recovery distribution.

If the weights are used directly in risk-neutral CVA, they must be justified as a modeling assumption under the relevant pricing framework, not misrepresented as empirically established risk-neutral probabilities.

The paper should be transparent about this limitation.

Exceptional non-realization

Retain non-realization or zero-credit stress separately unless the contractual rights and probability treatment justify incorporating it into the central distribution.

Distinguish:

* Temporary delay in disposal.
* Inability to execute during the prescribed window.
* Permanent inability to realize or obtain contractual credit.
* Legal extinguishment of an unpaid residual.

These are not necessarily equivalent economic outcomes.

The 5% and 10% mixtures in the existing appendix should remain clearly labeled hypothetical sensitivities, not estimated probabilities.

⸻

PART IV — Recovery and exposure dependence

11. State the baseline dependence assumption

The current proposed distribution treats recovery severity independently of the existing XVA market paths, with fixed weights.

This assumption should be explicit.

A French sovereign stress event may simultaneously affect:

* Collateral liquidation recovery.
* Rates and inflation markets.
* Swap close-out exposure.
* Liquidity and execution.
* Default timing and market access.

Therefore, recovery and close-out exposure need not be independent economically.

However, do not introduce a full joint sovereign-default and bond-price dynamics model into Bliq v0.12.

That would expand the scope substantially and is not justified by the current implementation objective.

Instead:

1. State the baseline independence or fixed-mixture assumption.
2. Explain what dependence is omitted.
3. Identify conditions under which the omission could materially affect CVA.
4. Propose a limited sensitivity or validation test using representative exposure profiles, if available.
5. Treat full path-dependent recovery as a possible future extension, not an initial implementation requirement.

The aim is to disclose and test the assumption, not to solve a larger stochastic modeling problem.

⸻

PART V — Numerical illustration and mathematics

12. Preserve the updated €24.3m example

Use consistently:

Input	Value
Pool nominal	€51m
Index ratio	1.3195
Indexed face	€67.2945m
Illustrative close-out PV	€24.3m
Break-even recovery	Approximately 36.11%

The €24.3m is an illustrative input, not an independently verified live transaction mark.

The calculation must compare close-out and liquidation proceeds on the same valuation and discounting basis.

The static illustration is not itself a production stochastic CVA calculation.

Central reference calculation

Using recoveries 20%, 40%, 65% and weights 30%, 50%, 20%:

[
L_{dist}

0.30[24.3-67.2945(0.20)]^+
+
0.50[24.3-67.2945(0.40)]^+
+
0.20[24.3-67.2945(0.65)]^+.
]

The result is:

[
\boxed{L_{dist}=€3.25233m.}
]

Applying the 39% mean recovery directly gives:

[
[24.3-67.2945(0.39)]^+=0.
]

The loss-equivalent scalar for this fixed exposure is:

[
R_{\mathrm{LE}}

\frac{24.3-3.25233}{67.2945}
\approx31.277%.
]

Explain why these three quantities differ:

* 39% is the mean of the recovery distribution.
* €3.25233m is the expected positive-part loss under the distribution.
* 31.277% is the scalar recovery reproducing that particular expected loss.

Do not describe 31.277% as the universal issuer mark or production portfolio-equivalent recovery.

Required mathematical checks

Retain or verify:

* Recovery weights sum to one.
* Recovery levels lie within admissible bounds.
* The additive mapping preserves the intended mean.
* The expected-loss function is non-increasing in recovery.
* The function is piecewise linear with kinks at state-specific break-even points.
* Deterministic mean recovery does not generally reproduce distributional loss.
* The deterministic approximation is exact in the relevant common-affine regions.
* The loss-equivalent scalar may depend on exposure and may not be unique in zero-loss regions.
* The weighted-CVA identity is subject to its independence and implementation conditions.

Correct any numerical inconsistencies in the current appendices and independently reproduce the calculations.

Avoid unnecessary algebra in the main text. Put proofs and sensitivity derivations in Appendix E.

⸻

PART VI — Contractual treatment and production boundaries

13. Preserve the SPIRE liquidation mechanics

Continue to use the SPIRE 2025 Base Prospectus as the programme-level contractual reference, especially the relevant Condition 13 disposal provisions.

Preserve the established distinctions:

* Commercially reasonable disposal.
* Orderly realization where practicable.
* The Series-specific 30 Reference Business Day period.
* The applicable starting event and Reference Business Day calendar.
* Disposal requirements and exceptions.
* Treatment of saleable versus legally or practically non-saleable assets.
* Delayed proceeds and subsequent contractual credit.
* Legal extinguishment of unpaid residual obligations.

Do not equate a missed sale deadline with zero economic recovery without analyzing the applicable rights and waterfall.

Do not assume the Dealer has access to proceeds from the entire collateral pool without verifying the relevant contractual entitlement.

Document status

Clearly separate:

1. Programme-level provisions verified from the SPIRE 2025 Base Prospectus.
2. Series-specific facts supplied or supported by available transaction materials.
3. Matters requiring executed Final Terms, Swap Confirmation, CSA or other definitive documents.
4. Modeling assumptions made pending those documents.

Do not imply that executed documents have been independently verified when they have not.

14. CVA accounting interface

Retain the distinction between:

[
B_\tau^{liq}
]

and:

[
B_\tau^{avail}.
]

The former is modeled liquidation realization; the latter is the amount available to offset the Dealer’s relevant close-out entitlement under the actual transaction mechanics.

Retain the conditional Dealer-loss expression:

[
L_\tau

[V_\tau^{co}-B_\tau^{avail}]^+.
]

Avoid double counting collateral or other recoveries already reflected in the close-out, CSA treatment, waterfall or engine payoff.

Maintain a clear separation between:

* The Bliq methodology’s economic assumptions.
* The CVA engine’s existing exposure, discounting and default integration.
* Transaction-specific legal allocation.
* Production implementation and model validation.

Do not expand the paper into a redesign of the XVA engine, its exposure sampling, MPOR conventions, numeraire or discounting infrastructure.

Those are external implementation dependencies unless they directly affect the Bliq recovery interface.

⸻

PART VII — Document restructuring and readability

15. Main editorial objective

The paper should be understandable to a technically competent quant reader without requiring them to reconstruct the argument from scattered caveats and appendices.

Use the following narrative:

What is the economic loss? → What does the contract permit? → What does the historical evidence tell us? → Why choose indexed-face recovery? → Why retain recovery dispersion? → How can the existing engine implement it? → What remains uncertain?

Each section should answer one principal question.

Do not repeat the same model recommendation in every section.

Do not introduce mathematical notation before explaining what the quantity represents economically.

Avoid excessive abbreviations and model labels.

Use short paragraphs, descriptive headings and carefully selected tables.

Prefer a well-written explanatory paragraph to a table when the table adds no analytical value.

Conversely, use a table where it genuinely helps readers compare evidence, assumptions or alternatives.

16. Recommended main-paper structure

Executive conclusion — approximately one page

Include:

* The economic valuation objective.
* Recommended indexed-face-normalized recovery approach.
* One trader-facing recovery mark with centrally governed dispersion.
* Scalar-engine implementation through weighted deterministic recovery runs.
* The €24.3m illustration and its key result.
* Principal empirical and contractual qualifications.

Do not include the full derivation, historical event chronology or detailed validation checklist.

Section 1 — Transaction economics and liquidation value

Explain:

* SPIRE collateral and Dealer entitlement.
* Default and disposal mechanics.
* Indexed face and liquidation proceeds.
* Dealer-available recovery and positive-part loss.
* Relevant contractual uncertainties.

Use one coherent valuation chain.

Avoid repeating the same definitions in later sections.

Section 2 — Historical evidence and applicability to France

Include:

* Seven-event evidence summary.
* Data coverage and indicative-price limitations.
* Structural versus numerical comparability.
* Why historical evidence supports a range of severity outcomes.
* Why it does not uniquely identify French probabilities.

Keep detailed data selection and chronology in appendices.

Section 3 — Recommended recovery methodology

This should be the conceptual center of the paper.

Explain:

* Why indexed-face normalization is preferred for this application.
* One trader-facing economic mark.
* Governed recovery distribution.
* Statewise positive-part loss.
* Why direct mean recovery may be inadequate.
* Additive mapping and its interpretation.

Keep the central model simple and clearly defined.

Section 4 — Calibration and numerical illustration

Explain:

* The 20/40/65% recovery states.
* The provisional 30/50/20 weights.
* The €24.3m example.
* The distinction between mean recovery and loss-equivalent recovery.
* The key sensitivities.

Do not repeat the full historical-data discussion.

Section 5 — XVA implementation, governance and limitations

Explain:

* Existing deterministic scalar-recovery infrastructure.
* Weighted deterministic runs as the preferred initial implementation.
* Conditions for exact aggregation.
* Potential one-scalar approximation.
* Exposure/recovery dependence.
* Contractual and implementation verification gates.
* Marking and governance responsibilities.

End with a concise statement of the recommended working methodology and what remains to be validated.

⸻

17. Appendices

Preserve the supporting evidence. Reorganize it where necessary.

Appendix A — Contractual evidence and verification

Programme terms, Series-specific assumptions, disposal mechanics, waterfall, legal uncertainties and verification status.

Appendix B — Historical data and provenance

Instrument register, original versus replacement bonds, event dates, observation windows, missingness, quotation conventions and reproducible descriptive statistics.

Appendix C — France applicability and comparability

Institutional versus numerical comparability, Greece limitations, inflation-linked claim differences and historical-price transfer.

Appendix D — Calibration rationale and sensitivity

Recovery states, weights, parameter provenance, alternative weights, execution considerations, non-realization stress and governance.

Appendix E — Mathematical and XVA validation

Positive-part nonlinearity, deterministic equivalence, additive mapping, bounds, monotonicity, loss-equivalent scalar, weighted deterministic CVA runs and dependence limitations.

Appendix F — Sources, implementation gates and governance record

Source register, documentary status, model ownership, validation requirements and outstanding transaction-specific questions.

Appendices may be as long as necessary to support independent review.

Do not delete substantive evidence solely to meet an arbitrary page count.

⸻

18. What to remove, consolidate or demote

Remove or consolidate:

* M0/M1 labels used for deterministic versus distributional recovery.
* Repeated explanations of the valuation object.
* Repeated discussions of positive-part nonlinearity.
* Repeated disclaimers that historical frequencies are not French probabilities.
* Any suggestion that 39% is a statistically fitted French recovery estimate.
* Any implication that 31.277% is a universal loss-equivalent recovery.
* Any assertion that weighted deterministic CVA runs are exact without stating their assumptions.
* Unsupported claims that face-value recovery is intrinsically more accurate than market-value recovery.
* Unsupported execution haircuts or numerical France adjustments.
* Outdated historical observations, event anchors or legacy calibrations.
* Unnecessary generic sovereign recovery theory.
* Low-level XVA infrastructure details outside the Bliq scope.
* Duplicative tables and excessive main-text derivations.

Retain material that contributes to:

1. Understanding the recommendation.
2. Justifying a modeling choice.
3. Validating a mathematical result.
4. Identifying an important limitation.
5. Reproducing the calibration.
6. Establishing contractual or data provenance.

⸻

PART VIII — Quality control and deliverables

19. Independent consistency checks

Before finalizing v0.12:

1. Verify all numerical calculations from source inputs.
2. Verify that €24.3m replaces the obsolete €19.62m illustration everywhere relevant.
3. Verify the 36.11% break-even threshold.
4. Verify the €3.25233m distributional loss and approximately 31.277% fixed-exposure loss-equivalent recovery.
5. Verify all recovery-weight and severity sensitivities.
6. Verify that the trader-input mapping reproduces the specified mean.
7. Check recovery bounds and monotonicity.
8. Check that all valuation-date conventions are consistent.
9. Check that CVA and collateral-recovery semantics are not conflated.
10. Check all source references, table numbering and equation numbering.
11. Check consistency with the companion CVA paper’s M0 nomenclature.
12. Check that every transaction-specific contractual claim has an appropriate evidence status.
13. Check that the main paper can be understood without reading the appendices.

20. Deliverables

Provide:

A. Bliq v0.12 working paper

A complete, professionally formatted standalone PDF, preserving mathematical rigor and sufficient supporting appendices.

B. Concise revision log

Identify substantive changes from v0.11, including:

* Model nomenclature.
* FV versus MV interpretation.
* Trader-facing recovery interface.
* Deterministic-engine integration.
* Loss-equivalent recovery treatment.
* Calibration and dependence qualifications.
* Structural and editorial consolidation.

C. Reproducible calculation support

Preserve or provide the calculation script or workbook underlying the numerical illustration, sensitivities and relevant historical summaries.

D. Outstanding verification register

List only genuinely unresolved matters, with:

* The precise question.
* Why it matters.
* The evidence required.
* Whether it blocks the working-paper conclusion or only production implementation.

Do not pad this register with generic modeling caveats.

⸻

Final acceptance criteria

The v0.12 paper is successful if a reader can answer the following questions clearly:

1. What economic quantity is Bliq modeling?
2. Why is indexed-face-normalized recovery recommended for this SPIRE transaction?
3. What does the historical evidence support, and what does it not establish?
4. Why does a distribution matter when the desk marks only one recovery number?
5. How does the trader’s (R_{\mathrm{eff}}) determine the internal recovery states?
6. Why does the distribution’s mean not generally reproduce expected Dealer loss?
7. What is (R_{\mathrm{LE}}), and why is it exposure-dependent?
8. How can the existing deterministic-recovery XVA engine implement the methodology?
9. Which assumptions and transaction-specific rights still require verification?
10. What exactly is recommended for implementation and governance?

The paper should communicate one coherent modeling recommendation, not present several equally prominent alternatives and leave the reader to decide.

Preserve the analytical strengths of v0.11. Correct the remaining conceptual and implementation ambiguities. Improve the argument’s flow. Make v0.12 the version we can reasonably regard as methodologically complete, subject to clearly identified verification gates.

Do not begin another broad research cycle. Do not add complexity merely for completeness.

Simple, not simpler.
