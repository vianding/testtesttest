Task: Major Refactoring of the SPIRE CVA Methodology Paper

Objective

Rewrite the existing working paper, Pricing CVA for a SPIRE Repackaging Swap: Sovereign Bond Collateral, into a substantially shorter, clearer, technically rigorous methodology document suitable for a quantitative finance audience.

The current paper is approximately 28 pages including appendices. It contains substantial quantitative and contractual analysis, but the main argument is difficult to follow because of excessive length, repeated explanations, legal terminology, and the interleaving of modelling choices, contractual evidence and implementation details.

The intended reader is technically sophisticated and comfortable with scientific papers, stochastic modelling, derivatives pricing and CVA. However, the reader should not need to be a specialist in SPV legal documentation to understand the methodology.

This is a structural rewrite, not a copy-editing exercise.

Preserve the essential economics, mathematical derivations, contractual conclusions, modelling assumptions, implementation requirements and genuinely unresolved issues. Remove repetition, simplify language, reorganise the derivation and relocate supporting material where appropriate.

Do not sacrifice correctness or introduce unjustified simplifications to achieve brevity.

Inputs and reference material

I will provide:

1. The existing SPIRE CVA working paper, preferably its LaTeX source.
2. The separate Default-State Collateral Realisation / B_liq Methodology and any related analysis.
3. Existing findings on the XVA engine’s simulation, exposure calculation, event grids, discounting and CVA aggregation.
4. Relevant source-code references, where available.

Read these materials before producing the rewrite. Where information is unavailable, identify it as an open question rather than inventing an implementation detail.

The CVA and B_liq documents must remain separate but logically and notationally consistent.

1. Reconstruct the core quantitative argument

The rewritten document should tell one continuous story:

Transaction economics → loss-triggering event → contractual close-out claim → available collateral proceeds → Dealer loss → CVA calculation → implementation.

Explain each step once, in the order the reader needs it.

The central questions are:

* What event creates a potential Dealer loss?
* How is the Dealer’s contractual claim determined at that event?
* What collateral proceeds are available to satisfy the claim?
* What happens to any shortfall?
* How is the resulting loss priced under the risk-neutral measure?

The paper must make the distinction between the Dealer’s contractual claim and the economic value of the defaulted collateral particularly clear.

Keep the essential mathematical derivation, including the dependence between the sovereign default event, swap value and collateral recovery.

Avoid interrupting the derivation with extensive legal quotations, recovery literature or implementation caveats.

2. Verify and simplify the CVA formulation

Independently review the existing loss and CVA expressions, including:

* The definition and timing of the Original Collateral Default event.
* The gross swap close-out claim.
* Treatment of posted collateral within the contractual close-out.
* The Dealer’s access to the remaining collateral pool through the waterfall.
* Senior deductions and the limited-recourse constraint.
* The resulting event-state loss.
* Discounting and risk-neutral expectation.
* Wrong-way dependence between default timing, close-out value and collateral realisation.

The existing baseline can be expressed schematically as

[
L_\tau =
\left[V_\tau^{co}-B_\tau^{liq}(N^{pool})\right]^+,
]

with appropriate adjustments for senior waterfall deductions where required.

Treat this as a candidate derived result, not an assumption that must be defended.

Verify the exact conditions under which the whole-pool simplification is valid. In particular, check the contractual treatment of posted collateral and ensure that collateral is not double-counted or omitted.

If a more general formulation is needed, show it first and then derive the production approximation under explicitly stated assumptions.

For CVA, consider the event-time representation

[
CVA_0 =
\mathbb E^{\mathbb Q}
\left[
D(0,\tau)L_\tau
\mathbf 1_{{\tau\leq T}}
\right].
]

Then derive the discretisation that is most natural for our existing XVA engine.

Do not automatically assume that a generic bucketed CVA formula is the best implementation representation. Examine how the engine actually generates default probabilities, exposure profiles, discount factors and event-dependent losses.

Pay particular attention to whether the implementation requires:

* An explicit simulated default time.
* Bucketed conditional default losses.
* Hazard-weighted exposure integration.
* A common-factor conditional expectation.
* An existing CVA aggregation interface.

Explain the relationship between the mathematically correct continuous-time expression and the chosen engine-compatible approximation.

Do not replace joint expectations with products of unconditional expectations unless the independence approximation is explicitly justified.

Also check whether event-time valuation, close-out timing, liquidation timing and discounting are handled consistently.

3. Align with the existing XVA engine

Use our recent XVA-engine analysis to examine the production workflow.

Specifically establish:

1. Which market risk factors and relevant dates are simulated.
2. How the regular swap pricing engine calculates future pathwise values.
3. How the close-out value at the contractual termination event can be obtained.
4. How default probability or intensity information enters CVA aggregation.
5. Where the conditional collateral-realisation calculation belongs.
6. Whether the existing engine can handle event-dependent collateral recovery and wrong-way risk without introducing an unnecessary new stochastic bond-price process.

Distinguish clearly between:

* Simulation dates required for market-factor evolution and product valuation.
* Contractual event dates that affect cashflows or exercise.
* Default-event timing used for CVA.
* Post-default liquidation timing.

Do not imply that every contractual date requires an independently simulated market node.

Similarly, do not introduce a dedicated stochastic sovereign bond-price process unless it is genuinely required by the selected collateral-realisation model.

The implementation should reuse existing engine capabilities wherever possible.

Where the source code does not settle a question, identify the necessary investigation and do not present a proposed architecture as an established fact.

4. Keep collateral realisation modular

We have not finalised the default-state collateral-realisation methodology.

The CVA paper should therefore depend on an abstract conditional-realisation quantity, provisionally

[
B_\tau^{liq},
]

representing the proceeds attributable to the relevant collateral after the sovereign credit event and applicable liquidation process.

The main CVA derivation should not depend on committing to a particular RFV, RMV, proxy-discount-curve or stochastic credit-aware bond valuation model.

Summarise the current alternatives only to the extent needed to understand the interface:

* M0: recovery-of-face / index-adjusted principal benchmark.
* M1: stressed-curve bond-valuation proxy, the current practical baseline.
* M2: richer credit-driven, state-dependent bond valuation.

For each, a short description and mathematical expression should be sufficient.

Detailed model construction, calibration, recovery conventions, empirical sovereign restructuring evidence, spread decomposition and validation should remain in the separate B_liq methodology.

The CVA paper should specify what information it requires from the collateral-realisation model, rather than prescribing prematurely how that model must be built.

Preserve the distinction between pre-default bond value, default-state collateral value and realised liquidation proceeds wherever it is economically material.

Also preserve the distinction between sovereign collateral recovery and loss severity on an extinguished residual claim.

5. Harmonise notation across CVA and B_liq

Review the symbols used in both documents and propose one coherent notation standard.

In particular, examine:

* Default time and event indicators.
* Market state variables.
* Regular swap value versus contractual close-out claim.
* Base notional, index-adjusted principal and posted notional.
* Pre-default bond value.
* Default-state bond or restructuring-package value.
* Realised liquidation proceeds.
* Collateral resources available to the Dealer.
* Senior waterfall deductions.
* Event-state loss and CVA.

Use different symbols only where there is a genuinely different economic object.

Avoid creating several symbols for the same quantity or using one symbol for different recovery bases.

Where a notation change would affect the B_liq paper, provide a concise cross-document mapping.

Prefer notation that works naturally with the implementation and can accommodate future extensions.

6. Dramatically improve readability

Write in clear, precise English suitable for quants.

Avoid unnecessary legalistic phrases, repetitive qualifications, rhetorical questions and long explanations of points already established.

For every major contractual provision, explain its economic consequence in ordinary financial language. Cite the legal basis without reproducing several paragraphs of legal mechanics in the main derivation.

For example, the reader should understand quickly that a specified sovereign credit event automatically terminates the swap, the Dealer has a close-out claim, the collateral is liquidated, the Dealer receives proceeds according to a defined priority, and any unpaid residual is extinguished.

The detailed documentary basis can be referenced separately.

Use equations when they clarify a relationship; use prose when an equation would merely restate a definition.

Do not repeat the same conclusions in the introduction, intermediate sections, implementation discussion and conclusion.

Every paragraph should serve a distinct purpose.

Ensure that the transition from one section to the next is natural. The reader should never need to jump ahead several sections to understand a symbol or return repeatedly to recover a key assumption.

7. Suggested document architecture

You may improve on this structure, but aim for a compact main text along the following lines:

1. Overview and transaction economics

A concise description of SPIRE, the swap cashflows, the sovereign collateral and the source of Dealer credit exposure.

2. Contractual default and Dealer loss

Explain the triggering event, termination, contractual claim, collateral access, waterfall and limited recourse. Derive the loss expression.

3. Collateral realisation

Define the required conditional-realisation input, explain the modelling alternatives briefly and establish the interface to the separate B_liq paper.

4. CVA valuation

Present the continuous and discretised formulae, dependence structure, required inputs and model assumptions.

5. Production implementation

Map the methodology into the existing simulation, swap pricing, recovery calculation and CVA aggregation workflow.

6. Numerical illustration and outstanding decisions

Keep one compact worked example demonstrating the key quantities, including the whole-pool effect and sensitivity to recovery. Summarise only unresolved questions that could materially change pricing or implementation.

Appendices

Include only supporting material that is necessary to audit the core derivation or substantiate critical contractual conclusions.

Aim initially for approximately 8–12 pages of main text, potentially fewer if the argument can be expressed clearly without sacrificing essential detail. This is a guideline, not a mandatory limit.

8. Decide what to keep, move or discard

Do not simply transfer every existing paragraph into the new structure.

Review the original paper section by section and classify material as:

Keep in main text

* Essential economic definitions.
* Core contractual conclusions.
* Equations and necessary derivations.
* Assumptions affecting the pricing result.
* Production methodology and material limitations.

Move to appendix

* Detailed contractual support and references.
* Evidence required to verify significant assumptions.
* Supporting numerical calculations.
* Technical qualifications that are important but interrupt the main argument.

Move to companion B_liq document

* Detailed recovery modelling.
* Alternative default-state bond valuation constructions.
* Recovery calibration and empirical evidence.
* Sovereign restructuring case studies.
* Model-specific validation.

Delete

* Repeated explanations.
* Redundant formulae.
* Historical discussion not needed for the conclusion.
* Unnecessary defensive prose.
* Secondary observations that neither support the derivation nor affect implementation.

Do not retain material merely because it required substantial effort to develop originally.

9. Preserve genuine uncertainty

Distinguish clearly between:

* Contractual facts established by reviewed documentation.
* Modelling assumptions.
* Engineering choices.
* Approximations requiring validation.
* Unresolved documentary or implementation questions.

However, avoid littering the narrative with repeated MODEL CHOICE and TO CONFIRM markers.

Prefer a concise assumptions-and-open-items table near the end, with references in the main text where needed.

Prioritise open questions according to whether they could change:

1. The fundamental loss formula.
2. The numerical CVA result.
3. The implementation architecture.
4. Only a secondary modelling refinement.

Do not present tentative contractual interpretations as established facts.

10. Quality assurance

Before finalising, check the rewritten methodology against the original paper for:

* Missing essential economic arguments.
* Unsupported changes in contractual interpretation.
* Inconsistent notional or inflation-index conventions.
* Incorrect treatment of posted collateral.
* Double-counting of collateral recovery or sovereign credit risk.
* Incorrect handling of the limited-recourse loss severity.
* Confusion between pre-default value and post-default recovery.
* Inconsistent close-out, default and liquidation dates.
* Unsupported assumptions about XVA-engine capabilities.
* Symbols introduced before definition.
* Redundant explanations or unnecessary cross-references.

Pay special attention to the simplifications behind the final Dealer loss formula. Preserve those simplifications only where justified.

Deliverables and workflow

Phase A — Architecture and audit

First provide:

1. A concise diagnosis of the current paper’s structural and technical problems.
2. A proposed replacement table of contents, with approximate page allocation.
3. A keep / move / delete mapping from the original sections.
4. The proposed core mathematical derivation and notation standard.
5. Any technical issues that must be resolved or explicitly qualified before the rewrite.

Do not begin the full rewrite until this architecture has been reviewed.

Phase B — Complete rewrite

After approval, produce a clean, self-contained LaTeX methodology paper with consistent notation, numbered equations, coherent cross-references, concise appendices and a compact list of material assumptions and open items.

Retain citations to important contractual provisions and external research where relevant, but do not invent evidence or citations.

Phase C — Verification

Perform a final comparison with the original document and report:

* Significant content removed or relocated.
* Changes to derivations or model assumptions.
* Any outstanding contractual or production questions.
* Any consequential inconsistency between the CVA and B_liq methodologies.

Guiding principle

Derive first. Model only what survives the derivation. Explain it once, clearly.

The final paper should be something an experienced quant can read from beginning to end, understand the methodology and its implementation, and identify the remaining uncertainties without needing to decode extensive legal language or reconstruct an argument scattered across multiple sections.
