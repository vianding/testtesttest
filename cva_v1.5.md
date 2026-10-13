# SPIRE CVA Methodology — Version 1.5
## Final Document-Wide Readability and Precision Pass

### Objective

Update *Pricing CVA for a SPIRE Repackaging Swap: Sovereign Bond Collateral* to Version 1.5.

The methodology and core quantitative architecture have converged. This revision is intended to make the paper **more concise, readable and logically continuous**, without sacrificing scientific rigor, contractual precision or implementation information.

The target reader is an experienced quantitative finance professional who is comfortable with scientific papers but unfamiliar with the detailed SPIRE legal documentation.

The main problem to solve is not mathematical complexity. It is unnecessary verbal complexity: repeated conclusions, excessive contractual exposition, defensive explanations and details introduced before the reader needs them.

**This is a surgical, section-by-section editorial refinement. Do not rewrite the paper from scratch.**

### Important: two parts have already been revised

I have already made targeted improvements to:

- Section 2.2 — the scheduled-obligation / close-out claim discussion.
- Section 2.3 — Route 1, including its introduction and the definitions of \(C_\tau^{post}\) and \(B_\tau^{liq}(N)\).

Use the current working LaTeX source containing those edits, not an older PDF or earlier source snapshot.

**Do not overwrite, restore or independently redraft these two revised passages.** Preserve their notation, definitions, equations and intended concise explanation.

Minor grammatical adjustments or necessary cross-reference fixes are permitted, but do not reopen their substance.

Everything else should be reviewed according to the instructions below.

---

## 1. Governing editorial principles

Apply these principles consistently.

**A. State the economic mechanism before its legal support.**

For contractual mechanics, the reader needs to understand:

1. What happens.
2. Why it matters to valuation.
3. Which mathematical quantity it determines.

Detailed legal evidence belongs in Appendix A.

**B. Explain each important conclusion once.**

Do not repeatedly explain that:

- Sovereign default impairs the collateral, not the scheduled contractual claim.
- The posted quantity does not necessarily cap total recovery.
- The Dealer reaches the collateral through two routes.
- The same collateral must not be credited twice.
- The sovereign recovery fraction and residual LGD are different.
- Default-state impairment is not identical to full default/exposure dependence.

Establish each where it belongs, then refer to the existing result.

**C. Protect mathematical continuity.**

Do not remove definitions, symbols, assumptions or intermediate reasoning merely to save space.

A shorter paragraph is useful only if the reader can still follow the derivation without guessing.

**D. Distinguish contractual facts, modelling assumptions and implementation limitations.**

Preserve substantive uncertainty. Remove repetitive announcements that something is a modelling choice when its status is already clear.

**E. Prefer affirmative, direct language.**

Explain what the model does and how it works. Discuss incorrect alternatives only where a likely pricing or implementation error needs to be prevented.

**F. Keep the document self-contained.**

The collateral-realisation methodology is not yet published. Do not introduce legacy model labels, historical draft comparisons or reliance on an unpublished companion paper.

---

## 2. Section 1 — Transaction economics and collateralisation

### Keep unchanged

Preserve:

- The description of the swap's bond-side and Note-side cashflows.
- The economic asymmetry between the index-linked and fixed cashflow legs.
- The performing-state swap valuation equation.
- The CSA posted-notional equation.
- Definitions of the required notional and price conventions.

### Simplify

The current discussion of collateralisation is longer than needed.

Its purpose is to establish that the sovereign bond also serves as variation margin, explain the finite collateral pool and prepare the reader for the contractual recovery discussion.

A suitable concise explanation is:

"The Original Collateral also serves as variation margin, posted at the CSA Valuation Percentage \(q_{\mathrm{CSA}}\). Ignoring minimum-transfer and rounding effects, the posted base notional is [retain existing equation], where \(P_t\) is the market value per unit of base notional, including indexation. The finite pool limits performing-state collateral posting.

At termination, the posted-collateral credit is not reduced by the CSA Valuation Percentage, and the Dealer may also access the unposted remainder through the waterfall (§2.3). The posted quantity therefore determines the split between recovery routes but does not cap aggregate recovery under the baseline assumptions."

Adapt the wording naturally to the surrounding text.

Do not imply the idealised posting formula represents every daily collateral adjustment.

### Notional convention

Retain a concise definition:

- \(N^{pool}\) and \(N_t^{post}\) are base notionals.
- \(P_t\) is quoted per unit of base notional and already incorporates relevant indexation.
- \(IR_t\) is applied explicitly to scheduled bond cashflows and FV-based realisation.
- In the MV-proxy construction, indexation is included in the inflation-linked bond PV.

Move the detailed Initial Exchange Amount verification to Appendix B, preserving its evidence.

Avoid explaining this convention repeatedly in later sections.

---

## 3. Section 2.1 — Contractual default and termination

This section should receive substantial compression.

### Essential content to preserve

1. Original Collateral Default is the contractual trigger.
2. The trigger produces Note early redemption, deemed swap termination and collateral liquidation.
3. The loss-relevant credit belongs to the third-party sovereign collateral issuer, not the Dealer's direct counterparty.
4. Liquidation takes place through a contractual process and may occur after termination.
5. Event probability is approximated using the sovereign issuer's credit curve, subject to event-definition basis risk.

### Remove or relocate

Move the detailed list of credit-event categories and lengthy liquidation quotations to Appendix A.

Do not reproduce the full Condition 3 trigger list in the main text.

Do not quote the exact forced-sale language unless a short quotation is essential to establish the valuation argument.

Remove repeated assertions that these consequences are contractual rather than modelling assumptions. One concise statement is sufficient.

### Desired narrative

Explain the trigger and its consequences first.

Then explain that default and liquidation need not occur at the same time, motivating a separate realisation valuation.

Finally, state how the event probability is modelled.

The section should naturally lead into the close-out claim discussion, without duplicating the collateral-realisation explanation in Section 3.

Rename the subsection to **"Contractual default and termination"** if this improves consistency.

---

## 4. Section 2.2 and Route 1 of Section 2.3 — Preserve existing edits

These passages have already been revised.

Do not reintroduce:

- Extended scheduled-versus-pass-through arguments.
- Long legal quotations.
- Redundant credit-double-counting discussion.
- Repeated definitions of the posted-collateral credit.
- Detailed CSA termination provisions that are already supported by Appendix A.

Keep the current notation:

\[
C_\tau^{post}
\]

for the contractual posted-collateral credit, and

\[
B_\tau^{liq}(N)
\]

for event-date liquidation resources attributable to base notional \(N\).

Keep the Early Termination Amount relationship:

\[
ETA_\tau=V_\tau^{co}-C_\tau^{post}.
\]

Preserve the distinction between the contractual credit and subsequent liquidation proceeds.

---

## 5. Section 2.3 — Route 2 and the whole-pool result

This section deserves moderate compression, but the mathematics is essential.

### Route 2 — waterfall

The current text explains the eight-tier waterfall in more detail than the main quantitative derivation requires.

Keep only the following essential points:

- Condition 15 distributes Available Proceeds according to fixed contractual priorities.
- The Dealer's residual swap claim ranks ahead of the Noteholders.
- Specified senior items, represented by \(K_\tau^{sen}\), rank ahead of the Dealer's residual claim.
- The waterfall recognises posted collateral already credited inside the close-out, preventing a second credit.
- The Dealer's residual entitlement is limited by the remaining claim and the proceeds actually available.

The complete tier ordering stays in Appendix A.

Avoid lengthy discussion of tier (i) when it does not apply to the direction of collateral posting in this example. A short qualification is sufficient.

### Preserve the two-route derivation

Retain the general resource expression:

\[
C_\tau^{post}+\mathrm{Rec}_\tau^{Dealer}.
\]

Then explain the conditions under which it can be approximated by whole-pool realisation net of senior items.

In particular, preserve:

- Common valuation-date and valuation-basis assumptions.
- Reconciliation between contractual posted credit and realised proceeds.
- The actual application of the unposted remainder through Condition 15.
- The absence of a relevant Series-level priority override.
- The requirement that senior deductions are not already embedded in \(B^{liq}\).

Do not describe the whole-pool equality as exact where valuation timing or conventions remain unconfirmed.

### Avoid repeated conclusions

The current text states whole-pool access, the difference between priority and ownership, and the limited significance of the Delivery Cap several times.

Explain these once, near the final whole-pool expression.

A concise statement is sufficient:

"Under these assumptions, the Dealer effectively has priority access to the realised value of the entire collateral pool, subject to senior deductions and the cap on its claim. This is a right to available proceeds, not ownership of the pool. The CSA posted quantity determines the allocation between the two recovery routes rather than the total recovery."

Retain the contractual qualifications.

### Limited recourse

Compress the Condition 17 explanation.

State that any shortfall remaining after available resources are exhausted is extinguished. Consequently, the contractual severity on the unpaid residual is 100%.

Move extended legal quotations and the non-petition explanation to Appendix A.

Keep the distinction between sovereign collateral recovery and residual claim LGD.

---

## 6. Section 2.4 — Event loss

This section is already strong and needs only light editing.

Retain:

- Definition of available resources.
- The positive-part loss expression.
- The general formula with \(K_\tau^{sen}\).
- The zero-senior-cost baseline.
- The contractual assumptions supporting the simplification.

Do not re-derive the waterfall after it has already been explained.

Explain briefly why the positive part is necessary and why excess collateral resources do not generate negative Dealer loss.

The baseline should remain prominent:

\[
\boxed{
L_\tau\approx
[V_\tau^{co}-B_\tau^{liq}(N^{pool})]^+.
}
\]

Avoid an additional extended explanation of structural wrong-way risk. One sentence is sufficient because Sections 4–5 address the pricing implications.

Preserve the distinction between this baseline and the general two-route contractual formulation.

---

## 7. Section 3 — Collateral-realisation interface

This section is fundamentally good.

**Do not turn it into a recovery-modelling paper.**

### Opening definition

Preserve the interface requirements:

- Event-state realisation value.
- Correct claim currency.
- Consistent valuation date.
- Clearly specified pool notional.
- Conditioning on sovereign credit event and relevant market state.
- No unconditional default probabilities or CVA aggregation weights embedded in the realisation amount.
- Gross/net treatment consistent with separate senior waterfall deductions.
- No residual-claim LGD embedded in collateral recovery.

However, express these in a compact paragraph rather than a lengthy catalogue.

Do not repeat the same distinction between collateral recovery and LGD in Section 5 unless required for the engine configuration.

### Section 3.1 — Valuation basis and timing

The event-date conditional-valuation equation is useful and should remain.

Preserve the distinction between:

1. Later liquidation proceeds.
2. Their value at the contractual event/close-out date.
3. The immediate event-state approximation used in the baseline.

Also preserve the important nonlinear-loss qualification:

\[
[V-\mathbb E(Y)]^+
\neq
\mathbb E[(V-Y)^+]
\]

in general.

Explain once that integrating out future liquidation variability before applying the loss floor may understate expected loss, especially near the boundary between full coverage and shortfall.

Do not expand this into a separate stochastic liquidation methodology.

Retain materiality assessment as an open validation item.

### Section 3.2 — Current implementation and recovery bases

Preserve the distinction between:

**Current desk implementation**

\[
B_\tau^{liq}(N)
=
0.70\,B_\tau^{proxy}(N).
\]

**FV illustration**

\[
B_\tau^{liq}(N)
=
R^{FV}IR_\tau N.
\]

Keep explicit that these recovery fractions have different denominators.

Retain the warning that the stressed-curve valuation and 70% MV fraction must be validated jointly to prevent double counting.

Reduce discussion of additional credit-aware constructions to one short sentence.

Avoid explaining model-selection history or alternative modelling hierarchies.

The section should make clear what the current model supplies, not invite the reader into another unfinished research debate.

---

## 8. Section 4 — CVA valuation

The mathematical structure is good. Preserve it.

Keep:

- The pricing-measure expectation of discounted event loss.
- The event indicator and default-bucket discretisation.
- The survival-probability interpretation of marginal default weights.
- The requirement to evaluate the positive-part loss before averaging.
- The difference between joint event-conditioned expectation and deterministic-weight approximations.

### Simplify the prose

The current text repeats several conclusions already established:

- Sovereign default is not itself the loss.
- The weighting curve belongs to the sovereign collateral issuer.
- The loss is net of recovery.
- Residual LGD should not be counted twice.

Retain one concise explanation of each genuinely necessary point.

Suggested organization:

1. Define the continuous-time CVA and discretisation.
2. Identify the relevant default curve.
3. Explain the required joint expectation and its relationship to the production approximation.

Avoid revisiting the contractual derivation.

Preserve the convexity argument where useful to warn against flooring an average exposure instead of averaging floored pathwise losses.

Do not expand the section.

---

## 9. Section 5 — XVA engine implementation

This section should preserve technical findings rather than merely become shorter.

### Section 5.1 — Existing engine

Retain:

- Pathwise exposure calculation.
- Pathwise discounting through the numeraire.
- Deterministic default-bucket weighting.
- Correct weighting entity.
- Collateral driving input.
- No duplicate external CSA netting.
- Potential dependence enhancements.

However, simplify the explanation into two or three coherent paragraphs surrounding the existing engine formula.

Avoid repeated descriptions of event-state collateral impairment.

The reader should understand exactly:

- Which components already exist.
- Which input the SPIRE CVA calculation supplies.
- What numerical approximation the existing aggregation makes.
- What remains a configuration or validation issue.

### Section 5.2 — Senior deductions and residual LGD

This discussion is useful but somewhat repetitive.

Preserve the economic distinction:

\[
K_\tau^{sen}
\]

reduces the collateral resources and therefore increases loss.

Residual LGD applies to any remaining shortfall and scales loss.

These act at different stages and cannot replace one another.

Retain:

- Contractual LGD = 100%.
- Baseline \(K_\tau^{sen}=0\), subject to materiality.
- Optional desk convention LGD = 99%.
- Correct use of the engine's payoff recovery parameter.
- Separation between curve-bootstrap recovery and payoff recovery.

Remove repeated warnings that 99% LGD is not sovereign recovery or a proxy for senior fees; state that distinction once.

Ensure that any illustrative LGD-adjusted formula is clearly identified as an aggregation convention, not a redefinition of contractual event loss.

### Section 5.3 — Timing

This is already concise.

Preserve the point that the contractual liquidation window is not automatically equivalent to the engine's MPOR parameter.

Avoid further development unless a genuine technical inconsistency exists.

---

## 10. Section 6 — Worked example and open items

### Section 6.1 — Worked example

**Preserve the numerical example and its economic conclusions.**

Keep:

- €51m base pool notional.
- €19.62m illustrative close-out.
- Index ratio 1.3142.
- Greek-style 22% FV illustration.
- 70% MV-proxy desk assumption.
- 29.3% FV break-even.
- €28.03m MV-proxy break-even value.
- Whole-pool versus posted-slice-only comparison.

These are useful because they turn the contractual derivation into tangible financial consequences.

Do not invent a monetary MV-proxy valuation where none is verified.

Do not reintroduce the unsupported 40% FV recovery example.

### Improve the narrative

Currently several sentences restate information already visible in the table.

Explain the assumptions briefly, show the calculation and draw one clear conclusion.

The important conclusion is that limiting recovery to the posted slice can materially overstate loss when the Dealer has contractual priority access to the remaining pool.

Avoid lengthy repetition of the 22% scenario limitations. One concise footnote or sentence is enough, with historical evidence in Appendix C.

### Section 6.2 — Open items

The current table is useful and relatively concise.

Keep the six principal items and their priorities unless there is a genuine omission.

Do not expand it into a comprehensive implementation checklist.

Shorten any crowded cell text without removing essential information.

---

## 11. Appendices — Supporting evidence, not repeated methodology

### Appendix A — Contractual basis

Retain the documentary evidence needed to verify the core derivation.

However, avoid duplicating long prose explanations already present in the main text.

Keep:

- Original Collateral Default.
- Swap termination.
- Contractual posted-collateral credit.
- Condition 15 waterfall.
- Limited recourse.
- Scheduled-obligation wording.
- Relevant Series-level qualifications.

The appendix can retain more legal precision than the main text.

Do not delete important supporting contractual quotations merely to save space.

### Appendix B — Example inputs and provenance

Preserve numerical auditability.

Keep the quoted-price/indexation reconciliation and the source of the numerical inputs.

Move any detailed price-basis discussion removed from Section 1 here.

Ensure that units, currencies, real versus dirty price and indexation treatment remain clear.

### Appendix C — Recovery evidence

Keep this concise.

Its purpose is to support the historical Greek-style illustration and explain why recovery denominators matter.

It does not need to reproduce the full recovery literature or calibration analysis.

Retain the important distinction between principal write-down, economic haircut and the market value of consideration received.

Do not suggest that the Greek precedent calibrates the desk's temporary 70% MV-proxy approach.

### References

Retain authoritative contractual and published research sources.

Do not cite unfinished, undistributed B_liq working drafts as an external methodological authority.

---

## 12. Final mathematical and notation audit

Even though this is an editorial pass, verify that the simplifications have not introduced errors.

Check:

- All variables are defined at first use.
- \(C_\tau^{post}\) remains distinct from \(B_\tau^{liq}\).
- No implicit equality is introduced between posted credit and future liquidation proceeds.
- The two-route resource formula and whole-pool approximation remain logically connected.
- The claim cap and positive-part operators are applied consistently.
- \(K_\tau^{sen}\) is not double-counted.
- FV and MV recovery fractions retain their distinct denominators.
- Inflation-indexation treatment is consistent.
- Contractual dollar loss is distinct from any optional LGD scaling in the engine.
- Joint default/exposure dependence is not silently replaced by an independence assumption.
- The current implementation remains correctly described as an approximation where required.

If a mathematical inconsistency is discovered, correct it and explain the correction in the revision summary.

Do not silently change the pricing methodology under the guise of editorial simplification.

---

## 13. PDF layout and final quality control

Compile the updated LaTeX and inspect the PDF page by page.

Ensure:

- Equations fit within the margins.
- No overfull boxes or clipped content.
- Tables use readable font sizes and sensible wrapping.
- Headings are not stranded at page bottoms.
- Paragraphs and equations have natural page breaks.
- The numerical example is not awkwardly split.
- Appendix A begins on a new page.
- Appendix tables remain legible.
- Equation numbers and cross-references are correct.
- Footer version is updated to 1.5.

Prefer better spacing, column widths and paragraph flow over reducing font sizes.

Do not force the total page count down by compromising the layout.

---

## 14. Deliverables

Produce:

1. Revised Version 1.5 LaTeX source.
2. Compiled Version 1.5 PDF.
3. A short section-by-section summary of edits.
4. A concise note identifying any substantive technical issue found.
5. Confirmation that the PDF was visually inspected and formatting errors corrected.

The edit summary should separately distinguish:

- Prose removed or relocated.
- Mathematical definitions or equations preserved.
- Any substantive corrections.
- Material assumptions that remain unresolved.

Do not perform another broad research review or propose a new architecture.

### Final acceptance test

An experienced quant reading the paper for the first time should be able to follow, in a single continuous reading:

**Transaction economics → contractual default → Dealer claim → posted credit and waterfall → available resources → event loss → realisation input → CVA → engine implementation.**

The reader should not have to consult the appendices to understand the core quantitative argument, but should be able to use them to verify material contractual and numerical assumptions.

The paper should feel like a concise, finished scientific methodology with explicit assumptions—not a compressed research diary.

**Final principle: Preserve every necessary idea, equation and causal connection. Remove unnecessary words, repetition and documentary detail from the main narrative.**