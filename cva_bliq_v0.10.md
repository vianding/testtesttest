Agent B — Bliq Liquidation Value Methodology and CVA Loss Interface

Complete instructions for standalone working paper v0.10

1. Objective and deliverable

Prepare a complete, standalone quantitative methodology paper entitled:

Bliq — Liquidation Value Methodology and CVA Loss Interface

Subtitle: SPIRE Sovereign Collateral Working Paper

Version: 0.10 — 17 October 2026 — Not production approved

The paper concerns the valuation of defaulted French inflation-linked sovereign collateral held in a SPIRE repackaging structure, and the resulting collateral-adjusted loss to the Dealer for CVA purposes.

The primary objective is to establish a transparent, economically coherent, and defensible methodology for:

1. Defining event-date liquidation value.
2. Selecting a reduced-form distribution of liquidation recoveries.
3. Explaining the historical evidence and expert judgments supporting that distribution.
4. Mapping liquidation proceeds through the contractual collateral/waterfall mechanism into Dealer recovery.
5. Calculating the Dealer’s loss state by state.

The paper must be self-contained. It must explain its methodology, assumptions, parameters, evidence and conclusions without referring readers to previous paper versions or previous calibration exercises.

Existing working documents may be used as supporting material, but the final paper must stand independently.

Do not describe the paper as an incremental revision of an earlier version.

Deliver a complete PDF, with a concise main body, detailed supporting appendices, properly numbered equations and tables, and a complete reference section.

2. Transaction and documentary scope

The reference transaction is:

* SPIRE Series ISIN: XS3411888731
* Original collateral: French OAT€i FR0013209871
* Coupon: 0.10%
* Maturity: July 2047
* Base collateral pool nominal: €51 million

The transaction involves an Issuer holding the indexed sovereign bond and entering into a swap with the Dealer to transform the underlying collateral economics into the scheduled note economics.

Use the existing transaction working documents, companion CVA methodology and SPIRE 2025 Programme Base Prospectus as supporting sources.

Distinguish carefully between:

* Contractual provisions verified in the generic Base Prospectus.
* Transaction-specific facts established by the available working documents.
* Provisions requiring confirmation against executed Series Final Terms, swap documentation and CSA.
* Explicit modeling assumptions.

Do not present generic programme provisions as automatically applicable Series-specific elections.

Contractual disposal mechanics

Incorporate the relevant SPIRE 2025 Base Prospectus disposal provisions, particularly Condition 13, subject to verification of the exact wording and applicable Series elections.

The working interpretation is:

* The Disposal Agent generally seeks an orderly disposal as soon as reasonably practicable, rather than waiting indefinitely for the highest eventual restructuring recovery.
* The applicable disposal period may impose a cutoff after which remaining saleable collateral is disposed of even at a deeply distressed price, subject to contractual exceptions.
* Condition 13(k) contemplates exceptional circumstances in which disposal may be prohibited, illegal or impossible.
* The contractual possibility of non-disposal does not, by itself, establish that the Dealer’s event-date economic recovery is zero.
* Unsold collateral, retained claims and subsequent proceeds require separate contractual analysis.

Do not assume that the historical 30-business-day observation window is the actual Series liquidation period.

Record the applicable Series liquidation-period election, Reference Business Day calendar, precise starting event, disposal-agent discretion and treatment of unsold collateral as documentary verification items.

Dealer claim and collateral treatment

The working CVA interpretation is that the Dealer’s gross contractual close-out is determined from the swap’s scheduled contractual obligations, before collateral credit.

The collateral recovery then reduces the Dealer’s positive claim through the applicable posted-collateral and waterfall mechanisms.

The working model assumes the Dealer may benefit from realization of the whole collateral pool, subject to the actual priority waterfall and existing collateral credits.

Do not assume the posted collateral notional independently caps total Dealer recovery.

Clearly distinguish legal entitlement to collateral, operational ability to dispose of it, and the price realized on disposal.

3. Economic definition of Bliq

Define Bliq as the event-date value of liquidation proceeds that can be legally and operationally realized and credited through the contractual mechanism.

For disposal receipts at dates (u_j\geq\tau), use:

[
B_\tau^{liq}

\sum_j D(\tau,u_j)\Pi_{u_j}^{exec}(N^{pool}).
]

Here:

* (\tau) is the relevant sovereign default/disposal event.
* (u_j) are realization dates.
* (D(\tau,u_j)) discounts receipts to the event date on the appropriate valuation basis.
* (\Pi_{u_j}^{exec}) represents executable net proceeds from the collateral pool, subject to the defined cost convention.

The conditional distribution of event-date Bliq values is the relevant object for CVA.

Distinguish Bliq from:

* Performing bond market value.
* Theoretical value of a defaulted sovereign claim.
* Eventual restructuring-package value.
* Published sovereign haircut statistics.
* CDS auction recovery prices.
* Prices of replacement bonds after exchange settlement.

A restructuring outcome or market quote may inform Bliq, but neither is automatically equivalent to contractually accessible executable proceeds.

Delayed receipts are discounted consistently with their timing. Delayed realization is not automatically assigned zero value.

4. Recovery convention and model boundary

Use the provisional indexed-face convention:

[
F_\tau=IR_{\tau^-}N^{pool}
]

and

[
R_\tau^{liq}(k)

\frac{B_\tau^{liq}(k)}{F_\tau},
\qquad
B_\tau^{liq}(k)=F_\tau R_\tau^{liq}(k).
]

For the numerical illustration:

[
N^{pool}=€51m,\qquad IR_{\tau^-}=1.3195,
]

so:

[
F_\tau=€67.2945m.
]

The indexed-face denominator is a modeling convention. It must not be presented as an automatically verified legal default claim for the OAT€i.

The legal treatment of indexation, index lag, accrual, principal floor, discontinuity and redenomination remains subject to bond documentation and applicable law.

Historical clean prices per 100 nominal are severity references; their use for an indexed French bond requires an explicit economic transfer assumption.

Retain a concise denominator-reconciliation explanation for face-value recovery versus market-value recovery.

Do not build an independent stochastic bond-price or sovereign restructuring model. The intended framework is a reduced-form conditional liquidation-recovery distribution interfacing with the existing CVA methodology.

5. Historical dataset — authoritative inputs

Use the latest supplied Bloomberg historical data files:

* bliq_calibration4_histDefaultWindowBondPrice.csv
* bliq_calibration10a_histData(2).csv
* bliq_calibration10b_histData(2).csv
* bliq_calibration10c_histData(2).csv
* bliq_calibration10z_histDataSummary.csv

The four price-history files are authoritative for dated observations and numerical calculations.

The summary file is authoritative for the latest event/instrument metadata and classification, subject to independent verification where material.

The price-history observations used for primary analysis have been confirmed as Series 1 legacy bonds. Do not substitute replacement bonds or eventual restructuring-package prices.

Older Bloomberg narrative summaries must not override these latest files where they conflict.

Required historical event register

The dataset covers 19 legacy instruments across seven historical event groups:

Event	Bonds	Tier	Indicative mean bid
Argentina 2020	3	A	40.2
Zambia 2020	3	A	48.9
Ghana 2022	3	A	37.0
Ecuador 2020	2	B	33.0
Ukraine 2024 restructuring, 2022 T0	4	B	21.9
Greece 2012	1	C	20.8
Ukraine 2015	3	C	75.1

These figures are reference checks. Independently recompute the statistics from the raw CSVs.

Use valid Bloomberg BVAL evaluated bid observations over the selected standardized weekday offsets 0–30 inclusive.

The available data use a Monday–Friday calendar without holiday suppression. Do not label these observations as actual Series Reference Business Days.

Do not forward-fill missing observations or treat missing bids as zero.

Calculate event-level statistics by averaging available bond bids within each event and offset, then averaging the event path across its available offsets.

Weight sovereign events equally in cross-event descriptive statistics.

Report coverage and source limitations.

Historical selection statistics

The latest working calculations are:

* Tier A: three events, mean 42.0.
* Tier A+B: five events, mean 36.2.
* All tiers: seven events, mean 39.6.

Recompute these and explain their selection sensitivity.

These are historical descriptive statistics, not probabilities or direct French liquidation-recovery estimates.

Event-anchor distinctions

Record the selected T0, its economic interpretation and material alternative dates.

In particular:

* Ukraine 2015: 25 August 2015 is an observed distressed-price trough; 5 October 2015 is the alternative ISDA failure-to-pay determination.
* Ukraine 2024 restructuring: the selected T0 is the August 2022 payment-freeze/default-related event, approximately two years before the August 2024 exchange settlement.
* Greece 2012: only two valid event-path observations within the selected window, approximately 6% coverage.
* Ecuador 2020: preserve the distinction between the selected April event and the earlier March payment-related date.

Do not conflate an analytical observation anchor with a legal credit event.

Where useful, show the Ukraine 2015 alternative-anchor comparison and explain that changing T0 can change measured prices without changing the underlying instrument’s ultimate recovery.

Historical interpretation

The principal empirical findings should be:

1. Defaulted sovereign legacy bonds can retain substantial positive market value.
2. Observed distressed prices cover a wide severity range.
3. Several higher-quality observations cluster around the mid-30s to high-40s.
4. Prices in the low 20s are historically observable.
5. Favorable outcomes can produce much higher prices, but high observations may depend on event timing and information conditions.
6. Restructuring announcements and progress can materially influence prices.
7. The sample does not identify French conditional recovery probabilities.
8. BVAL evaluated bids are not verified executable block prices.

Do not mechanically map the historical mean to the French central expected recovery.

6. Central liquidation-recovery model — agreed calibration

Use three positive-recovery sale-severity states as the central model.

This is a deliberate model-design decision.

The states represent materially different executable liquidation outcomes, not sequential restructuring stages and not independently estimated latent market regimes.

The agreed central calibration is:

State	Weight	Recovery of indexed face
Severe distressed sale	30%	20%
Distressed orderly sale	50%	40%
Resilient sale	20%	65%
Total	100%	39% weighted mean

The mean is:

[
\bar R^{liq}

0.30(20%)+0.50(40%)+0.20(65%)

39%.
]

These weights are expert pricing assumptions conditional on the relevant default event, not fitted historical frequencies or uniquely market-implied risk-neutral probabilities.

Do not reintroduce a 0% non-realization state into the central distribution.

Do not preserve an earlier four-state distribution or its mean recovery as a calibration target.

7. Why three states?

Explain the model selection explicitly.

Two positive-recovery states would combine materially different distressed-market conditions.

Three states allow the model to represent:

* Deeply distressed execution.
* A functioning but discounted distressed market.
* A relatively favorable realization.

Additional states would introduce parameters that the limited historical evidence cannot independently identify.

The objective is a parsimonious and auditable distribution appropriate for a reduced-form CVA interface.

Do not claim that the data statistically establish exactly three regimes.

8. Recovery-level rationale

Provide a clear evidence-to-parameter explanation for each recovery level.

Severe distressed sale — 20%

Economic conditions:

* Substantial restructuring uncertainty or unfavorable expected terms.
* Severe dealer balance-sheet and financing constraints.
* Limited buyer capacity.
* Weak price discovery and significant distressed execution concessions.

Historical support:

* Ukraine August 2022 event-window mean around 21.9.
* Greece 2012 indicative price around 20.8, but based on sparse and less comparable evidence.

Interpretation:

20% is a plausible deeply distressed execution anchor, not an empirical lower bound or a fitted French quantile.

More severe outcomes below 20% remain possible and should be captured in sensitivity analysis.

Distressed orderly sale — 40%

Economic conditions:

* Transferable defaulted claim.
* Functioning distressed-bond or claims market.
* Buyers can form reasonable expectations about restructuring value.
* Material credit and liquidity discounts remain.

Historical support:

* Ghana around 37.0.
* Argentina around 40.2.
* Ecuador around 33.0.
* Zambia around 48.9.
* Tier A mean around 42.0.
* Tier A+B mean around 36.2.

Interpretation:

40% is the strongest historically supported central severity anchor, but is not a fitted French conditional expectation.

Resilient sale — 65%

Economic conditions:

* Greater clarity about expected restructuring terms.
* Credible institutional support or favorable expected resolution.
* Stronger distressed-investor demand and better price discovery.

Historical support:

* Ukraine 2015 high-price observations around 75, subject to important observation-anchor qualifications.
* Broader eventual restructuring evidence demonstrating that higher recoveries can occur.

Interpretation:

65% is a judgmental favorable-outcome anchor below the observed high historical reference. It is less directly supported than the 40% central state.

It does not require a completed restructuring before disposal.

9. Weight rationale

Explain each weight separately and transparently.

30% severe weight

A French sovereign default would be a major systemic event potentially disrupting European government-bond financing, collateral eligibility, dealer risk capacity and investor mandates.

A severe distressed-sale outcome therefore deserves material weight.

30% is a risk-sensitive expert judgment, not an estimate derived from historical event counts.

Do not claim precision beyond what the evidence supports.

50% orderly weight

The strongest historical observations cluster in the distressed-market range represented by the 40% state.

The contractual expectation of attempted disposal and the existence of distressed-claims trading channels support using a functioning, although severely discounted, sale as the modal working scenario.

50% expresses that central modeling judgment. It is not the observed proportion of historical events.

20% resilient weight

A defaulted French claim may still command substantial value if expected restructuring terms, institutional support or market demand are favorable.

However, given the severity of the hypothetical sovereign event and the constrained disposal process, this is not assumed to be the dominant outcome.

20% gives the favorable regime meaningful but limited weight.

Interpretation of the distribution

The weights are conditional expert pricing inputs for the CVA recovery model.

They are not:

* Historical default frequencies.
* Estimated French physical probabilities.
* Statistically fitted regime probabilities.
* Independently calibrated risk-neutral probabilities.

The central distribution is intended to be economically representative, not artificially conservative through multiple overlapping haircuts.

10. Restructuring timing and executable value

Integrate restructuring timing explicitly into the economic rationale.

The relationship is:

Information available at sale time → expected claim value and required risk premium → executable sale price.

Restructuring timing influences the state recoveries through:

* Uncertainty about legal treatment and exchange terms.
* Expected value of the defaulted claim.
* Funding conditions.
* Buyer participation.
* Settlement and transferability.
* Market price discovery.

Do not assume restructuring announcements always improve prices. Announcements can clarify expectations while revealing unfavorable terms.

The states are not chronological stages.

A bond may be sold before restructuring settlement at a price reflecting the expected eventual outcome.

Under the generic SPIRE disposal mechanics, the Disposal Agent cannot simply be assumed to wait for the most favorable restructuring resolution.

The central model therefore values the outcome of the contractual disposal process, not the maximum eventual recovery obtainable after a prolonged restructuring.

Include a concise discussion of early versus later permitted disposal as a timing sensitivity.

Do not introduce an unnecessary separate stochastic restructuring-time model.

11. Exceptional inability to dispose — separate stress

Retain exceptional non-disposal as an explicit risk, outside the central three-state distribution.

Distinguish:

1. A deeply distressed but executable sale.
2. A temporary absence of willing buyers.
3. Legal or operational prohibition of disposal.
4. Delayed realization with retained economic rights.
5. Permanent loss of enforceable recovery rights.

These are not economically equivalent.

The generic SPIRE terms contemplate exceptional inability to dispose, but they do not establish that the Dealer’s economic recovery is automatically zero.

For a non-disposal stress, define:

[
R^{ND}

\frac{\text{event-date value of recoverable proceeds and retained rights}}
{F_\tau}.
]

A zero value for (R^{ND}) is a maximum-loss assumption, not a verified contractual result.

Do not assign an empirically unsupported central probability to this outcome.

For illustration, report hypothetical non-disposal stress weights of 5% and 10%, with the central three-state distribution scaled by (1-q).

For a zero-recovery stress:

[
EL(q)

(1-q)EL_{\text{central}}
+
q[V_\tau^{co}]^+.
]

Clearly label (q) as a hypothetical stress weight, not a calibrated probability.

Retain the contractual treatment of unsold collateral and subsequent Dealer recovery as an open verification item.

12. Execution-price convention

The central recoveries are intended to represent distressed executable proceeds, not pristine mid-market prices.

Historical BVAL evaluated bids are evidence informing those recoveries.

Do not apply an additional universal 2–5-point BVAL-to-execution deduction automatically.

The execution effects may already be embedded in the judgmental severity states.

If an incremental block-execution concession is tested, show it separately, specify the assumed basis and avoid double counting.

The €51m nominal collateral block may be transferable through dealer, RFQ, bilateral or distressed-investor channels, but the existence of such channels does not guarantee executable bids during a French sovereign crisis.

No numerical French macro uplift or discount should be introduced without credible evidence.

13. Dealer loss and CVA interface

Retain the existing distinction between:

* Gross contractual Dealer close-out.
* Posted collateral already credited.
* Incremental collateral realization through the waterfall.
* Total resources available against the Dealer’s positive claim.
* Residual Dealer loss.

Use:

[
L_\tau^{Dealer}(k)

\left[
V_\tau^{co}-B_\tau^{avail}(k)
\right]^+.
]

Where posted collateral has already been recognized, use a consistent two-route accounting representation.

For example, with posted credit (C_\tau^{post}):

[
E_\tau^{rem}

[V_\tau^{co}-C_\tau^{post}]^+,
]

[
Rec_\tau^{Dealer}(k)

\min\left(B_\tau^{wf}(k),E_\tau^{rem}\right),
]

[
B_\tau^{avail}(k)

\min(C_\tau^{post},[V_\tau^{co}]^+)
+
Rec_\tau^{Dealer}(k).
]

Define the waterfall proceeds consistently so that collateral already credited is not credited a second time.

If posted and unposted resources can be reconciled to a common event-date valuation basis, the simplified whole-pool approximation may be written as:

[
B_\tau^{avail}(k)
\approx
\min\left(
[B_\tau^{liq}(k)-K_\tau^{sen}]^+,
[V_\tau^{co}]^+
\right).
]

This is a valuation-consistency approximation, not an independently verified contractual identity.

The baseline should use zero separately modeled senior deductions unless a reliable amount has been identified, while retaining any costs already embedded in the recovery calibration.

Do not apply a second collateral LGD, liquidation-recovery multiplier or generic execution haircut.

The companion CVA methodology supplies the default timing, exposure profile and discounting. Bliq supplies the conditional liquidation-recovery distribution and resulting statewise loss.

14. Numerical illustration — retain €19.62m

Use the following main illustrative inputs:

[
N^{pool}=€51m,
]

[
IR_{\tau^-}=1.3195,
]

[
F_\tau=€67.2945m,
]

[
V_\tau^{co}=€19.620m.
]

The €19.62m gross close-out is retained for consistency with the companion CVA working paper.

Treat it as an illustrative valuation input, not an independently verified market mark or a forecast of default-time exposure.

The break-even recovery is:

[
R^*

\frac{19.620}{67.2945}

29.16%.
]

Central statewise calculation

State	Weight	Recovery	Dealer loss	Weighted loss
Severe	30%	20%	€6.1611m	€1.84833m
Orderly	50%	40%	€0	€0
Resilient	20%	65%	€0	€0
Total	100%	39% mean		€1.84833m

The severe-state liquidation proceeds are:

[
0.20\times67.2945=€13.4589m.
]

The severe-state loss is:

[
19.620-13.4589=€6.1611m.
]

Thus:

[
EL_{\text{central}}

0.30\times6.1611

€1.84833m.
]

This is default-conditioned expected loss for one illustrative exposure, not full CVA.

Show explicitly that:

[
\sum_k w_k[V^{co}-B^{avail}(k)]^+
\neq
\left[V^{co}-\sum_k w_k B^{avail}(k)\right]^+
]

in general.

Applying the 39% mean recovery directly would incorrectly produce zero loss for this illustration.

15. Exposure sensitivity — €23.4m standard-pricer value

A standard-pricer valuation as of 5 October 2026 gives approximately €23.4m.

Use this as a secondary exposure sensitivity, subject to confirming that it is gross of collateral credit and comparable in valuation perspective and contractual scope to the main illustrative close-out.

Do not replace the main €19.62m example.

For (V^{co}=€23.4m):

[
R^*=\frac{23.4}{67.2945}\approx34.77%.
]

At 20% recovery, the severe-state loss is:

[
23.4-13.4589=€9.9411m.
]

The central expected loss is:

[
0.30\times9.9411

€2.98233m.
]

Report the sensitivity concisely:

Gross close-out	Break-even recovery	Central expected loss
€19.62m	29.16%	€1.848m
€23.4m	34.77%	€2.982m

Explain that a contemporary standard-pricer MtM is not necessarily the future close-out at sovereign default.

16. Priority sensitivities

Prioritize parameters that materially affect the loss.

For the €19.62m example, only recoveries below 29.16% generate positive loss.

Therefore the severe recovery and severe weight are more important than fine-tuning the resilient recovery.

Severe-recovery sensitivity

Hold the weights at 30/50/20 and the orderly/resilient recoveries at 40/65.

Vary severe recovery across:

10%, 15%, 20%, 25%, 30%.

Calculate the corresponding expected loss exactly from the stated formula.

Severe-weight sensitivity

Hold recoveries at 20/40/65.

Vary the severe-state weight while reallocating the difference to the orderly state and keeping the resilient weight at 20%.

Use severe weights of:

20%, 30%, 40%.

Exceptional non-disposal sensitivity

For zero-recovery non-disposal stresses with weights (q=5%) and (q=10%), scale the three central state weights by (1-q).

For the €19.62m illustration:

* Central: €1.84833m.
* 5% non-disposal stress: approximately €2.737m.
* 10% non-disposal stress: approximately €3.626m.

These stresses are not additional calibrated central scenarios.

Where useful, explain how nonzero retained-rights recovery would reduce the stress loss.

Do not simultaneously change weights, recoveries and exposure in an unexplained Conservative/Aggressive distribution.

17. Recommended paper structure

Use the following structure, or a close equivalent.

Executive conclusion

Prominently state the valuation object, recommended three-state central calibration, mean recovery, illustrative statewise expected loss, and principal contractual limitations.

1. Transaction and default mechanics

Explain the SPIRE structure, contractual disposal, waterfall and Dealer claim.

2. Definition and valuation of liquidation proceeds

Present Bliq, indexed-face recovery, delayed realization and denominator consistency.

3. Historical evidence and applicability to France

Present the corrected legacy-bond dataset, event-level statistics, evidence-quality distinctions and the limits of historical transfer.

4. Recommended liquidation-recovery model

Present the three central states, weights, recoveries, and concise economic rationale.

5. Illustrative loss consequences

Present the €19.62m central example, the €23.4m exposure sensitivity, and the positive-part nonlinearity.

6. CVA interface and conclusion

Explain posted collateral, whole-pool resources, waterfall accounting, and the interface with the companion CVA framework.

Appendix A — Data sources, event selection and provenance

Include the instrument register, event dates, coverage, historical price conventions, alternative anchors, restructuring references and source limitations.

Appendix B — Calibration rationale and sensitivity

Include:

* Why three states.
* Evidence supporting each recovery level.
* Rationale for each weight.
* Restructuring timing as a price driver.
* French-specific transfer assumptions.
* Severe-recovery and severe-weight sensitivities.
* Exceptional non-disposal stress.
* Recovery-base reconciliation.

Appendix C — Contractual assumptions and verification register

Identify all material open documentary questions, including Series-specific disposal timing, unsold collateral treatment, Dealer rights, posted collateral credit, waterfall priorities and OAT€i default claim.

References

Provide a clean list of original contractual, Bloomberg, sovereign restructuring and academic sources.

18. Appendix quality requirements

Appendix B is especially important.

It must not merely say that parameters are judgmental.

It must explain the reasoning chain:

Observed evidence → economic interpretation → French-specific transfer judgment → selected recovery or weight → limitation and sensitivity.

Include a compact calibration table with columns:

State | Economic regime | Historical anchors | Recovery rationale | Weight rationale | Key uncertainty

The table should make it clear that:

* Recovery severity has meaningful empirical anchors.
* Regime probabilities are substantially more judgmental.
* The selected central model is not fitted to the historical average.
* Exceptional non-disposal is a distinct contractual risk.
* Restructuring timing affects sale price through information and execution conditions.

Do not devote excessive space to debt/GDP scatterplots or unstable correlations.

A short paragraph is sufficient to explain why the available macro data do not support a stable numerical French adjustment.

Keep supporting source data and selection logic auditable.

19. Presentation and mathematical quality

Use a restrained, professional quantitative-research style.

Prioritize clarity and logical structure over excessive length.

Requirements:

* Clean, consistent typography and spacing.
* Legible equations and tables.
* No clipped headings, footnotes or page elements.
* Consistent mathematical notation.
* Defined symbols at first use.
* Appropriate rounding without arithmetic inconsistencies.
* No repetitive caveats or unnecessarily verbose explanations.
* No decorative charts that do not materially improve interpretation.
* No unsupported statistical precision.
* No invented contractual provisions or data.
* No internal C++ or implementation details unrelated to the methodology.

The executive conclusion should communicate the result quickly.

The appendices should contain the detailed reasoning needed for independent quantitative review.

20. Final validation checklist

Before delivering the paper, verify:

1. All historical prices and event statistics are calculated from the latest authoritative files.
2. All primary historical instruments are correctly classified as legacy bonds.
3. Selected event anchors and alternative dates are clearly distinguished.
4. Greece’s sparse observation coverage is disclosed.
5. The central distribution has exactly three positive-recovery states.
6. Weights are 30/50/20 and recoveries are 20/40/65.
7. Mean recovery is 39%.
8. The €19.62m illustrative central expected loss is €1.84833m.
9. The €23.4m exposure sensitivity is €2.98233m.
10. Exceptional non-disposal is outside the central distribution.
11. Delayed recovery is not automatically valued at zero.
12. No generic execution haircut is double counted.
13. No historical event frequencies are represented as French pricing probabilities.
14. No generic SPIRE condition is incorrectly asserted as an executed Series-specific election.
15. The recovery denominator and collateral accounting are internally consistent.
16. The paper can be read and evaluated without consulting any earlier version.

Final deliverable: A complete standalone v0.10 PDF, accompanied by a short validation/change summary for the reviewer. The PDF itself must contain the full methodology, calibration rationale, conclusions, references and contractual limitations.
