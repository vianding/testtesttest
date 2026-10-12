Task: Final Technical and Editorial Refinement — SPIRE CVA Methodology v1.1

Objective

Perform a final, focused improvement of Version 1.1 of Pricing CVA for a SPIRE Repackaging Swap: Sovereign Bond Collateral.

The latest rewrite has significantly improved the structure and mathematical coherence of the original paper. Do not restart the rewrite or redesign the overall architecture.

The remaining work is to:

1. Make the main derivation easier and faster for a technically sophisticated quant reader to follow.
2. Remove unnecessary legal exposition, repeated conclusions and research-stage commentary.
3. Tighten a few important technical statements concerning conditional default loss, collateral-realisation notation, engine integration and residual LGD.
4. Preserve the contractually justified core loss formula and its assumptions.
5. Ensure the resulting paper is concise, precise and implementation-oriented.

The intended reader is highly comfortable with derivatives pricing, CVA, stochastic modelling and scientific papers, but is not necessarily familiar with SPIRE’s legal structure.

Target: approximately 6–7 pages of main text, with supporting appendices. This is a soft target. Do not omit essential reasoning to achieve it.

The final document should read as a finished quantitative methodology, not a legal memorandum, research diary or collection of modelling caveats.

⸻

1. Preserve the current architecture and central result

Keep the current logical progression:

1. Transaction economics and source of exposure.
2. Contractual default, close-out claim, collateral access and Dealer loss.
3. Conditional collateral-realisation input.
4. CVA valuation.
5. Mapping into the existing XVA engine.
6. Worked example, assumptions and outstanding decisions.

This architecture works well. Keep it.

The central derived result remains, under the stated contractual and modelling assumptions,

[
L_\tau \approx
\left[
V_\tau^{co}-B_\tau^{liq}(N^{pool})
\right]^+.
]

The more general version retains senior deductions where relevant:

[
L_\tau =
\left[
V_\tau^{co}

\left(B_\tau^{liq}(N^{pool})-K_\tau^{sen}\right)^+
\right]^+,
]

subject to the contractual cap on resources and the precise treatment of the claim.

Do not modify these formulae merely for stylistic reasons. Reconfirm their conditions of validity and preserve the distinction between contractual identity and baseline approximation.

The paper’s principal contribution is the derivation of the loss from the contractual structure, not the selection of a sovereign recovery model.

Make this result prominent.

⸻

2. Significantly compress Section 2 without weakening the derivation

Section 2 remains the primary source of unnecessary length.

Its objective is to establish only three economically essential facts:

* The sovereign collateral credit event triggers termination.
* The Dealer’s claim is based on the Issuer’s scheduled swap obligations, not the defaulted bond’s market value.
* The Dealer can access the posted and unposted collateral through separate contractual routes, resulting in the whole-pool recovery expression under the relevant conditions.

Then derive the event loss.

2.1 Default trigger

Explain the Original Collateral Default trigger and its consequences concisely.

The reader needs to understand that the trigger produces:

* Early termination of the swap.
* Liquidation of the sovereign collateral.
* Application of proceeds through a contractual priority structure.

Mention that the trigger is a contractual credit-event definition and that the issuer’s credit curve is used to approximate its probability, with any event-definition basis risk acknowledged.

Move the detailed list of individual credit events and extended Condition 8(c)/13 discussion to Appendix A.

The forced-sale deadline should be mentioned because it matters to collateral-realisation modelling, but the full legal quotation is unnecessary in the main text.

2.2 Close-out claim

This subsection should be reduced substantially.

The essential reasoning is:

The Dealer is entitled to scheduled swap cashflows referenced to the Original Collateral. The Issuer’s obligation is not reduced simply because the sovereign collateral defaults.

Therefore, the close-out claim is valued from the contractual cashflow schedules, using the regular inflation-linked swap valuation framework.

The sovereign credit impairment belongs in collateral realisation, not in a reduction of the contractual claim.

Explain this in approximately three compact paragraphs and retain the equation defining the two close-out legs.

Avoid extensive discussion of hypothetical pass-through structures.

A single statement can note that a genuine pass-through obligation would require a different treatment and that the scheduled-obligation reading remains subject to confirmation against the executed Swap Agreement.

Move documentary quotations establishing scheduled obligations to Appendix A.

Avoid repeatedly explaining why sovereign credit should not widen the close-out discount curve. State the pricing principle once and preserve the required qualification concerning non-credit close-out adjustments.

2.3 Collateral access and no double-counting

Retain the two-route explanation because it is essential:

Route 1: Posted collateral is credited within the contractual close-out.

Route 2: The remaining collateral is available through the waterfall, after applicable senior deductions.

However, simplify the prose considerably.

The reader should immediately see that the posted collateral is credited once, not twice.

Introduce the Early Termination Amount only to the extent required for that derivation.

Then show how the contractual priority and the cap on the remaining Dealer claim produce whole-pool access.

Move detailed tier enumeration and quotations from Condition 15 into Appendix A.

Preserve the qualifications concerning senior deductions and possible Series-level waterfall overrides.

Do not conflate legal entitlement to available proceeds with ownership of the entire pool.

2.4 Event loss

Keep this subsection short.

Present the general loss formula, then the simplified baseline.

Explain the positive-part operator and the role of limited recourse.

Conclude with one sentence explaining structural wrong-way dependence: the sovereign event generates the claim while simultaneously impairing the asset available to meet it.

Do not repeat this derivation later.

⸻

3. Improve collateral-realisation notation and interface precision

Section 3 is conceptually good, but the notation needs refinement.

The paper currently uses the same (R_\tau^{liq}) fraction against different recovery bases.

For example:

[
B_\tau^{liq}=R_\tau^{liq}IR_\tau N
]

and

[
B_\tau^{liq}=R_\tau^{liq}B_\tau^{proxy}.
]

Although the paper notes the denominator difference, this notation invites confusion.

Use recovery-fraction notation that explicitly distinguishes the denominator.

For example:

[
B_\tau^{liq}(N)=R_\tau^{FV}IR_\tau N
]

for the direct face-value construction, and

[
B_\tau^{liq}(N)=R_\tau^{MV}B_\tau^{proxy}(N)
]

for the stressed-curve proxy.

Use the same discipline for the credit-aware construction.

Review the companion B_liq paper and choose notation that is consistent across both documents.

Do not confuse:

* Sovereign recovery of principal.
* Fraction of a pre-default or proxy bond value recovered.
* Post-default liquidation proceeds.
* Residual LGD on the Dealer’s extinguished claim.

Also distinguish a constant recovery-fraction parameter from a state-dependent recovery fraction.

Model construction descriptions

Keep the three descriptive construction names already introduced:

* Direct FV realisation.
* Stressed-curve proxy.
* Credit-aware bond valuation.

Avoid reintroducing M0/M1/M2 or S0/S1/S2/S3 into the main text.

Retain the legacy-label mapping only in the appendix if useful for traceability.

Clearly distinguish:

* The methodology preferred by the companion B_liq paper.
* The approximation currently executable in production.
* More sophisticated future refinements.

Do not imply that the recommended methodology and the current production configuration are necessarily identical.

Timing and valuation basis

Specify precisely what (B_\tau^{liq}) represents.

Collateral may be sold after the termination event, during the contractual liquidation period.

The CVA calculation nevertheless requires collateral resources expressed on a valuation basis consistent with the close-out claim and the event-loss formula.

Verify whether (B_\tau^{liq}) represents:

1. Proceeds actually realised at a later date.
2. The value of those future proceeds assessed at the close-out or event date.
3. An immediate event-state recovery approximation.

Do not silently equate these three quantities.

If the current production model approximates delayed realisation by an immediate event-state amount, state this explicitly as an approximation and indicate how liquidation timing could later be incorporated.

Keep detailed modelling and calibration in the companion B_liq methodology.

⸻

4. Correct the treatment of wrong-way dependence in Sections 4 and 5

This is the most important technical refinement.

Version 1.1 correctly identifies that the existing engine uses deterministic survival-curve weights and computes pathwise market-factor exposures.

However, the statement that the dominant wrong-way effect is captured “exactly” is too strong without qualification.

Distinguish three concepts.

A. Event-conditioned collateral severity

The collateral-realisation model explicitly evaluates the collateral under a sovereign default/restructuring state.

This is important structural dependence.

The event damages the collateral value used in the loss calculation.

B. Dependence between market state and default timing

The mathematically general CVA expression requires the joint expectation

[
CVA_0=
\mathbb E^{\mathbb Q}
\left[
D(0,\tau)L_\tau
\mathbf 1_{{\tau\leq T}}
\right].
]

At bucket level:

[
CVA_0\approx
\sum_i
\mathbb E^{\mathbb Q}
\left[
D(0,t_i)L_{t_i}I_i
\right].
]

If the engine instead computes

[
\sum_i q_i
\mathbb E^{\mathbb Q}
\left[
D(0,t_i)L_{t_i}^{event}
\right],
]

with (q_i) determined independently of the simulated market-factor paths, this is an approximation to the full joint expectation.

It does not generally capture dependence between the sovereign’s default intensity and the simulated rates, inflation or other market factors.

Make this distinction explicit.

C. Wrong-way-risk capability of the existing engine

Check the actual implementation to determine:

* Whether the default weighting entity is the sovereign collateral issuer.
* Whether its default weights are deterministic.
* Whether any conditional-default or common-factor overlay is activated.
* Whether the collateral value is genuinely evaluated in the required default state.
* Whether the resulting event loss enters the positive-part calculation pathwise.
* Whether discounting is included within the pathwise expectation.
* Whether an additional LGD multiplier is already applied through the default weight.

Present these as established findings only where verified against source code.

If configuration or code paths remain uncertain, label them as implementation checks.

Required wording change

Replace statements such as:

“The dominant wrong-way effect is nonetheless exact.”

with something more precise, along the lines of:

“The event-state collateral impairment is represented explicitly in the loss calculation. The baseline does not, however, capture all dependence between sovereign default timing and simulated market factors, because default weights are deterministic. This additional dependence may be introduced through the engine’s conditional-default or hazard-weighting capabilities if material.”

Adjust this wording to match verified engine behavior.

The key point is that correct event-state loss modelling is not automatically equivalent to exact joint default/exposure modelling.

Do not describe the entire implementation as either fully wrong-way-risk-consistent or wholly independent. Explain exactly which dependence it captures and which it approximates.

Also, do not introduce an unnecessarily complicated new simulation architecture. Retain the simplest implementation justified by the current infrastructure.

⸻

5. Finalise LGD and senior deduction treatment

Section 5.2 is useful and should remain, but it can be shorter.

The distinction is:

[
K_\tau^{sen}
]

represents prior-ranking costs that reduce collateral proceeds reaching the Dealer.

Residual LGD represents the fraction of the remaining unpaid Dealer claim that constitutes loss after application of those proceeds.

They are different quantities and cannot substitute for one another.

Baseline treatment

Use:

* (K_\tau^{sen}=0) as an approximation, subject to materiality.
* Contractual residual LGD = 100%, based on extinguishment of the remaining claim.
* Desk convention of 99% LGD as an optional approximation if required for consistency with existing practice.

Do not claim 99% LGD is a mathematical proxy for senior deductions.

The 99% assumption must be presented as a desk convention, not a contractual conclusion.

Engine integration

The existing engine apparently already incorporates a factor ((1-R)) into the CVA aggregation weight.

Verify exactly what (R) represents and how it is configured.

If the exposure input already represents a post-waterfall dollar loss, the aggregation should not unintentionally apply another sovereign-bond recovery or generic counterparty recovery.

Where required, distinguish the entity used for default-probability calibration from the recovery/severity convention used to scale the final Dealer residual loss.

If the two cannot be independently configured, identify this as an implementation issue.

Keep the discussion concise, with one short numerical or algebraic clarification if necessary.

⸻

6. Reduce repetition throughout the document

Perform a deliberate duplication audit.

The following conclusions should each have one primary explanation:

1. Sovereign collateral default triggers termination.
2. The contractual claim does not fall merely because the sovereign bond defaults.
3. The Dealer reaches the whole pool through two routes.
4. Posted collateral must not be credited twice.
5. Limited recourse extinguishes the residual claim.
6. Sovereign collateral recovery and residual claim LGD are different.
7. The default event creates structural dependence between the claim and collateral.
8. The B_liq construction is separate from the CVA derivation.

Later sections may reference those conclusions but should not re-derive them.

In particular:

* Do not repeat the contract rationale in Section 5.
* Do not repeat the whole-pool explanation in the conclusion.
* Do not repeat the detailed distinctions between recovery conventions after Section 3.
* Avoid a long Section 6.3 summary restating Sections 1–5.

A concise concluding paragraph is sufficient.

Also reduce expressions such as “not a modelling assumption”, “not a wash”, “not a second credit adjustment”, and similar defensive formulations when the positive explanation has already established the result.

Use such contrasts only where they prevent a genuinely likely implementation error.

⸻

7. Improve the worked example

Keep the worked example because it gives the methodology tangible economic meaning.

Retain the essential inputs:

* Total pool base notional.
* Index ratio.
* Dealer close-out claim.
* Collateral-realisation assumptions.
* Break-even realisation fraction.
* Resulting loss under the illustrative scenarios.

The 29.3% break-even realisation is particularly valuable.

However, consider reducing the detailed performing-state CSA posting calculations unless needed to illustrate a specific point.

The critical intuition is that once whole-pool access is established, the default-state Dealer recovery is not capped at the amount of collateral previously posted.

The worked example should demonstrate this result, not reproduce every intermediate accounting quantity.

Clearly distinguish illustrative dated values from production valuations.

Use one compact table and minimal accompanying prose.

⸻

8. Make the open-items section decision-oriented

Replace the existing long list with a small prioritised table.

Use categories such as:

* Contractual confirmation.
* Model/calibration decision.
* Engine integration.
* Optional refinement.

Only include issues that materially affect the pricing result, the loss formula or the ability to implement it.

For each issue, identify:

* What must be confirmed or decided.
* Why it matters.
* Whether it blocks the core derivation, production implementation or only a refinement.

Prioritise the following:

1. Scheduled-obligation interpretation and executed Swap Agreement.
2. Possible waterfall override.
3. Engine integration of conditional event loss and default weights.
4. B_liq construction and calibration.
5. Correct treatment of residual LGD and recovery in aggregation.
6. Senior waterfall costs if material.
7. Close-out and liquidation timing if material.

Move secondary implementation checks into a separate engineering checklist rather than displaying them all in the methodology.

Avoid leaving research markers scattered throughout the main text.

⸻

9. Improve prose and presentation

The target is scientific clarity and professional simplicity.

Apply these editing rules:

Prefer concise affirmative statements.

Explain what the model does and why, rather than repeatedly explaining what it does not do.

Use contractual language sparingly.

Introduce the contractual mechanism in ordinary financial terms, then cite the relevant Condition. Keep quotations in the appendix.

Keep mathematical transitions explicit.

Do not remove the reasoning connecting the close-out, recovery resources and final loss simply to save space.

Avoid excessive notation.

Every new variable should represent a genuinely distinct quantity. Define it once, when introduced.

Use tables selectively.

Tables are useful for the three collateral constructions, the numerical example and material open items. Do not add tables merely for decorative structure.

Do not turn the paper into presentation slides.

It must remain a continuous scientific methodology with coherent paragraphs, derivations and references.

No unnecessary new sections or modelling discussions.

Technical questions identified during the revision should lead to a correction, a concise qualification or an open item—not an expanded literature review.

⸻

10. Quality and consistency verification

Before finalising, check:

Contractual consistency

* Correct trigger and automatic termination.
* Scheduled-obligation interpretation.
* Treatment of posted collateral inside close-out.
* Whole-pool access and senior priority deductions.
* Limited recourse and extinguishment.

Quantitative consistency

* Indexation and base-notional conventions.
* Recovery denominators.
* Event-state versus pre-default valuation.
* Timing basis for liquidation proceeds.
* Pathwise positive-part operation.
* Event probabilities and conditioning.
* Discounting.
* Residual LGD and avoidance of double-counting.

Implementation consistency

* Correct default weighting entity.
* Accurate description of deterministic versus conditional default weights.
* Correct handling of event-state collateral realisation.
* Correct recovery/severity configuration.
* No duplicate collateral netting.
* No unnecessary stochastic bond model added.

Cross-document consistency

* Shared notation for B_liq.
* Common terminology for recovery constructions.
* Clear separation between current production baseline and preferred methodology.
* No conflicting claims between the CVA and companion methodology.

Do not silently change a material modelling assumption to fix a wording problem.

⸻

Deliverables

A. Revised LaTeX paper

Produce Version 1.2 of the CVA methodology.

Preserve the current architecture, core equations and essential contractual conclusions.

Target approximately 6–7 pages of main text and concise supporting appendices, without forcing artificial compression.

B. Short revision summary

Provide a concise summary of:

* Main prose and structural cuts.
* Any substantive mathematical or modelling corrections.
* Notation changes and required companion-paper alignment.
* Remaining important unverified assumptions.
* Final main-text and appendix page counts.

C. Technical issues log

List any issue discovered during the final review that could affect the pricing result or implementation.

Separate actual inconsistencies from optional future enhancements.

Do not expand the main paper to discuss every open technical issue.

⸻

Final acceptance criteria

The paper should satisfy the following test:

An experienced XVA quant reading it for the first time should be able to understand, without consulting the appendices:

1. Why the transaction creates credit exposure.
2. What event triggers a loss.
3. How the Dealer’s close-out claim is valued.
4. How the Dealer reaches the available collateral.
5. Why the whole-pool loss formula follows.
6. What the B_liq model must provide.
7. How the CVA is calculated using the existing engine.
8. Which approximations remain material.

Every essential mathematical step should be defensible.

Every material approximation should be identifiable.

The reader should not have to decode legal terminology, navigate repeated arguments or read the companion methodology to follow the core CVA calculation.

Guiding principle: Explain the economic mechanism once, derive the mathematical result cleanly, and map it into production without adding unnecessary machinery.

This is a refinement pass, not a new research project. Preserve what already works and focus effort on clarity, correctness and concision.

Additional requirement: Treat this as a first-release, self-contained methodology

An important clarification: neither the previous CVA drafts nor the companion B_liq methodology has been formally released or shared with the intended readers.

Write this document as though it is the first and only version the reader has ever seen.

Apply this requirement throughout the main text, appendices, terminology, references and document metadata.

1. Remove development-history references

Delete references to:

* Legacy labels, including historical M0/M1/M2 and S0/S1/S2/S3 mappings.
* Previous drafts or changes in notation.
* Earlier modelling recommendations or disagreements between documents.
* Changes from prior methodology versions.
* Internal research history that does not contribute to the final quantitative argument.

In particular, remove Appendix C.1’s legacy-label mapping entirely.

Do not replace it with another historical mapping table.

Adopt one coherent set of descriptive model names and mathematical notation from the beginning, as though these definitions were introduced for the first time.

2. Make the CVA paper self-contained

The companion Default-State Collateral Realisation Methodology is still under development.

The CVA paper must therefore define the collateral-realisation quantity, its economic meaning, valuation basis, required inputs and mathematical interface sufficiently for the reader to understand and implement the CVA methodology.

It may briefly identify possible realisation constructions, but must not require the reader to consult an unpublished document to understand the core argument.

Do not write statements such as:

* “The companion paper recommends…”
* “The companion methodology’s labels are adopted here…”
* “The two papers now use…”
* “As established in the previous version…”

Instead, present the selected definitions and modelling framework directly.

If a separate collateral-realisation methodology is eventually published, it can be cited or cross-referenced at that time.

3. Distinguish model status without exposing development history

It remains useful to distinguish:

* The current executable implementation.
* A conceptual benchmark.
* Potential future enhancements.
* Model assumptions and calibration decisions that remain unresolved.

However, present these as current methodological facts or outstanding decisions, not as a narrative of how the research evolved.

For example, “The current implementation uses a stressed-curve proxy” is appropriate if verified.

“The companion paper recommends M0 while the earlier CVA paper used M1” is not appropriate.

4. Clean up references and appendices

Remove unpublished companion documents from the formal references section unless they are genuinely available to the intended reader.

Do not cite an inaccessible internal working draft as the authority for a core assumption or formula.

Retain primary contractual documents, relevant published literature and independently verifiable sources.

The useful substantive material in Appendix C may remain if necessary, but reorganise it without any legacy terminology or development-history discussion. Consider retaining only concise recovery evidence, or remove Appendix C entirely if it adds little to the CVA derivation.

5. Keep internal traceability outside the reader-facing paper

You may retain the old-to-new notation mapping, changes from previous drafts and cross-document consistency checks in your separate revision summary or internal working notes.

None of this belongs in the methodology PDF.

Also remove any unnecessary version-comparison language from the title page. The document can carry a controlled document version if required for governance, but its prose should not imply the reader has encountered earlier versions.

Final editorial test

Read every paragraph and ask:

“Would this statement make complete sense to an experienced quant who has never seen our earlier drafts, internal discussions or unfinished B_liq work?”

If not, either explain the underlying concept independently or remove the statement.

The final methodology should stand entirely on its own, with no visible research-history baggage.
