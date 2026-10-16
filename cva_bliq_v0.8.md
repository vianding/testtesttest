Agent B — Bliq Working Paper v0.8: Standalone Methodology and Editorial Refactor

Objective

Revise Bliq — Transaction Mechanics, Valuation and CVA Implementation, version 0.7, into version 0.8, a standalone, technically rigorous, readable methodology paper suitable for senior quant and Desk review.

This is a substantive editorial and methodological refactor, not simply a copy-editing exercise.

The primary purpose of the paper is to establish the valuation methodology for default-state liquidation proceeds of French sovereign inflation-linked bond collateral, with an evidence-backed calibration recommendation.

The paper should answer four fundamental questions:

1. What economic quantity must be valued, and why?
2. How should that quantity be defined and measured?
3. What evidence supports the proposed calibration, and where does judgment enter?
4. How does the resulting liquidation value affect the Dealer’s CVA loss?

The reader should understand the methodology without needing access to previous drafts, internal research conversations, or the separate calibration working paper.

Preserve technical correctness and substantive conclusions, but eliminate unnecessary complexity, repetition, implementation details and unsupported assertions.

Do not modify the companion CVA paper or other working papers as part of this task.

⸻

1. Establish the correct document boundary

1.1 Make Bliq fully standalone

The separate SPIRE Bliq Calibration Final paper and its supporting research package were used to develop the calibration. They are research inputs, not authoritative references for this methodology paper.

Remove references that present them as controlling documents, including language such as:

* “The authoritative empirical source is the standalone SPIRE Bliq Calibration Final paper.”
* “The calibration package described in Appendix A.”
* References to conclusions that can only be understood by consulting that separate paper.

Instead, integrate the relevant empirical evidence, methodology, assumptions and conclusions directly into Bliq.

Cite original sources wherever possible:

* SPIRE Base Prospectus and applicable transaction documents.
* Bloomberg BVAL histories and Bloomberg research extracts.
* Bloomberg Master Haircuts Publication April 2025.
* Cruces–Trebesch historical restructuring data.
* IMF WEO, FRED and other macroeconomic sources actually used.
* Published academic research.

The supporting calibration research may be consulted to recover calculations, source lineage and selection rationale, but must not appear as an external authority or required reading in the final paper.

1.2 Separate methodology from implementation

This is a valuation methodology paper, not an engine implementation report.

Remove Appendix A.3, “Implementation evidence,” including source-code paths, function names and discussion of recovery-parameter resolution.

Remove other source-code-specific claims throughout the paper.

Do not discuss:

* Engine class names or functions.
* Code configuration.
* Unit and regression tests.
* Software integration status.
* Quant Development sign-off.
* Production approval workflows.
* Detailed time-grid, numeraire or survival-weighting implementation.

These subjects belong in the companion CVA methodology, implementation documentation or model governance materials.

Retain only the essential conceptual interface between liquidation proceeds and the Dealer’s loss, including the requirement not to apply recovery or collateral credit twice.

1.3 Distinguish fact, assumption and inference

Maintain a clear distinction among:

* Documented contractual facts — directly supported by available transaction documents.
* Empirical observations — derived from identified market or restructuring data.
* Mathematical results — derived from stated definitions and assumptions.
* Modeling assumptions — conventions selected for the methodology.
* Expert judgments — particularly the French recovery scenarios and weights.
* Open questions — matters that cannot be established from available evidence.

Do not present a working contractual interpretation as an executed-document fact.

Do not describe judgmental calibration parameters as statistically estimated or empirically identified.

⸻

2. Introduce an explicit evidence and footnote standard

This is a central requirement for v0.8.

Every material, non-obvious factual or empirical claim must have a traceable basis.

Apply the following rules.

2.1 External factual claims

Provide a footnote or citation to the underlying source.

Examples include:

* SPIRE liquidation mechanics, sale discretion, deadlines, priority and limited recourse.
* The Greek restructuring package composition and its estimated market value.
* Historical BVAL event-window prices.
* The number of securities and sovereign events in the sample.
* Restructuring database counts, filters, medians and other statistics.
* OAT€i indexation and principal-floor conventions.

Cite specific sections, conditions, tables, datasets or observation definitions where available.

Do not attach a broad citation that does not actually establish the claim.

2.2 Empirical conclusions

When the main text makes an empirical claim, provide enough information for a reader to understand why it follows.

For example, v0.7 states that the exploratory debt/GDP relationship changes sign when event tiers are restricted.

Do not retain this as an unexplained conclusion.

Either:

* Present the underlying comparison or diagnostic in an appendix and reference it; or
* Replace it with a more limited, supportable conclusion, explaining that the small, heterogeneous event sample does not justify a stable French macro adjustment.

Do not invent a new statistical finding or imply that exploratory regressions are robust.

2.3 Modeling judgments

Where the paper recommends a judgmental parameter, explicitly identify it as such.

Explain:

* What evidence informed the judgment.
* What that evidence cannot establish.
* Why the selected parameter or range is considered reasonable.
* Which alternative assumptions are represented by sensitivities.

A footnote may direct the reader to an appendix containing detailed rationale.

Do not disguise judgment as empirical precision.

2.4 Mathematical statements

For important equations, provide the relevant definitions and assumptions, and include a short derivation or intuitive explanation when the result is not immediate.

A mathematical identity does not require an external citation if it follows from the paper’s own definitions.

However, assumptions used to obtain an approximation must be stated.

2.5 Footnote discipline

Use footnotes selectively and consistently.

The main text should remain readable without requiring constant reference to footnotes.

Use:

* Short source citations for facts.
* Short explanations for assumptions or qualifications.
* Appendix cross-references for detailed derivations, data selection, robustness checks and limitations.

Avoid long argumentative footnotes. Substantive explanations should appear in the main text or appendices.

Do not manufacture source references, page numbers, section numbers or data provenance. If evidence is unavailable, qualify the claim or identify the gap.

⸻

3. Restructure the main paper

Use the following conceptual structure. Section names may be refined for elegance and clarity.

Executive conclusion

Open with a concise explanation of:

* The target quantity: event-date value of timely accessible collateral liquidation proceeds.
* Why this differs from performing-bond value, announced haircut and ultimate sovereign recovery.
* The recommended four-state model.
* The Balanced case as the provisional central calibration.
* The distinction between a 36% reporting mean and the full distribution required for CVA.
* The principal empirical and contractual limitations.

Avoid lengthy approval or production-readiness statements.

Section 1 — Transaction and default mechanics

Explain the performing SPIRE structure and the contractual path from Original Collateral Default to disposal proceeds and the Dealer’s residual claim.

Retain the transaction-specific economics needed to understand Bliq.

Distinguish the generic SPIRE Base Prospectus provisions from unverified Series-specific elections.

Remove the current Section 1.3 provenance table from the main text. Replace it with a concise statement of the documentary basis and outstanding uncertainties. Preserve detailed source status in an appendix if necessary.

Avoid duplicating the companion CVA paper’s close-out analysis.

Section 2 — Definition and valuation of liquidation proceeds

Define the event-date value of accessible proceeds, allowing for realization at later dates.

Explain the distinction between:

* Performing-bond market value.
* Value of the defaulted claim or restructuring package.
* Timely accessible liquidation proceeds.

Define the recovery denominator and clarify the treatment of the OAT€i index ratio.

Explain why an indexed-face convention is adopted, including its legal and modeling limitations.

Remove the existing “The target is not” bullet list. Replace it with a concise, positive explanation and only the most important contrasts.

Section 3 — Historical evidence and applicability to France

Explain what historical sovereign restructuring experience can teach us about liquidation proceeds.

Include:

* Greece 2012 as a worked example of the difference between nominal exchange terms, package market value and other recovery measures.
* The 22-security, seven-event Bloomberg BVAL research sample.
* Event-level summary results, with selection tiers and material comparability limitations.
* The broader restructuring-haircut database and what it measures.
* The limits of transferring historical evidence to French sovereign inflation-linked debt.
* Why the current evidence does not support a reliable fitted French macro adjustment.

The historical analysis must be sufficiently self-contained to support the recommendation.

However, avoid reproducing the entire research process in the main narrative.

The purpose is to establish what the evidence supports—not to catalogue every analysis previously performed.

Section 4 — Recommended liquidation-recovery model

Present the four liquidation states:

1. No timely accessible cash.
2. Severe liquidation.
3. Distressed orderly sale.
4. Resilient sale.

Retain the Conservative, Balanced and Aggressive parameterizations, subject to verification of arithmetic and internal consistency.

Make clear that the state definitions combine market severity and accessibility.

Explain the economic rationale for the recovery levels and scenario weights, especially the Balanced 10% no-access assumption.

Explicitly state that these are expert-judgment pricing inputs informed by historical evidence, not statistically fitted French default probabilities.

Retain the key distinction between:

* Scenario recovery rates.
* Scenario weights.
* Mean recovery.
* The distribution used to calculate expected loss.

Section 5 — Illustrative loss consequences

Retain the numerical illustration, but improve its clarity substantially.

The purpose of this section is to demonstrate why statewise recovery matters for CVA loss, not to establish the transaction’s actual close-out valuation.

Define each input before using it.

For the current illustration:

* Pool base notional: €51.000m.
* Index ratio: 1.3195.
* Indexed face: €67.2945m.
* Dealer close-out entitlement: €19.620m.

The €19.620m figure must be clearly described as an illustrative Dealer exposure supplied by, or assumed consistently with, the companion transaction/CVA analysis.

If its precise source can be verified, cite that calculation.

If it cannot, state that it is an illustrative assumption. Do not imply it was calculated by the Bliq methodology.

Explain the break-even recovery:

[
R_\tau^*=\frac{E_\tau^{Dealer}}{F_\tau}.
]

For the stated illustration, it is approximately 29.16%.

Explain in ordinary language that this is the minimum recovery required to cover that particular Dealer claim, under the stated simplifying assumptions.

It is not a universal transaction recovery threshold or a forecast of sovereign recovery.

Show the Balanced case calculation clearly:

State	Weight	Recovery	Dealer loss	Weighted loss
No access	10%	0%	€19.620m	€1.962m
Severe liquidation	25%	20%	€6.161m	€1.540m
Distressed orderly	45%	40%	€0	€0
Resilient sale	20%	65%	€0	€0

Total illustrative default-conditioned expected loss: approximately €3.502m.

Explain why using the 36% average recovery before taking the positive part would incorrectly produce zero loss in this example.

Present Conservative and Aggressive results in a compact sensitivity comparison.

Avoid describing these default-conditioned illustrative losses as full CVA.

Section 6 — CVA interface and conclusion

Retain the essential relationship:

[
L_\tau^{Dealer}

\left[E_\tau^{Dealer}-B_\tau^{avail}\right]^+.
]

Explain what is supplied by the existing CVA framework and what is supplied by the Bliq model.

Clarify that collateral already credited and subsequent waterfall proceeds must be reconciled without double counting.

Where appropriate, retain a concise reference to the whole-pool approximation and the conditions under which it is valid.

Do not reproduce the full engine aggregation methodology, survival weighting, time-grid design, numeraire treatment or source-code configuration.

Close with the recommendation and its principal limitations, rather than a separate production-control checklist.

⸻

4. Simplify technical presentation without weakening the mathematics

Apply the following editorial principles throughout.

4.1 Lead with the economic conclusion

Before a technical derivation, explain in one or two sentences what question the calculation answers.

After a material equation, explain its interpretation.

A reader should not need to reconstruct the purpose of an equation from notation alone.

4.2 Use bold for a small number of key arguments

Examples:

Recovery must be applied to the same valuation base on which it was calibrated.

A restructuring haircut is not a liquidation recovery.

Indicative prices are not executable proceeds.

Historical observations inform severity but do not identify a French recovery distribution.

Expected loss must be calculated state by state before averaging.

Do not bold entire paragraphs or use emphasis decoratively.

4.3 Reduce unnecessary notation

Use a consistent notation dictionary.

Avoid introducing multiple symbols for the same economic object.

Do not retain technical definitions or equations that are unused later.

Preserve the distinction between collateral economic value, collateral credit, available proceeds, Dealer entitlement and Dealer loss.

4.4 Remove repeated disclaimers

A qualification should appear where it matters, rather than being repeated in every section.

For example, establish early that the scenario weights are judgmental, then reinforce that point in the calibration recommendation. Do not repeat it in every subsequent paragraph.

4.5 Improve tables

Use clear headings, appropriate column widths, sufficient row padding and restrained ruling.

Avoid dense tables with long wrapped prose.

Move detailed audit and governance tables out of the main narrative.

4.6 Review every paragraph for necessity

Ask:

* Does this paragraph help define the model?
* Does it support a material modeling choice?
* Does it explain a result or limitation?
* Is this already established elsewhere?
* Would a senior quant or Desk reviewer understand it without reading another working paper?

Remove or relocate material that does not pass this test.

⸻

5. Appendices: evidence, derivations and source traceability

Start each appendix on a new page.

Recommended structure:

Appendix A — Data sources, selection and provenance

Provide direct references to the primary data and documents.

Include:

* SPIRE 2025 Base Prospectus and relevant conditions.
* Series-specific documents available or still requiring confirmation.
* OAT€i instrument and indexation references.
* Bloomberg BVAL source histories, event definitions and observation conventions.
* Complete 22-ISIN selection register or a clear supporting-data reference.
* Explanation of why only seven sovereign events constitute independent clusters.
* Inclusion/exclusion decisions, substitutions, missing observations and tier definitions.
* Bloomberg Master Haircuts source and extraction rules.
* Cruces–Trebesch data and methodological references.
* IMF WEO/FRED sources, variables, dates and vintages actually used.

Do not refer to the separate calibration paper as the authoritative empirical source.

Appendix B — Supporting analysis and sensitivity

Include material necessary to substantiate non-obvious claims in the main paper.

Examples:

* Event-level BVAL statistics and calculation conventions.
* Relevant cross-event comparisons.
* Historical restructuring recovery definitions.
* Selected macro diagnostics, if referenced in the main text.
* Scenario-parameter rationale.
* Additional loss sensitivities.
* Any material derivation omitted from the main text for readability.

Keep the analysis proportionate.

Do not include exploratory regressions merely because they were previously attempted.

Appendix C — References and contractual assumptions

Provide a clean academic bibliography and source references.

Document the contractual interpretations material to the valuation, distinguishing verified provisions from unresolved Series-specific questions.

Do not include source-code implementation evidence or a production sign-off matrix.

⸻

6. Specific technical checks

While refactoring, verify the following without expanding the paper unnecessarily.

1. Recovery denominator: Indexed face is a stated convention, not an automatically established legal default claim. Verify consistency with OAT€i indexation, default treatment and applicable claim provisions.
2. Recovery conventions: RFV, RMV and Treasury/cashflow recovery are not interchangeable. If an example applies a recovery calibrated on one denominator to another, explain the resulting error.
3. Greece example: Distinguish face-value write-down, restructuring NPV haircut, market value of the exchange package and CDS auction recovery. Verify numerical statements against appropriate sources.
4. Historical sample: Preserve the 22-security/seven-event distinction. Do not treat daily observations or multiple bonds from the same event as independent sovereign defaults.
5. French transfer: Do not infer a numerical French uplift or discount from an unstable or insufficiently supported macro regression.
6. Scenario calibration: Verify weights sum to one, weighted means are correct and all three modes are internally consistent.
7. Illustrative losses: Reproduce the break-even recovery and statewise expected-loss calculations independently.
8. Positive-part ordering: Do not replace expected statewise positive-part loss with the positive part of average recovery.
9. Whole-pool collateral: Avoid incorrectly restricting liquidation recovery to the posted collateral notional where the contractual analysis supports access to the whole collateral pool. Distinguish total collateral value from the portion credited to the Dealer.
10. Double counting: Do not apply a second generic liquidation haircut, collateral LGD or payoff recovery to a loss already defined net of the relevant collateral realization.
11. Calendar: Do not equate the empirical T0–T30 TARGET-business-day window with a verified SPIRE Series-specific 30 Reference Business Day disposal period.
12. Risk measure: Distinguish historical observations from the pricing-measure scenario assumptions used within CVA.
13. Contractual interpretation: Do not silently promote held transaction-analysis assumptions to executed-document facts.

If any existing statement fails verification, correct or qualify it and record the change.

⸻

7. Quality standard and deliverables

Produce:

1. Bliq v0.8, a polished, standalone methodology paper in PDF.
2. The editable source.
3. A concise change log identifying substantive changes from v0.7.
4. A short list of unresolved factual, contractual or calibration questions, separated from editorial issues.

Before finalizing, perform three independent reviews.

Technical review: Check definitions, equations, arithmetic, denominator consistency, payoff ordering and contractual qualifications.

Evidence review: For every material non-obvious claim, confirm that the cited source, appendix analysis or stated rationale actually supports it. Remove unsupported claims rather than hiding them behind generic citations.

Editorial review: Read the paper as a senior quant or Desk reviewer unfamiliar with its research history. Ensure the narrative is understandable on its own, the main conclusions are visible, and unnecessary technical detail has been removed.

Inspect the rendered PDF page by page for equation clipping, dense tables, poor spacing, broken references, awkward page breaks and appendices that fail to start on new pages.

Final editorial principle

Simple, not simplistic. Rigorous, not encyclopedic. Standalone, not self-referential.

The paper should establish a defensible economic methodology, explain its calibration with appropriate evidence, and communicate the implications clearly.

It should not read like a research diary, implementation checklist, or summary of another working paper.

Preserve the model’s intellectual substance while making every important claim understandable and challengeable.
