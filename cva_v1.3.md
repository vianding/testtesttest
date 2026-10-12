Task: SPIRE CVA Methodology — Targeted Update to Version 1.3

Please update Version 1.2 of Pricing CVA for a SPIRE Repackaging Swap: Sovereign Bond Collateral to Version 1.3.

This is a targeted technical, numerical, editorial and typesetting update, not another structural rewrite.

Version 1.2 has largely converged on the desired methodology. Preserve its main architecture, contractual derivation, core loss formula, XVA engine analysis and concise scientific style.

The priorities for this revision are:

1. Correct the recovery examples and their economic interpretation.
2. Improve the treatment of liquidation timing and the event-date valuation basis.
3. Verify and qualify the close-out treatment of posted collateral and the whole-pool simplification.
4. Resolve any remaining inconsistencies between the loss formula, recovery construction and XVA engine integration.
5. Apply small readability improvements without expanding the document.
6. Inspect and fix all PDF formatting issues, including overfull boxes and tables.
7. Start the appendices on a new page.

1. Replace the recovery scenarios in the worked example

The existing Version 1.2 uses 40% and 22% as alternative recovery fractions on the face-value basis.

Please revise this.

1.1 Remove the unsupported 40% FV assumption

The 40% recovery-of-face assumption is insufficiently supported for this transaction and should no longer be presented as a principal illustrative scenario.

Remove it from the main numerical comparison, associated calculations and any appendix discussion presenting it as a preferred developed-sovereign assumption.

Do not replace it with another generic sovereign recovery percentage.

1.2 Retain 22% as a Greek-style FV illustration

The approximately 22% historical Greek-style package-value example should be retained, but correctly characterised.

It is an illustrative recovery fraction against old face/index-adjusted principal, informed by the Greek restructuring experience.

It is not a universal sovereign recovery rate, an estimate specifically calibrated for the French sovereign collateral in this transaction, or a direct observation of proceeds under SPIRE’s liquidation process.

Retain the caveat that the Greek precedent involved different bonds, restructuring terms and legal/economic circumstances.

For the existing illustrative inputs:

* Gross close-out claim: €19.62m.
* Index-adjusted pool principal: €67.02m.
* FV recovery fraction: 22%.

The resulting approximate whole-pool realisation is €14.74m, and the baseline Dealer loss is approximately €4.88m, before senior deductions and using the illustrative close-out.

Recalculate from full-precision inputs where available and handle rounding consistently.

1.3 Introduce the desk’s temporary 70% MV recovery assumption

The desk has specified a 70% recovery assumption on market value / MV-proxy value for its temporary recovery approach.

This should be included as a separate construction, not substituted directly into the FV formula.

The relevant expression is:

[
B_\tau^{liq}(N)

R_\tau^{MV}B_\tau^{proxy}(N),
\qquad R_\tau^{MV}=70%.
]

For the stressed-curve construction, (B_\tau^{proxy}) is the bond value generated using the applicable stressed discount-curve assumption.

The desk’s 70% is a prescribed temporary assumption, not a calibrated FV recovery rate.

Clearly explain that it is applied to a bond valuation denominator rather than to index-adjusted principal.

Do not imply that 70% MV recovery is necessarily greater or less than 22% FV recovery in monetary terms.

The result depends on the stressed-curve valuation base.

1.4 Rebuild the numerical example using the correct denominators

Replace the previous 40% FV versus 22% FV table with a compact comparison that distinguishes:

Greek-style FV illustration

[
B_\tau^{liq,FV}

0.22,IR_\tau N^{pool}.
]

Desk temporary MV-proxy approach

[
B_\tau^{liq,MV}

0.70,B_\tau^{proxy}(N^{pool}).
]

The existing paper does not establish the stressed-curve proxy value needed to calculate the monetary recovery under the 70% assumption.

If that valuation is available from the actual implementation or verified input data, use it, with its date, notional basis, discount-curve specification and provenance stated.

If unavailable, do not invent a bond valuation.

Instead, leave the MV-proxy proceeds and corresponding loss symbolic and show the break-even proxy valuation.

With the existing close-out input:

[
B_\tau^{proxy,*}

\frac{V_\tau^{co}}{R^{MV}}

\frac{19.62}{0.70}
\approx €28.03m.
]

This is the proxy-value threshold above which the baseline Dealer loss vanishes, assuming zero senior deductions and the current whole-pool formula.

The FV break-even fraction remains approximately 29.3%, using the stated example inputs.

Present these as different break-even diagnostics, not interchangeable recovery percentages.

1.5 Preserve the intuition about whole-pool access

The current Version 1.2 numerical example usefully demonstrates that the Dealer’s loss depends on access to the whole collateral pool rather than only the posted slice.

Preserve this insight.

However, do not force the 70% MV-proxy construction into the posted-slice comparison unless the corresponding proxy valuation of the posted slice is available and the allocation is consistent.

A short mathematical or numerical explanation of the difference between posted-slice-only and whole-pool recovery is sufficient.

The example should remain concise and mathematically meaningful.

2. Refine the liquidation timing argument in Section 3.1

Version 1.2 correctly distinguishes:

1. Actual cash proceeds realised during liquidation.
2. Their present value at the event/close-out valuation date.
3. An immediate event-state recovery approximation.

Keep this distinction.

However, revise the justification for approximating future liquidation proceeds by an immediate amount.

The fact that the liquidation period is short relative to the remaining maturity of the bond is not sufficient justification.

What matters is whether the valuation effect of the liquidation period is material, given:

* Discounting between termination and liquidation.
* Market movements during the liquidation period.
* Forced-sale terms and available liquidity.
* Whether these effects are already represented in the recovery input.
* The precision needed for the CVA calculation.

If useful, give a general representation such as:

[
B_\tau^{liq}

\mathbb E_\tau^{\mathbb Q}
\left[
D(\tau,u)\Pi_u^{liq}
\mid \text{relevant event information}
\right],
]

with an appropriate definition of liquidation date (u), cash proceeds and conditioning.

Keep the actual methodology compact.

The baseline can use an event-state approximation, but this should be justified by calibration consistency or a materiality assessment, rather than by comparison with the trade’s long maturity.

Do not claim the approximation has been validated unless there is actual supporting evidence.

If the materiality has not been tested, state this as a refinement/open item.

3. Verify posted collateral credit and the whole-pool loss expression

Review Section 2.3 and equations (5)–(9).

The contractual framework describes two routes:

* Posted collateral credited within the Early Termination Amount.
* Unposted collateral recovered through the Condition 15 waterfall.

The current draft assumes the posted slice is credited at its realised value.

Please verify carefully whether the governing documents establish this valuation basis or whether the amount is determined at the Early Valuation Date using an applicable contractual valuation convention.

Distinguish:

* The contractual valuation date.
* The value used to credit posted collateral within the ETA.
* Subsequent liquidation proceeds.
* Any contractual valuation discretion or timing differences.

Do not treat these as identical without justification.

Determine whether the whole-pool reduction remains valid exactly, approximately, or only under specified valuation-consistency assumptions.

If the exact split between posted-collateral credit and waterfall distribution is uncertain, preserve the general two-route formulation and state the assumptions supporting the aggregate formula.

Avoid overstating the result as a contractual identity when some valuation convention remains unconfirmed.

The central whole-pool approximation should remain the primary baseline if justified.

Engine treatment

Verify that the engine can supply the intended gross close-out claim and total realisation amount without separately subtracting standard CSA collateral again.

Distinguish the implementation of the contractual ETA from the simplified total-loss interface.

Keep only the necessary explanation in the paper and place specific configuration checks in the open-items table or engineering notes.

4. Check the recovery constructions for consistency

In Section 3.2, retain the distinction between:

* FV recovery against index-adjusted principal.
* MV recovery against the specified proxy or bond market value.
* A credit-aware bond valuation with its own recovery fraction.

Ensure that the 70% desk assumption is placed only on the correct MV denominator.

Do not silently apply the same 70% to both the stressed-curve proxy and a pre-default market bond price if those are different objects.

Review whether the stressed-curve discounting already incorporates an element of sovereign credit deterioration.

Avoid double counting credit deterioration by simultaneously embedding it in the proxy valuation and applying a recovery assumption whose calibration assumes a different valuation base.

For the MV-proxy baseline, explicitly state that the combination of the stressed-curve valuation and the 70% desk fraction requires validation as a whole.

This does not require building a new credit model.

The paper should only establish the calculation, its denominator, its current assumptions and its calibration limitations.

Keep detailed recovery calibration in the separate modelling work.

5. Preserve the improved XVA engine analysis

Sections 4 and 5 are substantially improved.

Preserve the distinction between:

* Default-state collateral impairment explicitly represented in the event loss.
* Dependence between simulated market factors and sovereign default timing, approximated under deterministic default weights.
* Richer conditional-default integration available as a potential refinement.

Do not revert to statements that all wrong-way risk is captured exactly.

Verify the implementation-specific conclusions against the actual source/configuration.

One additional consistency check is important:

The engine’s aggregation weight contains ((1-R)), while the recovery model already determines the collateral proceeds within the pathwise loss.

The (R) in the aggregation weight must represent the intended residual-claim severity convention, not the sovereign bond’s FV or MV recovery assumption.

Preserve the distinction between recovery used for credit-curve calibration and recovery used in the payoff aggregation.

Confirm that the relevant configuration selects the appropriate payoff recovery rate, with no second severity multiplier elsewhere.

If unverified, retain it as an implementation check.

Do not expand this section with a general discussion of XVA architecture.

6. Keep senior deductions and LGD treatment concise

Preserve the existing distinction between (K_\tau^{sen}) and residual LGD.

For the baseline:

* (K_\tau^{sen}=0), subject to materiality.
* Contractual residual LGD = 100%.
* The desk’s optional 99% residual LGD convention is an approximation, separate from collateral recovery and senior costs.

Do not describe 99% LGD as an estimate of sovereign recovery or a proxy for senior fees.

Do not repeat the same distinction across multiple sections.

Retain the necessary formula and a concise implementation note.

7. Final editorial improvements

Apply only targeted prose edits.

Section 1

Consider compressing the CSA posting discussion, since posting affects performing-state margin dynamics but does not bound the baseline whole-pool realisation.

Keep the posting formula if it helps explain the distinction and supports the example; otherwise move the detailed calculation to Appendix B.

Section 2

Preserve the derivation and its explanation.

Do not expand the legal discussion or restore deleted quotations.

Section 3

Shorten the construction comparison table.

The Status column should contain concise phrases rather than entire explanatory paragraphs.

Move any important qualification into one brief paragraph.

Section 4

Preserve the continuous-time CVA definition and the corresponding discretisation.

Do not conflate the exact joint expectation with the deterministic-weight approximation.

Section 5

Retain the source-verified engine conclusions, but avoid restating all of Section 2’s contractual logic.

Section 6

Update the numerical example as above.

Keep the outstanding decisions focused on matters that affect the contractual derivation, numerical result or production implementation.

Avoid another lengthy conclusion.

Whole document

Remove repeated explanations, excessive defensive qualifications and unnecessary MODEL CHOICE / TO CONFIRM interruptions.

Preserve the ability to distinguish contractual facts, modelling assumptions and genuine open questions.

Do not introduce new historical or legacy terminology.

Write as a self-contained first-release methodology. No previous CVA or B_liq draft should be assumed to have been distributed.

8. Mandatory LaTeX and PDF formatting audit

After producing Version 1.3, compile the LaTeX and inspect the rendered PDF page by page.

A successful compilation alone is not sufficient.

Check and correct:

* Overfull and underfull horizontal boxes where visually problematic.
* Equations extending past the text margins.
* Long equations or arrows protruding beyond their containers.
* Tables exceeding page width.
* Table text that wraps excessively or produces awkward, very tall rows.
* Column headings colliding with adjacent columns.
* Footnotes or captions overlapping other material.
* Excessively large blank spaces introduced by floats or page breaks.
* Orphaned headings at page bottoms.
* Bad page breaks inside equations or short explanatory blocks.
* Inconsistent mathematical superscripts, subscripts and notation.
* Incorrect equation references after revisions.
* Poorly positioned appendix headings.
* Missing or inconsistent references.

In particular, review the Section 3.2 construction table, Section 6.2 outstanding-decisions table, Appendix A contractual-provisions table and Appendix B example-input table.

Prefer a better layout, shorter cell text or suitable column widths over simply reducing the font size.

Do not globally shrink the document to fix one overflowing equation or table.

Use the LaTeX compilation log to identify overfull box warnings, then visually inspect the affected pages.

Where practical, use appropriate alignment or multiline equation environments rather than forcing long expressions onto one line.

9. Start appendices on a new page

The appendices must begin on a new page, not immediately after the main-text conclusion.

Insert an explicit page break before the appendices, such as \clearpage or the equivalent appropriate to the document class.

Place the Appendices heading and Appendix A at the beginning of the new page.

Do not allow a nearly empty appendix-title page. The heading should share its page with the first appendix content.

Ensure the transition from main text to appendices looks intentional and professional.

Keep subsequent appendix page breaks natural; avoid forcing every appendix onto its own page unless layout requires it.

10. Output and verification

Deliver:

1. Updated Version 1.3 LaTeX source.
2. Successfully compiled Version 1.3 PDF.
3. A short technical and editorial change summary.
4. A list of remaining material open issues.
5. A confirmation of the PDF inspection and any formatting problems corrected.

The change summary should specifically confirm:

* The unsupported 40% FV scenario was removed.
* The Greek-style 22% FV example was correctly labelled.
* The desk temporary 70% MV-proxy assumption was introduced on the correct denominator.
* No unsupported MV-proxy valuation was invented.
* The liquidation-timing justification was corrected.
* The posted-collateral valuation basis and whole-pool approximation were reviewed.
* The CVA engine integration and severity handling remain consistent.
* All major tables, equations and page breaks were visually inspected.
* The appendices start on a new page.

Do not introduce new methodological research unless necessary to resolve a material inconsistency.

Final objective: Version 1.3 should be a concise, self-contained, scientifically rigorous and professionally typeset quantitative methodology, with numerical examples that accurately distinguish the recovery bases and no unnecessary research-history baggage.

Additional requirement: Finalise the CVA–B_liq methodology boundary

Version 1.3 should establish a clean, stable and self-contained interface between the CVA framework and the collateral-realisation model.

The CVA paper must be complete as a quantitative methodology in its own right, even though the detailed collateral-realisation research is still ongoing and has not been published.

Do not make the CVA paper read like an incomplete first half of a larger research project.

1. Clarify ownership of the two methodologies

The CVA paper owns:

* The transaction’s economic cashflows and contractual default trigger.
* The Dealer’s close-out claim.
* The treatment of posted collateral and waterfall distributions.
* The available resources and limited-recourse loss.
* The CVA pricing expectation and its engine-compatible approximation.
* The integration of conditional collateral realisation into the loss calculation.

The collateral-realisation methodology owns:

* Default/restructuring recovery economics.
* Selection of recovery base and model construction.
* Calibration of recovery fractions.
* Default-state sovereign bond or restructuring-package valuation.
* Recovery sensitivities and empirical validation.

Do not duplicate the second set of responsibilities in the CVA paper.

2. Define B_liq as a precise model interface

The CVA framework consumes

[
B_\tau^{liq}(N^{pool}),
]

representing the collateral-realisation resources attributable to the specified pool following the contractual sovereign credit event.

Section 3 should specify the following requirements:

Valuation basis: The value must be expressed consistently with the event-date close-out claim. If proceeds are realised later, the model must either account for the timing or use an explicitly documented event-date approximation.

Currency and notional: The output is a monetary amount, expressed in the appropriate currency and on the specified base-notional/indexation convention. Recovery percentages with different denominators are not interchangeable.

Event-state conditioning: The input represents collateral realisation under the sovereign default/restructuring event, evaluated using the relevant market state. Any subsequent averaging over default times and simulated market states belongs to the CVA framework.

Gross versus net proceeds: Specify whether the input is before or after senior waterfall deductions. Under the current formulation, B_liq represents collateral realisation before (K_\tau^{sen}), which is handled separately. Avoid double-counting deductions.

Recovery and LGD: The input represents collateral recovery, not loss severity on the Dealer’s extinguished residual claim. Do not apply a generic residual LGD inside B_liq.

A compact mathematical representation may be used, if helpful:

[
B_\tau^{liq}

\mathcal R(\tau,X_\tau,N^{pool};\theta_R),
]

where (X_\tau) denotes the relevant simulated state and (\theta_R) represents collateral-realisation assumptions and parameters.

However, introduce this additional notation only if it improves clarity. Prefer preserving the existing (B^{liq}) notation.

3. Separate event-state valuation from default probability weighting

Be precise about the phrase “conditional on default.”

The collateral-realisation model supplies the value of collateral under the specified default event and relevant market state.

It must not silently incorporate unconditional sovereign default probabilities or CVA default-bucket weights.

The CVA engine owns the integration over event timing and market states.

Keep the distinction:

[
\text{Event-state realisation}
;\longrightarrow;
\text{Contractual Dealer loss}
;\longrightarrow;
\text{Discounted expected loss}.
]

This division should remain valid under both the current deterministic-weight engine approximation and potential future conditional-default enhancements.

4. Give the reader one definite current implementation

Avoid presenting three competing recovery constructions as though the CVA methodology is waiting for its own mathematical definition.

The CVA framework is already defined independently of that choice.

State the current executable approach clearly:

* A stressed-curve bond-valuation proxy.
* A desk-prescribed temporary 70% recovery fraction on the MV-proxy base.
* The resulting proceeds supplied through the common B_liq interface.

Distinguish this from the 22% FV Greek-style illustration.

Other recovery constructions may be mentioned briefly to demonstrate modularity, but their construction and calibration belong outside the main CVA paper.

A change in recovery model may alter required market factors or valuation infrastructure, but it should not change the contractual Dealer-loss definition or CVA pricing principle.

5. Avoid companion-paper dependency and historical references

The separate B_liq methodology is not yet finalised or distributed.

Therefore:

* Do not cite it as an already-published authority.
* Do not make reading it necessary to understand the CVA paper.
* Do not discuss previous labels, historical model recommendations or earlier drafts.
* Do not describe the CVA methodology as awaiting completion of another paper.

The CVA paper should independently define its quantities, assumptions, current implementation and limitations.

References to future collateral-realisation refinements should be brief and factual.

6. Refine Section 3 without expanding it

Prefer the following flow:

Definition → valuation/interface requirements → current implementation → possible refinements.

Shorten the current construction table and any redundant recovery-model discussion to make room.

Do not add a new main section, lengthy mathematical formalism or another appendix merely to document this interface.

The total length should remain within the existing Version 1.3 target.

Final acceptance test

An experienced XVA quant should be able to read the CVA paper alone and understand exactly:

1. What B_liq represents.
2. How it is valued and on what basis.
3. What model currently supplies it.
4. How it enters the Dealer loss calculation.
5. Which recovery assumptions remain temporary or require validation.
6. Why replacing the collateral-realisation model does not require re-deriving the CVA methodology.

The paper must feel like a complete methodology with a replaceable valuation component, not an unfinished methodology dependent on a future publication.
