Agent B — Bliq v0.15: Final Closure Revision

Objective

Prepare Bliq v0.15 — Liquidation Value Methodology and CVA Loss Interface as the final standalone working paper.

Use v0.14 as the baseline. This is a strictly limited correction and editorial closure pass, not a new modeling exercise.

The methodology, central calibration, historical analysis and overall document architecture are accepted. Preserve them unless a demonstrable mathematical inconsistency requires correction.

The finished paper should be technically precise, concise, internally consistent and readable by a senior quant or trading stakeholder without requiring access to the revision history.

Do not expand scope, introduce new calibration work, investigate production source code or reopen settled methodological decisions.

⸻

1. Lock the methodological recommendation

Preserve the following conclusions without substantive change:

* Bliq represents the event-date economic value of OAT€i collateral proceeds that can be realized and credited to the Dealer through the SPIRE transaction.
* It is distinct from the performing bond mark, eventual restructuring recovery and counterparty LGD.
* Recovery is normalized to indexed face, subject to the existing OAT€i legal-claim verification qualification.
* The reference configuration uses three liquidation states:

State	Recovery	Conditional weight
Severe distressed sale	20%	30%
Distressed orderly sale	40%	50%
Resilient sale	65%	20%
Weighted mean	39%	100%

* The desk maintains one economic recovery mark, (R_{\mathrm{eff}}), mapped into centrally governed state recoveries using the existing additive mapping.
* Preserve statewise positive-part loss and the weighted-CVA methodology.
* Preserve the distinction between (R_{\mathrm{eff}}), weighted-mean recovery and exposure-specific loss-equivalent recovery.
* Preserve exceptional non-disposal as a separate contractual stress, not an ordinary fourth liquidation state.
* Preserve the existing qualification that conditional state weights are expert judgment rather than statistically calibrated French probabilities.

Do not change the central parameters to fit a historical average or introduce an additional generic execution haircut.

2. Set a firm boundary around implementation

This paper defines the required economic and mathematical interface; it does not specify or verify the production code implementation.

We anticipate that implementation work will be required separately. There is no need to determine whether the current scalar-recovery interface can already perform the desired calculation.

Make the following limited edits.

2.1 Executive conclusion

Replace any suggestion that the preferred initial implementation is to reuse the existing engine without a code change.

State the methodological requirement:

Bliq liquidation recovery must determine the state-contingent collateral resource before the Dealer’s positive-part loss is formed. The resulting state-specific CVAs are combined using the governed conditional weights. Counterparty loss given default remains a separate quantity. Production implementation is outside the scope of this working paper.

Do not discuss possible reuse of existing scalar infrastructure, verification of code insertion points or alternative engineering approaches.

2.2 Main Section 5

Retitle Section 5 as appropriate, for example:

5. CVA loss interface, governance and limitations

Retain only the content necessary to establish:

1. The trader supplies one (R_{\mathrm{eff}}).
2. The governed mapping determines three liquidation recoveries and their fixed weights.
3. Each recovery determines the relevant collateral resource on a consistent valuation basis.
4. Dealer loss is formed after the collateral resource is recognized, using the positive-part convention.
5. The resulting state CVAs are combined using the governed weights.
6. Counterparty LGD, default probabilities and other credit-risk quantities are not substituted for Bliq recovery or applied twice.

Retain the principal mathematical relationship:

[
\mathrm{CVA}_{\mathrm{dist}}
=\sum_k w_k,\mathrm{CVA}(R_k)
]

with its necessary assumptions and qualifications.

Keep the posted-collateral versus waterfall distinction and the requirement to avoid double counting. The main text needs only a concise explanation; supporting reconciliation belongs in the appendix.

2.3 Remove implementation-specific discussion

Remove or substantially shorten:

* Existing-engine capability claims.
* Source-code verification requirements.
* Scalar-recovery insertion-point investigations.
* Whether a targeted code change is necessary.
* Repeated deterministic-run engineering instructions.
* Production workflow design and proposed code modifications.
* Speculation about whether a pathwise-recovery redesign is required.
* Production-code approval gates.

Do not replace these passages with new implementation commentary.

A single scope sentence is sufficient:

Detailed implementation design and code validation will be addressed separately.

2.4 Preserve genuine methodology qualifications

Do not remove the economic assumptions merely because they may also matter to implementation.

Retain concise qualifications concerning:

* Common valuation date and discounting basis.
* State dependence of close-out or collateral resources.
* Conditional weights and their independence assumptions.
* Correct collateral credit and waterfall treatment.
* Separate counterparty LGD.
* Contractual and legal-claim verification.

These belong in a methodology paper.

⸻

3. Clarify the collateral-credit reconciliation

Review the numerical example introduced in Appendix E of v0.14.

The example uses:

* Gross positive close-out: €24.3m.
* Posted collateral credit: €8m.
* Future waterfall cash receipt: €10m.
* Discount factor to the common event-date basis: 0.95.
* Event-date value of the future waterfall receipt: €9.5m.
* Total event-date credit: €17.5m.
* Residual loss: €6.8m.

Make the time-value convention unambiguous.

In the table, label the relevant amount “Waterfall PV at event” or an equally clear expression.

Add a short explanatory note:

In the delayed-receipt example, the future €10m receipt has an event-date value of €9.5m using a discount factor of 0.95. All credits and losses in the table are expressed on the same event-date valuation basis.

Ensure that the €9.5m is not discounted a second time.

Check all table rows against the defined posted-credit and remaining-claim caps. Preserve the reconciliation if numerically consistent; correct any actual arithmetic or labeling error.

Do not expand the appendix into a detailed collateral-accounting implementation specification.

⸻

4. Refine the weighted-CVA exactness statement

In Appendix E, preserve the mathematical validity of weighted complete-scenario CVA calculations, but distinguish two separate claims:

Mathematical claim: weighted scenario CVAs implement the specified fixed-mixture model.

Economic modeling assumption: the chosen conditional weights and their use across default times and market states appropriately represent the intended liquidation scenarios.

Use wording along these lines:

Weighted deterministic CVA runs exactly implement the specified fixed-mixture scenario model when state weights are constant and the scenario calculations use consistent conventions. The economic appropriateness of applying that mixture across default times and market paths remains a separate modeling assumption.

Preserve the qualifications already present in v0.14 concerning state-dependent weights, recoveries, market variables and default timing.

Do not suggest that mathematical exactness validates the empirical probabilities.

Do not add a new stochastic-recovery framework.

⸻

5. Correct the statement about loss-equivalent recovery

In Appendix E, review the discussion of state-dependent close-out, indexed face, collateral credit and waterfall proceeds.

Avoid a categorical assertion that the scalar loss-equivalent formula is generally invalid whenever the underlying quantities depend on state.

For fixed positive (V) and (F), an admissible scalar can reproduce a specified loss in the appropriate positive-loss region:

[
R_{\mathrm{LE}}=\frac{V-\mathcal L_{\mathrm{dist}}}{F}.
]

However, such a scalar is exposure-specific and does not generally provide a transferable collateral-recovery mark.

Use the following formulation:

When close-out, collateral resources or waterfall allocations depend on the state, the simple fixed-(V), fixed-(F) derivation no longer applies directly. A loss-equivalent scalar may still be fitted for a specified exposure, subject to admissible recovery bounds and payoff conditions, but it is not generally transferable across exposures, states or portfolios.

Preserve the important distinction:

* (R_{\mathrm{eff}}): the trader-facing economic recovery control.
* (R_{\mathrm{LE}}): a scalar fitted to reproduce a specified loss.
* Portfolio-CVA-equivalent recovery: a potentially different scalar fitted to a complete CVA calculation.

Do not introduce further scalar-recovery variants.

⸻

6. Preserve the historical evidence and French applicability

No new historical calibration is required.

Preserve:

* Seven heterogeneous sovereign episodes.
* Original legacy bonds as the primary event-window sample.
* Bloomberg BVAL bids as indicative observations, not executable prices.
* Equal-event summary conventions.
* Greece as the strongest euro-area institutional analogy but a weak numerical price anchor.
* Separate treatment of Ukraine observation anchors and later default or restructuring dates.
* The French transmission-channel analysis covering institutions, market liquidity, funding and collateral, and OAT€i claim treatment.
* The conclusion that the French macro backdrop informs conditional severity narratives but does not generate a numerical recovery uplift or probability estimate.

Greece coverage footnote

Retain the verified factual detail:

* 12 March 2012: Bloomberg bid/ask 21 / 23.125.
* Next available observation, 4 July 2012: bid/ask 24.124 / 24.5.
* Only two valid bid observations within the selected 31-offset window, producing approximately 6.5% coverage.
* The missing observations reflect limitations of the available Bloomberg historical extract; they must not be interpreted as evidence that the bond did not trade.

Do not add interpolation, forward filling or invented observations.

Calibration interpretation

Retain the three reference recoveries and weights.

The recovery levels are evidence-informed judgments. The weights remain explicitly governed expert judgments.

Do not imply that the reference 39% mean was calibrated to the approximately 39.6% seven-event mean.

Do not add a France-specific macro adjustment unsupported by an independent model.

⸻

7. Final readability and document polish

Make only targeted editorial improvements. Avoid wholesale rewriting of sections already working well.

Main paper

The main text should have a clear progression:

Transaction economics → historical evidence and France → recommended recovery model → numerical consequence → CVA interface and limitations.

Ensure that:

* The executive conclusion states the recommendation immediately.
* Each section has a clear purpose without repetitive introductions.
* The three-state distribution is displayed once as the principal calibration table.
* The numerical example clearly shows why mean recovery and statewise loss differ.
* The final recommendation is concise and consistent with the executive conclusion.
* Methodology is presented as a recommendation, while contractual and empirical limitations remain accurately qualified.

Avoid repeating the same caveat in the executive conclusion, main text and appendix unless it serves a genuinely different purpose.

Appendices

Keep supporting evidence, detailed sensitivity analysis, derivations, reconciliation tables, document-verification questions and provenance in the appendices.

Do not move technical material back into the main paper merely to demonstrate completeness.

Formatting

Check the compiled PDF for:

* Awkward page breaks or nearly empty pages.
* Tables extending beyond margins.
* Equations separated from their explanatory text.
* Inconsistent terminology and symbols.
* Footnotes that are difficult to locate or read.
* Incorrect equation, table, section or appendix references.
* Excessive whitespace created by manual page breaks.
* Orphaned headings or short introductory paragraphs.

Keep tables compact, aligned and readable. Avoid creating new visual elements unless they materially improve understanding.

⸻

8. Final consistency checks

Before release, verify:

1. The reference weighted mean remains exactly 39%.
2. The €51m pool nominal, 1.3195 index ratio and €67.2945m indexed face reconcile.
3. The €24.3m common-basis illustrative exposure produces a break-even recovery of approximately 36.11%.
4. The reference three-state illustration produces approximately €3.25233m conditional expected loss.
5. Direct use of the 39% mean produces zero loss in that particular static example, without implying that it produces zero CVA generally.
6. The static loss-equivalent recovery remains approximately 31.28% for that exposure.
7. All recoveries are expressed on the stated denominator.
8. There is no duplicated discounting, collateral credit, LGD or execution haircut.
9. The conditional weights are not described as French empirical or risk-neutral probabilities.
10. The main paper contains no unsupported claim that existing production code has been verified.
11. The executive conclusion, Section 5 and final recommendation use consistent language.
12. The document remains clearly marked “Not production approved.”

If a check fails, correct the inconsistency without reopening the methodology.

⸻

9. Deliverables and stopping rule

Produce:

1. Bliq v0.15 final PDF.
2. Updated LaTeX source.
3. A short change log identifying the local corrections from v0.14.

No new research, data collection, calibration, production code review or alternative modeling proposal is requested.

Stopping rule: once the listed corrections, mathematical checks and PDF formatting review pass, finalize v0.15. Do not initiate another round of discretionary methodological or editorial improvements.

The objective is a completed, standalone and defensible working paper—not a production implementation specification.
