Agent B — Bliq v0.13 Final Revision

Methodology Consolidation, Reader-Centered Restructuring and Implementation Readiness

Assignment

Revise Bliq — Liquidation Value Methodology and CVA Loss Interface, v0.12, into v0.13, intended to be the final substantive version of the standalone working paper.

This revision has two equally important objectives:

1. Methodological integrity: preserve the correct economic reasoning, contractual interpretation, historical evidence, mathematics and implementation recommendation, while addressing the outstanding issues identified below.
2. Editorial quality: transform a technically sound but sometimes dense working paper into a document whose argument is clear, progressively developed, easy to navigate and defensible under independent challenge.

The guiding principle is:

Simple, not simpler.

Do not achieve simplicity by deleting necessary analysis. Achieve it by choosing the right sequence, explaining each concept once, moving supporting material to the right place, and separating conclusions from qualifications.

Treat v0.12 as the baseline. Preserve its strong work and verified results. Do not reopen settled modeling choices without new evidence or a material inconsistency.

⸻

Part I — The final argument the paper must communicate

1. The paper’s central thesis

The final paper should consistently communicate the following argument:

First: The relevant valuation object is the economic value of OAT€i collateral resources that can be realized and credited to the Dealer following a hypothetical French sovereign credit event.

Second: The preferred recovery normalization is indexed face, under the companion CVA paper’s M0 methodology. This is a practical economic convention, subject to verification of the OAT€i legal claim, rather than a claim that market-value-based modeling is intrinsically incorrect.

Third: Historical sovereign episodes demonstrate material variation in distressed bond prices and realizability. They inform the severity range, but do not statistically identify a French liquidation distribution.

Fourth: A centrally governed three-state recovery distribution is a defensible, parsimonious way to represent this dispersion. The desk should maintain only one recovery mark, (R_{\mathrm{eff}}).

Fifth: Because Dealer loss contains a positive part, applying the mean recovery directly can materially understate loss. The distribution must therefore be retained when this nonlinearity matters.

Sixth: Existing path-independent deterministic-recovery XVA infrastructure can be reused through weighted CVA runs, subject to verifying—and if necessary modifying—the code so that recovery applies to the intended collateral-liquidation quantity.

Seventh: The recommendation remains conditional on specific contractual, legal-claim and implementation checks. These checks must be visible without obscuring the methodological conclusion.

Every main section should advance one part of this argument.

Do not write the paper as a chronological account of the research undertaken.

⸻

Part II — Mandatory readability redesign

2. Write for a reader who has never seen the previous versions

Assume the primary reader is a technically competent quant or senior reviewer who understands CVA and fixed-income valuation but does not know the history of this project.

The reader should not have to reconstruct the argument from equations, appendices or earlier papers.

Apply the following rules.

2.1 One principal question per section

Each section must begin with a short paragraph explaining the question it answers.

For example:

* Section 1: What collateral value is relevant to Dealer CVA?
* Section 2: What does the historical evidence tell us about a hypothetical French liquidation?
* Section 3: What recovery model should we use, and why?
* Section 4: What difference does the recommended model make numerically?
* Section 5: How do we implement and govern it?

The section must answer its question before moving into secondary detail.

2.2 Use a consistent reasoning sequence

For each major methodological conclusion, use:

Question → economic intuition → evidence → modeling choice → limitation.

Do not start with a dense qualification, then present a formula, then explain the underlying economic problem several paragraphs later.

For example, in discussing the recovery distribution:

1. Explain that different disposal outcomes can produce different collateral proceeds.
2. Explain that CVA loss is nonlinear in those proceeds.
3. Demonstrate why a weighted mean is insufficient.
4. Introduce the three-state representation.
5. Explain the desk’s single recovery mark.
6. State the calibration and dependence limitations.

This is the preferred order even if it requires moving paragraphs from v0.12.

2.3 One paragraph, one principal idea

Rewrite paragraphs that combine multiple distinct topics.

In particular, avoid paragraphs that simultaneously discuss:

* Historical data limitations.
* French institutional support.
* Recovery probabilities.
* Desk marking.
* XVA implementation.
* Contractual verification.

Each paragraph should have a recognizable topic sentence and a clear conclusion.

As an editorial guideline, aim for approximately 80–130 words for substantive explanatory paragraphs, with shorter paragraphs where appropriate. This is not a mechanical word limit.

Avoid long strings of semicolon-separated caveats.

2.4 Put the conclusion before the detailed qualification

A common weakness of technical working papers is to surround every conclusion with so many qualifications that the reader cannot identify the recommendation.

Instead, use:

Conclusion: We recommend a governed three-state liquidation recovery distribution.

Reason: It preserves the nonlinear loss impact of recovery dispersion.

Qualification: The state weights are expert judgment and require governance.

Do not lead with several paragraphs explaining why the weights cannot be statistically estimated before telling the reader what the weights are and why the model uses them.

2.5 Explain concepts before introducing notation

Do not introduce an equation merely because it is mathematically available.

Before each major equation, provide one or two sentences explaining:

* What economic quantity it represents.
* Why the calculation is necessary.
* What the reader should learn from it.

After the equation, explain its implication in plain language.

For example, before the weighted-CVA equation, explain that the engine can price each recovery scenario using deterministic recovery and that the resulting CVAs are then averaged using centrally governed weights.

2.6 Use a controlled vocabulary

Define and distinguish:

* Performing collateral value.
* Defaulted claim value.
* Liquidation value.
* Dealer-creditable resources.
* Indexed face.
* Trader recovery mark (R_{\mathrm{eff}}).
* State recovery (R_k).
* Conditional pricing weight (w_k).
* Static loss-equivalent recovery (R_{\mathrm{LE}}).
* Portfolio-CVA-equivalent recovery (R_{\mathrm{LE}}^{CVA}).

After definition, use the same term consistently.

Do not use “recovery” without a qualifier when more than one recovery concept is under discussion.

Do not describe (R_{\mathrm{eff}}) as an equivalent loss recovery.

2.7 Eliminate repetition through cross-references

Each recurring caveat must have one primary home.

Use the following allocation:

Topic	Primary location
What liquidation value means	Section 1
Indexed-face rationale	Section 1
Historical evidence quality	Section 2 and Appendix B
France-specific transfer	Section 2 and Appendix C
Expert-judgment weights	Section 3 and Appendix D
Why recovery dispersion matters	Sections 3–4
Loss-equivalent recovery	Section 4 and Appendix E
XVA implementation assumptions	Section 5 and Appendix E
Contractual verification	Section 5 and Appendix A
Governance register	Appendix F

Elsewhere, provide a short reminder and a cross-reference.

Do not repeat full explanations.

2.8 Use appendices to support the argument, not to hide it

The main text must contain the reasoning needed to understand and assess the recommendation.

The appendices should contain:

* Detailed data provenance.
* Complete event comparisons.
* Extended contractual analysis.
* Alternative calibration assumptions.
* Sensitivity tables.
* Mathematical proofs and implementation validation.
* Detailed governance records.

A reader should be able to understand the recommendation from the main text, while a reviewer should be able to challenge and reproduce it using the appendices.

2.9 Avoid false precision

Use appropriate rounding in the main text.

For example:

* (R_{\mathrm{eff}}=39%).
* Break-even recovery approximately 36.11%.
* Illustrative distributional loss approximately €3.25m.
* Static loss-equivalent recovery approximately 31.28%.

Retain exact numerical values in reproducibility calculations or appendices where useful.

Do not present 30/50/20% weights as statistically precise estimates.

⸻

Part III — Prescribed main-document structure

3. Executive conclusion — target approximately one page

Rewrite the executive conclusion from the reader’s perspective.

Use four or five short paragraphs, not a continuous technical summary of every section.

Recommended sequence:

Paragraph 1 — The valuation question

Explain the hypothetical French OAT€i credit-event setting and the economic target: realizable, Dealer-creditable collateral value.

Paragraph 2 — The recommendation

State M0 indexed-face-based liquidation recovery, one desk mark and the centrally governed three-state distribution.

Show the reference configuration compactly:

* (R_{\mathrm{eff}}=39%).
* Recoveries 20/40/65%.
* Weights 30/50/20%.

Paragraph 3 — Why this matters

Explain positive-part nonlinearity and use the €24.3m illustration to show that a 39% mean can produce zero loss while the distribution produces approximately €3.25m.

Do not introduce the exact 31.276954% loss-equivalent scalar here. It is a secondary concept.

Paragraph 4 — Practical implementation

State the preferred implementation: weighted deterministic-recovery CVA runs using existing scalar-recovery infrastructure, with targeted code adaptation if needed.

Paragraph 5 — Limits and verification

Briefly state that historical evidence supports severity rather than French probabilities, and identify the major remaining contractual and engine-verification gates.

Do not put the full historical event count, Greek coverage discussion, formula chain, recovery-equivalent derivation and detailed legal caveats into the executive conclusion.

Acceptance test: A reader should understand the decision and its justification after reading this page alone.

⸻

4. Section 1 — Transaction economics and liquidation value

Required reader takeaway

Bliq measures economically realizable and Dealer-creditable collateral resources, not simply the defaulted bond’s market price or eventual restructuring recovery.

Recommended sequence

1.1 Transaction structure

Introduce SPIRE XS3411888731, OAT€i FR0013209871, the pool, swap and Dealer claim.

Use one simple transaction-flow diagram.

Avoid extensive legal details at this stage.

1.2 What happens following a collateral credit event

Explain the distinction between:

* Default trigger.
* Claim impairment.
* Disposal.
* Receipt of proceeds.
* Waterfall allocation.
* Dealer credit.

State that these events may occur at different times.

1.3 What Bliq values

Define liquidation proceeds and Dealer-creditable resources.

Present the principal loss interface:

[
L_\tau^{Dealer}(k)

[V_\tau^{co}(k)-B_\tau^{avail}(k)]^+.
]

Explain the economic meaning of the positive part.

1.4 Why indexed-face recovery

Explain the choice of denominator in intuitive terms before presenting:

[
F_\tau=IR_{\tau^-}N_\tau^{pool},
\qquad
B_\tau^{liq}(k)=F_\tau R_k.
]

Include a concise comparison with market-value recovery.

Explain that the two representations can be equivalent when calibrated to the same euro proceeds, but that applying an unchanged recovery fraction to an unrelated performing market-value base is not equivalent.

Material to relocate

Move detailed Conditions 13(b)/(k), timing exceptions, indexation conventions and legal-claim verification into Appendix A.

Keep only the contractual conclusions needed to understand the model in the main section.

Retain the critical distinction between posted collateral and incremental waterfall proceeds.

⸻

5. Section 2 — Historical evidence and applicability to France

Required reader takeaway

Historical events help establish plausible distressed liquidation severity and dispersion, but cannot directly calibrate the probability distribution for a French OAT€i disposal.

Recommended sequence

2.1 What evidence is available

Briefly introduce the 19 confirmed Series 1 legacy bonds and seven sovereign episodes.

Use one compact historical summary table.

Explain Bloomberg BVAL indicative bids, the standardized event window and the limitations of execution inference.

2.2 What the evidence suggests

Discuss the broad price dispersion, distressed-price cluster and high-recovery observations.

Do not repeat every event’s detailed history.

Identify the main implications:

* Severe impairment is plausible.
* Distressed claims may retain material value.
* Market access and resolution conditions matter.
* The sample is not a French conditional probability distribution.

2.3 Which precedents matter for France

Preserve the two-dimensional comparison:

1. Institutional and legal similarity.
2. Empirical price relevance and coverage.

Explain why Greece is structurally relevant but numerically weak, and why better-covered non-euro-area episodes remain useful severity evidence.

2.4 French macroeconomic and institutional transmission

This section must be more substantive than v0.12.

Explain how the following channels could affect conditional liquidation recovery:

* French fiscal and political stress.
* Sovereign funding and market confidence.
* Euro-area official and institutional responses.
* Government-bond liquidity and dealer balance-sheet capacity.
* Repo and collateral-market functioning.
* Bank-sovereign feedback.
* OAT€i-specific claim and investor-base characteristics.
* SPIRE block-sale execution and timing.

Present the competing effects rather than assuming they all point in one direction.

France’s deep normal-market sovereign market could support realizability, while a French sovereign credit event could create systemic dislocation that impairs the same market infrastructure.

Tie these mechanisms explicitly to severe, orderly and resilient liquidation scenarios.

Do not assign arbitrary quantitative macro uplifts, regression coefficients or scenario probabilities.

Greek coverage footnote

Use the user-verified Bloomberg observations:

* 12 March 2012: 21 bid / 23.125 ask.
* 4 July 2012: 24.124 bid / 24.5 ask.

The July observation lies outside the selected 0–30 weekday window.

Explain that 6.5% coverage reflects sparse available Bloomberg observations, not proof that the bond did not trade.

Reconcile the reported 20.8 in-window mean with the two underlying valid observations and verify the “next available” wording before publication.

Place this explanation in a footnote near the historical table, rather than interrupting the main narrative.

Material to relocate

Put full ISIN lists, dates, event anchors, coverage calculations, alternative windows, restructuring NPV comparisons and detailed comparability tables in Appendices B and C.

⸻

6. Section 3 — Recommended recovery methodology

Required reader takeaway

The desk marks one recovery number; the model converts it into a governed distribution because the Dealer loss is nonlinear in liquidation proceeds.

This section should be the conceptual center of the paper.

Recommended sequence

3.1 Why a single deterministic recovery may be insufficient

Start with the economic intuition.

A recovery mean does not generally preserve expected loss when loss is subject to a positive-part threshold.

Use one compact inequality:

[
\sum_k w_k[V-FR_k]^+
\geq
[V-F\sum_k w_kR_k]^+.
]

Do not introduce the loss-equivalent scalar yet.

3.2 Recommended distribution

Present the three-state table:

State	Weight	Recovery
Severe distressed sale	30%	20%
Distressed orderly sale	50%	40%
Resilient sale	20%	65%

Explain briefly why three states are a reasonable parsimonious choice.

Do not claim that the data statistically identify exactly three regimes.

3.3 One trader-facing mark

Explain in plain language:

“The trader expresses a central recovery view through one mark. The model translates that mark into a governed range of liquidation outcomes.”

Then introduce the additive mapping:

[
R_k(R_{\mathrm{eff}})

R_k^0+(R_{\mathrm{eff}}-39%).
]

Explain that the weighted mean is constructed to equal (R_{\mathrm{eff}}).

The mean identity is a design feature, not an independent empirical calibration.

3.4 Calibration rationale and limitations

Explain the empirical support for 20%, 40% and 65%, then distinguish the expert-judgment weights.

Explicitly separate:

* Evidence supporting recovery dispersion.
* Evidence informing the approximate recovery levels.
* Expert judgment determining the scenario weights.

These are three different levels of evidentiary strength.

Do not imply that seven historical episodes determine French state probabilities.

Material to relocate

Move the complete calibration chain, alternative mappings, mathematical bounds, weight sensitivity and severe-recovery sensitivity to Appendices D and E.

⸻

7. Section 4 — Numerical illustration and model implications

Required reader takeaway

The recovery distribution changes the calculated loss materially in exposure regions where some recovery states produce loss and others do not.

Use the following illustration consistently:

[
N^{pool}=€51m,\qquad IR=1.3195,
]

[
F=€67.2945m,\qquad V=€24.3m.
]

The €24.3m is a supplied illustrative close-out present value, not a verified event-date production exposure.

The common-basis break-even recovery is approximately 36.11%.

Use one clear table showing:

* Recovery state.
* Probability weight.
* Proceeds.
* State loss.
* Weighted contribution.

The distribution produces approximately €3.25233m of expected loss.

Direct application of the 39% mean produces zero in this static illustration.

Explain this result before introducing any loss-equivalent recovery concept.

Loss-equivalent recovery

After establishing the nonlinearity, introduce the static loss-equivalent recovery:

[
R_{\mathrm{LE}}

\frac{V-\mathcal L_{\mathrm{dist}}}{F}
]

for the relevant positive-loss region.

State that approximately 31.28% reproduces the illustrative distributional loss for this exposure only.

It is not the desk mark and is not a universal production recovery.

Keep the detailed exposure-ratio sensitivity and mathematical boundary analysis in Appendix E.

Valuation-basis warning

Make explicit that the illustration puts exposure and collateral proceeds on a common present-value basis.

Production CVA must evaluate both consistently at the default/event date and discount loss once.

Do not imply that a static present-value illustration is itself a full CVA calculation.

⸻

8. Section 5 — XVA implementation, governance and limitations

Required reader takeaway

The proposed distribution can be implemented using repeated deterministic-recovery CVA runs; full pathwise recovery modeling is not an initial prerequisite.

5.1 Preferred implementation

State the practical workflow before presenting the formula:

1. Trader marks one (R_{\mathrm{eff}}).
2. Central configuration produces three recoveries and fixed weights.
3. The engine calculates CVA separately for each deterministic recovery.
4. The resulting CVAs are aggregated using the governed weights.

Then present:

[
\boxed{
CVA_{\mathrm{dist}}

\sum_k w_kCVA(R_k).
}
]

Explain that this preserves the nonlinear loss effect under the specified fixed mixture.

5.2 Existing engine adaptation

The existing infrastructure is confirmed to accept path-independent scalar recovery.

However, its current economic meaning and application point still require verification.

If it controls a different recovery quantity, implement a targeted change so that it controls the intended OAT€i collateral-liquidation proceeds.

The scalar must enter before the Dealer positive-part loss, without duplicating collateral credit or contractual LGD.

Do not imply that full stochastic recovery-path infrastructure is required.

5.3 Important limitations

Explain, without excessive repetition:

* Fixed weights do not capture all potential recovery/default-time dependence.
* A French crisis may create joint stress in rates, inflation, liquidity and exposure.
* State-dependent collateral claims or waterfall proceeds require additional treatment.
* The proposed mapping is a governed model assumption.
* Executed transaction terms and OAT€i claim treatment remain verification gates.

5.4 Implementation alternatives

Mention direct mean recovery only as a simplification where validated error is immaterial.

Mention portfolio-CVA-equivalent scalar inversion as a possible future run-cost optimization.

Do not give these alternatives equal prominence to the preferred weighted-run implementation.

5.5 Final recommendation

End Section 5 with a short, decisive recommendation of approximately one paragraph.

Do not restate the full executive conclusion or all seven historical episodes.

⸻

Part IV — Substantive verification and model caveats

9. Contractual and accounting checks

Preserve the SPIRE disposal and Dealer-credit analysis, including Conditions 13, 15 and 17 and the relevant original collateral provisions.

The 30 Reference Business Days liquidation period remains a working Series-specific term. Verify its precise commencement, calendar and applicable executed elections.

Distinguish:

* Authority to dispose.
* Practical ability to execute.
* Actual sale proceeds.
* Retained rights.
* Dealer entitlement to proceeds.

Do not treat delayed proceeds as automatically worthless where enforceable rights remain.

Avoid double counting posted collateral and incremental waterfall proceeds.

Preserve senior-cost treatment, including the provisional zero assumption where no separate amount has been established.

10. Exceptional non-disposal

Retain exceptional non-disposal as a separate contractual stress.

Do not make it a fourth ordinary liquidation state.

The hypothetical (q)-weighted stress remains an illustration, not an empirically calibrated probability.

Keep the details in Appendix D.

11. Face-value and market-value equivalence

Retain the mathematical denominator reconciliation in Appendix E.

The face-based choice should be justified by interpretability, calibration stability and consistency with the impaired claim, not by an assertion that market-value recovery is mathematically inferior.

12. Weighted-CVA exactness conditions

Preserve the conditions under which the weighted deterministic CVA calculation exactly represents the intended fixed discrete mixture.

Clarify that it does not automatically capture dependence of recovery on simulated market states or default timing.

Preserve the distinction between static loss-equivalent and portfolio-CVA-equivalent scalar recoveries.

Do not treat the illustrative 31.28% as a production-wide recovery input.

⸻

Part V — Appendix design and visual presentation

13. Appendix responsibilities

Use the appendices as a structured evidence and validation repository.

Appendix A — Contractual evidence

Executed-document gaps, conditions, liquidation rights, waterfall, collateral credit and OAT€i legal-claim verification.

Appendix B — Historical data and provenance

Raw source inventory, ISIN register, anchor dates, Bloomberg observation conventions, coverage and restructuring-outcome distinctions.

Appendix C — France applicability

Complete two-dimensional event comparability, historical selection sensitivities, French macro/institutional channels and indexed-face transfer qualifications.

Appendix D — Calibration rationale and sensitivities

Detailed evidence-to-parameter chain, recovery/weight rationale, severe-state sensitivity, timing sensitivity and exceptional non-disposal stress.

Appendix E — Mathematical and XVA validation

Positive-part nonlinearity, (R_{\mathrm{eff}}) mapping bounds, alternative mappings, state-dependent quantities, weighted-CVA conditions, loss-equivalent scalar and recovery-base reconciliation.

Appendix F — Governance and references

Source classifications, model assumptions, implementation verification, change controls and references.

Every appendix should be independently navigable, with descriptive subsection headings.

Avoid repeating the main-text conclusion at the beginning of every appendix.

14. Specific formatting requirements

Tables

Redesign the dense calibration-chain and governance tables.

Do not use long prose sentences in narrow table cells.

Prefer short phrases with detailed explanation immediately below the table.

Use readable font sizes, consistent numerical formatting and sufficient row spacing.

Avoid splitting a table across pages without repeated headers.

Equations

Keep the central economic and implementation equations in the main text.

Move derivations, special cases and alternative formulas to Appendix E.

Ensure every displayed equation is introduced and interpreted.

Visual hierarchy

Use a restrained professional style.

Maintain clear section and subsection hierarchy.

Avoid excessive bold text, boxed conclusions and repeated decorative emphasis.

Use whitespace deliberately to separate conceptual transitions.

Page layout

Check for:

* Overcrowded pages.
* Orphaned headings.
* Tables with compressed text.
* Inconsistent footnotes.
* Awkward equation breaks.
* Unnecessary blank space.
* Repeated content.
* Inconsistent notation.

The final PDF must be visually reviewed page by page, not merely compiled successfully.

⸻

Part VI — Final editorial and technical quality gates

15. Reader’s journey audit

After drafting v0.13, perform a second review as if encountering the paper for the first time.

At the end of each main section, ask:

Section 1: Do I understand what is being valued and why?

Section 2: Do I understand what the historical evidence supports, and what makes France different?

Section 3: Do I understand the proposed recovery model, why dispersion matters, and what the desk marks?

Section 4: Do I understand the numerical consequence of the recommendation?

Section 5: Do I understand how to implement it and what must still be verified?

If the answer requires reading an appendix or a later section, restructure the explanation.

16. Redundancy audit

Search the entire paper for repeated discussions of:

* Expert-judgment weights.
* Sparse Greek coverage.
* BVAL non-executability.
* Mean versus distributional recovery.
* Indexed-face legal treatment.
* Path-independent engine recovery.
* Recovery/default-time dependence.
* Executed-document verification.

Retain one substantive explanation of each topic and use brief cross-references elsewhere.

Do not remove necessary qualifications; eliminate duplicated qualifications.

17. Evidence-strength audit

For each important modeling statement, classify its support as:

1. Contractual/documentary evidence.
2. Observed market evidence.
3. Mathematical result.
4. Model assumption.
5. Expert judgment.
6. Outstanding verification.

Make sure the prose does not present categories 4–6 as categories 1–3.

In particular:

* Recovery dispersion is supported by observed historical variation.
* The exact French recovery levels are evidence-informed judgments.
* The 30/50/20% weights are expert judgment.
* The additive mapping is a model-design choice.
* Weighted deterministic CVA aggregation is a mathematical result under stated conditions.
* Production readiness depends on code and transaction-specific verification.

18. Recommendation-versus-verification audit

Separate three levels of conclusion:

Methodological conclusion: the recommended M0 recovery framework.

Implementation verification: the existing recovery scalar’s actual meaning and correct insertion point.

Transaction verification: executed SPIRE provisions, OAT€i claim treatment, collateral-credit and waterfall rights.

Do not describe every open verification item as though it invalidates the entire modeling methodology.

Equally, do not imply production approval before those checks are complete.

19. Numerical and source validation

Reproduce and verify:

* Historical event means and coverage.
* Greek March and July bid/ask observations and the 20.8 mean.
* Three-state mean of 39%.
* €67.2945m indexed face.
* Approximately 36.11% break-even recovery.
* Approximately €3.25233m distributional loss.
* Approximately 31.28% static loss-equivalent recovery.
* Mapping bounds and monotonicity.
* Recovery and weight sensitivities.
* All cited source descriptions and dates.

Do not silently replace any v0.12 statistic with an older version.

⸻

Part VII — Deliverables and acceptance criteria

Deliver:

1. Clean v0.13 PDF.
2. Updated editable source.
3. Concise revision log.
4. Reproducible calculation and validation artifacts.
5. Short list of outstanding contractual and implementation gates.

The revision log should identify substantive methodological changes separately from editorial restructuring.

Final acceptance criteria

The paper should be:

* Economically coherent: valuation object, recovery base and Dealer loss are clearly distinguished.
* Methodologically defensible: the recommendation follows from evidence, mathematical reasoning and explicit judgment.
* Operationally practical: one desk mark, governed scenarios and a realistic XVA implementation.
* Contractually disciplined: no unsupported assumption of sale rights, proceeds or collateral credit.
* Evidence-aware: observed data, assumptions and expert judgment are not conflated.
* Reader-friendly: the argument unfolds naturally, without repetition or unnecessary detours.
* Auditable: all supporting calculations, limitations and verification requirements remain accessible.

Final instruction

Do not simply shorten v0.12.

Rewrite it around the reader’s decision process.

The main document should tell one coherent story:

What are we valuing? → What can history tell us about France? → What model follows? → Why does it matter numerically? → How do we implement it?

The appendices should preserve the detailed evidence and mathematics that make that story defensible.

The final objective is a paper that is easy to read because its reasoning is well organized—not because its technical substance has been diluted.
