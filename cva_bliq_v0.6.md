Agent B — Bliq Working Paper v0.6: Final Technical and Editorial Refinement

Objective

Revise the existing Bliq v0.5 — Transaction Mechanics, Valuation and CVA Implementation into v0.6, a consolidated, conceptually clean, technically defensible, and reader-friendly working paper for senior quant and desk users.

This is a targeted refinement, not a rewrite from scratch.

The v0.5 paper already has a strong overall architecture and important technical conclusions. Preserve these while improving the empirical justification, precision of the mathematical formulation, provenance, presentation, and consistency with the companion CVA methodology.

The intended standard is a production-oriented working paper, not a claim of completed production approval. Explicitly retain outstanding legal, desk, implementation, and model-validation gates.

Governing principles

1. Simple, but not simpler. Give each subproblem the appropriate level of mathematical and explanatory detail. Do not overengineer the liquidation model.
2. Economic and contractual model first; engine implementation second.
3. Preserve established results. Do not remove sound analysis merely to shorten the document.
4. Distinguish facts, modeling assumptions, empirical observations, and implementation conventions.
5. Make the main paper self-contained at the decision-making level, while retaining detailed empirical evidence in the standalone calibration paper and supporting research package.
6. Use consistent notation across Bliq, CVA, and the standalone calibration paper.
7. Do not introduce unsupported precision, fitted parameters, or unverified production claims.
8. Do not discuss the desk’s historical 70%-of-market-value convention. The paper should stand independently as a methodology.

⸻

1. Preserve the core architecture of v0.5

The current sequence is broadly appropriate:

* Transaction mechanics and contractual resources.
* Definition of the economic liquidation quantity.
* Transformation of defaulted claims into executable proceeds.
* Recovery denominator and OAT€i indexation.
* Historical evidence and French transfer.
* Recommended calibration.
* Numerical consequences.
* CVA implementation.
* Controls, validation, and conclusions.

Retain this structure unless a specific change materially improves conceptual clarity.

Keep the executive conclusion prominent and concise. The reader should understand the transaction, the modeled quantity, the central recommendation, and the key limitations before entering the detailed derivation.

Avoid repetitive legal explanations, repeated caveats, and lengthy discussions of alternative models that are not being adopted.

2. Clarify the economic target and reduced-form calibration

Preserve the distinction between:

Economic target: the event-date value of proceeds that can be realized and made available under the contractual disposal and waterfall mechanics.

Working approximation: a four-state distribution of liquidation recoveries normalized to indexed face value.

Use the established notation:

[
F_\tau(N)=IR_{\tau^-}N
]

[
R_\tau^{liq}(k)

\frac{B_\tau^{liq}(N,k)}{F_\tau(N)}
]

[
B_\tau^{liq}(N,k)

F_\tau(N)R_\tau^{liq}(k)
]

Use (N^{pool}) for the whole collateral pool and (N^{post}) for the CSA-posted amount. Do not substitute one for the other.

Clarify that the four states approximate the conditional distribution of timely accessible proceeds. They are not four empirically identified market regimes.

Where the paper introduces a discounted-executable-proceeds expectation, explain that it defines the underlying economic quantity. The scenario model approximates that quantity without requiring a detailed stochastic execution or restructuring model.

Important: If (B_\tau^{liq}(k)) is used for state-contingent proceeds, do not simultaneously define the same symbol as an already scenario-averaged expectation. Make the random outcome and its conditional mean mathematically distinct.

3. Tighten the relationship between physical evidence and risk-neutral CVA

The calibration uses historical Bloomberg quotes, restructuring outcomes, and expert judgment.

These data do not identify risk-neutral French liquidation probabilities.

Explain concisely that:

* Historical observations inform plausible severity and accessibility outcomes.
* Scenario weights are expert-judgment pricing inputs, subject to approval.
* The sovereign survival curve determines modeled default timing.
* The recovery distribution determines conditional loss severity.
* Any dependence between default, swap close-out, indexed principal, and liquidation recovery requires an explicit modeling assumption.

Do not present the scenario weights as statistically fitted probabilities or calibrated risk-neutral frequencies.

Avoid expanding this into a general discussion of physical versus risk-neutral measures. One precise paragraph and consistent equations should suffice.

4. Improve the historical evidence and French transfer section

Section 5 is currently too compressed relative to the importance of the calibration decision.

Expand it moderately, using the authoritative standalone calibration paper and supporting research package.

Add one compact seven-event table containing:

* Sovereign event and year.
* Number of Bloomberg securities.
* Evidence tier.
* Indicative event-window bid level.
* Principal comparability limitation.

Clearly explain that the 22 securities represent seven sovereign event clusters, not 22 independent default observations.

Preserve the distinction among:

1. Distressed secondary-market bond prices.
2. Eventually realized sovereign restructuring recoveries.
3. Executable proceeds available within the SPIRE liquidation window.
4. The French-specific recovery distribution adopted for modeling.

Explain the role of the broader sovereign restructuring database, including the modern bounded NPV-haircut subset, without presenting eventual restructuring recovery as 30-day sale recovery.

The evidence should justify the range and economic mechanisms represented by the scenarios, not claim that the data statistically estimate the French scenario weights.

French transfer conclusion

Retain the substantive conclusion:

* France is outside the directly observed historical sample.
* Euro-area monetary and institutional features, domestic-law intervention, indexed claims, systemic liquidity disruption, and creditor treatment create material differences from the historical comparators.
* The limited and heterogeneous macroeconomic evidence does not support a stable numerical French-specific adjustment.
* Therefore, the central calibration remains an explicit expert-judgment recommendation informed by historical evidence.

Do not add a speculative macroeconomic regression merely to make the methodology appear more sophisticated.

The discussion should answer: Why are these historical observations relevant, what can they support, and where must judgment enter?

5. Preserve the recommended calibration and nonlinear loss treatment

Retain the existing three modes and four states.

Mode	State weights	State recoveries	Mean recovery
Conservative	25/35/30/10%	0/15/30/55%	19.75%
Balanced	10/25/45/20%	0/20/40/65%	36.00%
Aggressive	5/15/40/40%	0/25/50/75%	53.75%

The Balanced mode is the provisional central recommendation; Conservative and Aggressive are sensitivities, not statistical confidence intervals.

Preserve the explanation that the 10% no-access state means no cash accessible within the relevant modeled window, not necessarily zero ultimate legal recovery.

Preserve the numerical example and independent arithmetic reconciliation.

Most importantly, retain statewise evaluation of the positive-part loss:

[
\sum_k w_k
\left[V_\tau^{co}(k)-B_\tau^{avail}(k)\right]^+
]

Do not replace it with the positive part evaluated at mean recovery.

Do not add another generic liquidation haircut or collateral LGD to losses already calculated net of liquidation proceeds.

6. Clarify liquidation proceeds versus Dealer-available resources

This is an important conceptual refinement.

Keep three quantities distinct:

* (B_\tau^{liq}): collateral liquidation value or proceeds.
* (B_\tau^{avail}): proceeds available to satisfy the Dealer claim after applicable senior deductions and contractual allocation.
* (L_\tau^{Dealer}): residual Dealer loss.

Where v0.5 uses

[
B_\tau^{avail}(k)
\approx
\min\left(
[B_\tau^{liq}(N^{pool},k)-K_\tau^{sen}]^+,
[V_\tau^{co}(k)]^+
\right),
]

explain that the claim cap applies to Dealer recovery, not to the economic liquidation value of the underlying collateral.

Retain the distinction even if the formulas can be algebraically simplified for a particular loss calculation.

Confirm the relationship between (V_\tau^{co}), the Dealer’s contractual entitlement, and the available-resource waterfall using the companion CVA paper. Do not silently assume they are identical if the transaction mechanics distinguish them.

Maintain the baseline whole-pool assumption, subject to executed-document verification.

The paper should clearly separate the economic model being proposed from the legal evidence still needed to approve it.

7. Keep the implementation mapping focused

Preserve the current engine architecture and verified implementation behavior.

The proposed liquidation distribution should enter the existing event-loss calculation, with statewise positive-part evaluation before aggregation over liquidation states and default-time buckets.

Do not redesign the XVA engine’s:

* Time grid.
* Numeraire or discounting conventions.
* Sovereign survival-curve construction.
* Exposure sampling.
* Performing-state CSA mechanics.
* Swap close-out calculation.

Verify that the implementation mapping is consistent with the actual code and companion CVA methodology.

Keep CDS bootstrap recovery, collateral liquidation recovery, and residual payoff recovery separate.

In particular, the 36% collateral recovery must not be reused as the engine’s residual payoff-recovery parameter.

Where v0.5 proposes a 0% residual payoff-recovery setting, retain it only under the stated limited-recourse and waterfall interpretation and distinguish the contractual assumption from verified engine configuration.

Do not claim implementation completion without source-code evidence.

8. Remove unnecessary modeling complexity

Review Sections 3, 4, and 6 for material that is mathematically correct but unnecessary at the present level of implementation.

For example:

* Detailed tranche-execution integral formulas can be shortened or moved to an appendix if the implemented model is reduced-form.
* A speculative state-dependent recovery function involving sovereign intensity or macro variables should not appear as part of the adopted model.
* Recovery-of-face, recovery-of-market-value, and recovery-of-Treasury comparisons should remain only as detailed as necessary to explain the selected indexed-face denominator.

Do not remove distinctions that are necessary for correct valuation.

The objective is to allocate complexity to the subproblem that requires it, not uniformly simplify every section.

9. Strengthen references, datasets, and provenance

The existing academic reference list is too narrow to represent the full empirical research.

Retain and verify the academic references, including sovereign restructuring, haircut methodology, and recovery conventions.

Add a clearly organized Data Sources and Research Provenance subsection identifying, as applicable:

* Bloomberg BVAL historical bond extracts.
* The seven-event, 22-security selection register.
* The sovereign restructuring haircut database and its publication/version.
* Cruces–Trebesch restructuring data and updates.
* Sovereign default datasets used for contextual diagnostics.
* IMF/FRED or other public macroeconomic series actually used.
* SPIRE 2025 Base Prospectus.
* Executed Series documentation, clearly identified as verified or outstanding.
* The standalone final calibration paper and its research package.

Verify exact dataset titles, authors, versions, dates, and provenance from the supplied source materials. Do not invent bibliographic details.

Separate academic references from empirical datasets and transaction documentation.

The main paper should cite the relevant evidence near the claims it supports and point to the standalone calibration paper for the detailed selection register, event-date conventions, data transformations, diagnostics, and reproducibility.

Do not reproduce the complete 22-ISIN inventory or all historical regressions in the main paper.

10. Improve layout and visual communication

Use the existing restrained, professional technical-paper style.

Improve readability through:

* Consistent mathematical notation and equation formatting.
* More deliberate whitespace around headings and tables.
* Better table column widths and alignment.
* Avoiding orphaned paragraphs and awkward page breaks.
* A clear visual distinction between conclusions, assumptions, evidence, and implementation requirements.
* Compact tables rather than dense prose where they genuinely help.

Consider one simple diagram showing:

Original Collateral Default → defaulted claim/entitlement → disposal → net available proceeds → Dealer recovery → residual loss → CVA.

Do not introduce decorative charts.

The empirical section may benefit from a seven-event evidence table. Avoid charts that suggest stronger statistical inference than the data support.

Maintain a concise main body and detailed supporting appendices.

11. Strengthen validation and approval boundaries

Retain the existing controls and approvals section.

Organize outstanding items by responsible function where possible:

* Structuring/Legal: executed Series terms, liquidation rights, calendars, waterfall, indexed claim treatment.
* Desk/Market Risk: scenario levels, weights, no-access assumption, execution feasibility.
* Quant Development: statewise implementation, payoff-recovery configuration, discounting and weighting.
* Model Validation: independent numerical reproduction, sensitivities, convexity and denominator consistency.
* Model Owner: approval, parameter ownership, monitoring and refresh criteria.

Do not describe the calibration as empirically validated or production-approved.

The document should nevertheless be complete enough for implementation planning and independent technical challenge.

12. Final deliverables and acceptance criteria

Produce:

A. Bliq v0.6 PDF — polished, self-contained, senior quant/desk-ready working paper.

B. Editable source — LaTeX or the existing document source.

C. Concise change log — what changed from v0.5, what was preserved, and what remains open.

Before delivery, perform a final consistency check:

* Every symbol has one meaning.
* Indexed-face and market-value denominators are never confused.
* Whole-pool and posted-collateral amounts remain distinct.
* Liquidation proceeds, Dealer-available resources, and residual loss remain distinct.
* Historical observations are not represented as fitted French probabilities.
* Scenario weights sum to 100%.
* The three mean recoveries and illustrative losses reproduce.
* Statewise nonlinear loss aggregation is preserved.
* No double counting of recovery, haircut, or LGD.
* CVA and calibration notation agree.
* References support the claims made.
* All tables, equations, figures and page breaks render correctly.
* Outstanding approvals are explicit.

Final instruction: Deliver a controlled improvement to v0.5, not a new research program. Preserve the sound core model, improve the evidence-to-calibration explanation, remove unnecessary complexity, and make the result conceptually elegant, technically defensible and easy for a senior quant or desk reader to follow.

## Additional mandatory instruction — Standalone publication standard

Although this task uses Bliq v0.5 as the development baseline, the delivered Bliq v0.6 must be written as a **fully standalone technical working paper**, not as an incremental revision or commentary on an earlier draft.

### Publication-facing requirements

1. Write the document as if it were being presented independently to a senior quant, desk head, model validator, or implementation team with no access to earlier drafts.

2. Do not refer to previous document versions, historical drafting decisions, changes from v0.5, or the revision process anywhere in the substantive text.

3. Do not use phrases such as “in v0.6,” “the previous approach,” “this revision,” or “we now introduce,” unless the comparison concerns an economically meaningful alternative methodology rather than an earlier document draft.

4. Version identification is permitted only in conventional document-control metadata, such as the title page or footer. Use a clean title:

   **Bliq — Transaction Mechanics, Valuation and CVA Implementation**

   Subtitle: *SPIRE Sovereign Collateral Working Paper*

5. The executive conclusion must state the actual model, its principal results, its intended use, and its approval status—not summarize what changed.

6. Every essential definition, assumption, formula, and conclusion must be understandable from the paper itself. Supporting research may be cited for detailed evidence, but the reader must not need another document to reconstruct the central valuation methodology.

7. Maintain a clear hierarchy:
   - Main paper: transaction economics, adopted methodology, essential empirical rationale, calibration, CVA interface, implementation and validation.
   - Technical appendices: necessary derivations, numerical reconciliation and supporting model details.
   - Standalone calibration paper: full empirical research, historical bond inventory, data selection and detailed diagnostics.

8. Do not overpopulate the main text with implementation details or research that belong in appendices. Conversely, do not remove essential reasoning merely to achieve a shorter document.

9. Maintain consistent notation with the companion CVA methodology and final calibration paper. Define symbols locally on first use, even when those symbols appear in supporting documents.

10. Preserve professional academic and quantitative-finance conventions for equations, tables, citations and references. Distinguish primary transaction documentation, empirical datasets and academic literature.

### Revised deliverables

**A. Standalone Bliq working paper (PDF)**

A publication-style, technically defensible, readable working paper suitable for independent senior quant/desk review. No revision commentary or change log inside the document.

**B. Editable source**

Complete LaTeX or equivalent source, including any figures, tables and bibliography needed to reproduce the PDF.

**C. Internal change log (separate file)**

A concise record of substantive changes from v0.5, preserved material, corrected issues, and outstanding questions. This is for development review only and must not be incorporated into the working paper.

**D. Final quality review**

Verify that the document stands on its own, contains no unintended drafting-history references, uses consistent notation, preserves the complete adopted methodology, and renders cleanly.

**Acceptance test:** A senior quant unfamiliar with the drafting history should be able to read this paper from beginning to end and understand what is being valued, why the methodology is appropriate, how the calibration was selected, how it enters CVA, and which assumptions still require approval—without knowing that v0.5 or any earlier version existed.