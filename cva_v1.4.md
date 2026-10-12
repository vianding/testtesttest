SPIRE CVA Methodology — Version 1.4

Final Technical Tightening and Readability Improvement

Objective

Update Version 1.3 of Pricing CVA for a SPIRE Repackaging Swap: Sovereign Bond Collateral to Version 1.4.

The methodology has largely converged. Its contractual derivation, core loss formula, modular collateral-realisation interface and XVA engine mapping should remain intact.

This is a precision-and-readability pass, not a new methodology development exercise.

The objectives are to:

1. Address the remaining technical issues identified in the v1.3 review.
2. Tighten the mathematical definitions and assumptions without introducing unnecessary modelling.
3. Improve readability and narrative flow for an experienced quant reader.
4. Remove remaining repetition, excessive qualifications and legalistic language.
5. Improve equations, tables and overall PDF layout.
6. Preserve a clean boundary between the CVA methodology and the future collateral-realisation methodology.

Do not expand the paper merely to explain every possible alternative.

The preferred outcome is a slightly shorter, more polished and technically sharper document.

Retain the existing structure and aim for approximately 7–8 pages of main text, with concise supporting appendices. This is a soft target; mathematical clarity takes priority over page count.

⸻

1. Preserve the core CVA methodology

The following elements are substantially settled and should not be reworked without identifying a genuine technical inconsistency:

* Transaction economics and sovereign collateral’s role.
* Original Collateral Default as the contractual event.
* Scheduled-obligation interpretation of the swap close-out.
* Posted-collateral credit within the Early Termination Amount.
* The separate waterfall route for the unposted collateral.
* Whole-pool access under the documented assumptions.
* Limited recourse and extinguishment of the residual claim.
* The event-state loss and CVA expectation.
* Deterministic default-weight approximation in the existing engine.
* Modular definition of B_liq.

The core baseline result remains:

[
L_\tau \approx
\left[
V_\tau^{co}-B_\tau^{liq}(N^{pool})
\right]^+.
]

The result is conditional on the scheduled-obligation interpretation, the waterfall priority, consistent treatment of posted collateral and the assumptions concerning senior deductions.

Preserve the more general contractual resource formulation where needed to make these assumptions auditable.

Do not present the baseline approximation as an unconditional contractual identity.

⸻

2. Clarify conditional expected liquidation proceeds versus expected loss

This is the highest-priority technical issue for v1.4.

Section 3.1 currently defines event-date collateral realisation using the conditional expectation of future liquidation proceeds:

[
B_\tau^{liq}(N)

\mathbb E_\tau^{\mathbb Q}
\left[
D(\tau,u)\Pi_u^{liq}(N)
\mid \text{event information}
\right].
]

This is an appropriate event-date valuation of the future proceeds under the specified assumptions.

However, the Dealer’s loss is nonlinear because of the positive-part operation.

In general,

[
\left[
V_\tau^{co}

\mathbb E_\tau^{\mathbb Q}(Y)
\right]^+
\neq
\mathbb E_\tau^{\mathbb Q}
\left[
(V_\tau^{co}-Y)^+
\right],
]

where (Y) represents appropriately discounted future liquidation proceeds.

By Jensen’s inequality, the loss calculated using expected proceeds may be lower than the conditional expected pathwise loss when the contractual claim is fixed at the conditioning state.

Required investigation

Determine the precise interpretation of B_liq under the current methodology:

* Is it the conditional event-date valuation of future liquidation proceeds?
* Is it a deterministic event-state approximation of realised proceeds?
* Or does it represent a recovery random variable whose outcomes are simulated pathwise?

Then determine how the positive-part loss calculation should be interpreted.

Distinguish:

Baseline approximation

The engine evaluates an event-state expected realisation amount, then applies the positive-part loss.

Richer treatment

The loss is evaluated across the relevant post-event liquidation outcomes before taking the conditional expectation.

Explain that the approaches are not generally equivalent.

Required treatment in the paper

Keep the current B_liq interface if it remains appropriate for the baseline.

Add only a concise qualification explaining:

* What uncertainty is integrated out in the realisation value.
* What uncertainty remains pathwise in the CVA calculation.
* Why the current approximation is practical.
* Under what circumstances omitted post-default liquidation variability could be material.

Do not claim the approximation has been empirically validated unless supporting evidence exists.

Do not introduce a new stochastic post-default liquidation engine or derive an elaborate alternative model.

If necessary, record the effect as an open model-validation item.

Important: This distinction must not become another major section. Prefer two or three technically precise sentences, supported by one equation only if essential.

⸻

3. Verify the B_liq interface and senior-deduction boundaries

The CVA paper should continue to own:

* Contractual waterfall and priority.
* Senior costs and deductions.
* Dealer claim.
* Residual loss.
* CVA integration.

The collateral-realisation component should own:

* Default-state collateral valuation.
* Expected liquidation proceeds.
* Recovery base and fraction.
* Model construction and calibration.

Gross versus net proceeds

Verify that (B_\tau^{liq}) is defined before the senior waterfall deductions represented by (K_\tau^{sen}).

Check that any recovery input based on market prices, restructuring packages or observed liquidation outcomes does not already deduct the same transaction-specific senior costs.

Avoid double counting.

Also distinguish:

1. Sovereign restructuring-related value loss, which may be embedded in B_liq.
2. SPV-specific liquidation expenses that may be embedded in the realisation input or separately deducted.
3. Contractual waterfall senior payments, represented by K_sen if not already reflected.

The boundary should be economically precise, even if the baseline sets K_sen to zero.

Do not add a detailed fee model.

Stable interface

Maintain the key calculation chain:

[
\text{Default-state collateral realisation}
\rightarrow
\text{Contractual Dealer loss}
\rightarrow
\text{CVA}.
]

Do not make the CVA paper dependent on finalising the companion B_liq methodology.

The paper must remain self-contained and should not reference unpublished companion drafts or legacy model labels.

⸻

4. Refine the posted-collateral and whole-pool explanation

Version 1.3 improved the distinction between:

* The contractual posted-collateral credit (C_\tau^{post}).
* The actual proceeds from liquidating posted collateral.
* The residual waterfall distribution.
* The aggregate whole-pool resource approximation.

Preserve this distinction.

Review Section 2.3 to ensure that:

* The two contractual routes are explained in plain quantitative language.
* The posted slice is credited exactly once.
* Any equality between posted-collateral credit and its realised liquidation value is identified as a valuation-consistency assumption rather than a general contractual identity.
* The remaining collateral is actually accessible through the waterfall under the stated priority assumptions.
* The simplified whole-pool expression follows logically from the general two-route resource formulation.

A reader should understand the economic logic without needing to navigate detailed legal tier descriptions.

Keep the contractual references in Appendix A.

If no new evidence resolves the valuation basis of (C_\tau^{post}), preserve it as an open confirmation. Do not invent a convention.

Improve the equation presentation

The existing boxed expression combining the general and zero-senior-cost cases is too wide.

Present them on separate lines or as an aligned derivation:

[
L_\tau
\approx
\left[
V_\tau^{co}

(B_\tau^{liq}-K_\tau^{sen})^+
\right]^+
]

followed by the baseline:

[
\boxed{
L_\tau\approx
[V_\tau^{co}-B_\tau^{liq}]^+,
\qquad K_\tau^{sen}=0.
}
]

Preserve the specified notional argument where useful.

Do not squeeze both expressions into one wide box.

⸻

5. Tighten the CVA and XVA implementation discussion

Sections 4 and 5 are largely satisfactory.

Do not expand them into a general introduction to the XVA engine.

Retain the distinction between:

* Mathematical CVA defined by the joint expectation of discounted event loss.
* The engine’s pathwise valuation and discounting.
* Deterministic marginal default weights.
* Event-state collateral impairment represented explicitly.
* Residual dependence between sovereign default timing and market factors that is not captured by deterministic weights.

Check the conditioning carefully

The correct general quantity remains:

[
\mathbb E^{\mathbb Q}
[D(0,t_i)L_{t_i}I_i].
]

The current approximation effectively uses deterministic bucket weights multiplying unconditional pathwise event-state losses.

Explain precisely which dependence this preserves and which it does not.

Do not claim that modelling collateral at default automatically captures all wrong-way risk.

Engine recovery factor

Continue to distinguish:

* Recovery assumptions used for calibrating the sovereign credit curve.
* The 70% MV-proxy fraction used to determine collateral proceeds.
* The residual severity applied to the extinguished Dealer claim.

Check that the engine’s payoff recovery parameter represents the intended residual severity and that the loss is not multiplied by an additional sovereign recovery adjustment.

The desk’s optional 99% LGD remains a separate convention from contractual 100% severity.

Keep the explanation concise.

Equation formatting

Improve the display of the principal CVA equation in Section 4.

If necessary, separate the continuous-time definition, bucket approximation and marginal default probability definition into aligned lines rather than one wide box.

Ensure that all notation is defined before or immediately after first use.

⸻

6. Keep the recovery scenarios correct and concise

Preserve the v1.3 correction:

Greek-style FV illustration

[
B_\tau^{liq}

0.22,IR_\tau N^{pool}.
]

The 22% fraction is illustrative and motivated by Greek restructuring-package evidence. It is not a calibrated recovery for the French collateral.

Desk temporary MV-proxy approach

[
B_\tau^{liq}

0.70,B_\tau^{proxy}(N^{pool}).
]

The 70% fraction is prescribed by the desk on the MV-proxy denominator and is not a recovery-of-face assumption.

Do not reintroduce the unsupported 40% FV example.

Keep the two break-even diagnostics:

* FV recovery break-even approximately 29.3%.
* MV-proxy value break-even approximately €28.03m.

These numbers measure different things and must not be presented as comparable recovery percentages.

Do not invent a proxy valuation to fill the missing monetary MV-proxy result.

Numerical example presentation

The worked example is valuable and should remain.

Keep one compact table showing the FV illustration, the current symbolic MV-proxy implementation and the key break-even quantities.

The posted-slice comparison can remain if clearly labelled as a hypothetical diagnostic rather than the contractual baseline.

Avoid unnecessary reproduction of calculations already shown in the appendices.

⸻

7. Improve readability through selective compression

The intended reader is an experienced quantitative finance professional comfortable with scientific papers.

The challenge is conceptual clarity, not mathematical sophistication.

Make the paper easier to read by eliminating unnecessary friction.

Section 1

Keep a concise explanation of the transaction and the economic asymmetry between the bond-indexed and fixed Note-side legs.

The CSA posting formula may remain, but avoid explaining its implications more than once.

Section 2

Preserve the derivation. This is the intellectual core of the paper.

Do not aggressively shorten the scheduled-obligation argument, since it is essential to understanding why the Dealer has credit exposure.

However, remove legalistic or defensive wording where a direct economic explanation is sufficient.

Section 3

Keep the B_liq interface prominent.

Explain the current MV-proxy approach and the direct FV alternative briefly.

Do not expand into recovery-model selection, historical evidence or calibration research.

Section 4

Keep the mathematical definition and approximation direct.

Avoid repeating the structural wrong-way-risk explanation already given in Section 2.

Section 5

Focus on what the existing engine actually does, what input is new and what approximation remains.

Do not reiterate the contract analysis.

Section 6

Keep the economic illustration and material open items.

Avoid a lengthy conclusion restating previous sections.

General editorial standard

* Prefer short, direct sentences.
* Explain each concept once.
* Use equations where they add precision.
* Avoid unnecessary rhetorical contrasts.
* Minimise MODEL CHOICE and TO CONFIRM markers in the narrative.
* Move supporting contractual detail into Appendix A.
* Avoid multiple names for the same economic object.
* Preserve a continuous logical flow from transaction to event loss to CVA.

Do not remove an essential explanatory step merely to reduce page count.

⸻

8. Improve the open-items table

The v1.3 table is useful but visually dense.

Improve it by:

* Shortening cell wording.
* Grouping closely related implementation checks.
* Giving the most important decisions adequate space.
* Avoiding excessively small fonts.
* Using sensible column widths and line breaks.

Retain the distinctions between contractual confirmation, model calibration, engine configuration and optional refinements.

Add post-default liquidation uncertainty/positive-part treatment as a model-validation issue if it cannot be resolved.

Do not turn the table into a comprehensive project-management tracker.

The main paper should identify material limitations, not catalog every possible enhancement.

⸻

9. Mandatory PDF and LaTeX quality checks

Compile the updated LaTeX and inspect the PDF page by page.

In particular, fix:

* The wide boxed loss equation in Section 2.
* The wide CVA equation in Section 4.
* Crowded rows in the open-items table.
* Any equations extending toward or beyond the margins.
* Overfull boxes.
* Awkward line wraps.
* Poorly balanced pages.
* Orphaned headings or stranded introductory sentences.
* Inconsistent symbol formatting and equation references.
* Unnecessary large blank areas.
* Any appendix tables with badly wrapped text.

Do not globally reduce font size or margins to make material fit.

Use the compilation log to identify LaTeX warnings, and visually inspect rendered pages to catch issues the compiler may not report.

Appendices

Keep the appendices starting on a separate page.

Avoid an appendix-only title page.

Preserve useful contractual evidence, example provenance and essential external references.

Do not restore historical model labels or references to unpublished internal drafts.

⸻

10. Final validation and deliverables

Produce:

1. Version 1.4 LaTeX source.
2. Compiled Version 1.4 PDF.
3. A concise revision summary.
4. A short list of unresolved material technical issues.
5. Confirmation that the compiled PDF was visually inspected.

The revision summary should explicitly explain:

* How the conditional-realisation/positive-part issue was treated.
* Whether any change to the loss formula was necessary.
* Whether the gross-versus-net realisation boundary was clarified.
* Whether senior costs may be double-counted.
* Whether the whole-pool derivation remains subject to the same contractual assumptions.
* Whether the XVA engine mapping changed.
* What was removed or shortened for readability.
* Which formatting issues were corrected.

Do not claim that contractual or implementation questions have been resolved unless new supporting evidence was actually found.

Final acceptance criteria

An experienced quant should be able to read the document once and understand:

1. What causes the Dealer’s loss.
2. Why the contractual close-out claim is not the defaulted bond’s value.
3. How posted collateral and waterfall distributions combine.
4. Why the baseline loss depends on whole-pool collateral realisation.
5. Exactly what B_liq represents and what approximation it contains.
6. How that loss is converted into CVA.
7. How the existing XVA engine implements the approximation.
8. Which assumptions materially affect the numerical result.

The paper should be complete without requiring an unpublished companion methodology.

Guiding principle: Preserve the correct derivation, state approximations precisely, and make the explanation simpler—not bigger.

This is a controlled final refinement. Do not reopen the entire methodology or introduce speculative modelling alternatives.
