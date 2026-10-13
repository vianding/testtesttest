Agent B — Phase 1: Aggressive Refactor of Default-State Collateral Realisation Methodology

Objective

Aggressively refactor the existing 37-page Default-State Collateral Realisation Methodology (v0.2) into a standalone, reader-first technical research document explaining how to model and calibrate (B_\tau^{liq}) for the SPIRE sovereign inflation-linked bond collateral.

The primary audience is me, a quant familiar with derivative pricing, credit risk, stochastic modeling, CVA and FVA, but not necessarily with the sovereign restructuring and recovery literature.

I found the existing paper difficult to follow. The problem is not simply its length: it introduces too many classifications, switches between theoretical and implementation questions, repeats conclusions, and distributes related arguments across distant sections.

The refactor must solve those problems structurally.

This is Phase 1 of a two-phase process:

* Phase 1 (now): A clear, self-contained technical research document that helps me understand the economics, mathematical derivations, evidence, and modeling choices. Some additional explanation is desirable.
* Phase 2 (later): A further reduction into a concise, formal methodology paper suitable for wider circulation.

Do not prematurely optimize Phase 1 for publication-style brevity. Prioritize readability, logical completeness, and understanding.

1. Required source material and document hierarchy

Read the latest versions of:

1. Main SPIRE CVA paper, especially the aggressively refactored approximately six-page main text and its explicit (B_\tau^{liq}) interface.
2. SPIRE FVA/ColVA methodology, for consistent performing-bond valuation, CSA posting, funding and Delivery Cap notation.
3. Existing Default-State Collateral Realisation Methodology v0.2, including appendices and references.
4. Relevant source code and legal/transaction references where needed to resolve a factual or implementation claim.
5. The underlying academic papers, where necessary to verify empirical claims or calculations.

Authority order:

* Contractual terms govern legal and waterfall mechanics.
* The latest refactored CVA paper governs the overall CVA architecture and the (B_\tau^{liq}) interface.
* The latest FVA paper governs the performing-state and funding architecture.
* The existing v0.2 (B_\tau^{liq}) paper is research material to be critically reconstructed, not an authoritative structure or notation template.

If you find inconsistencies between these documents, identify them and resolve them where possible. Do not silently carry forward older notation or assumptions.

The new document must not require readers to consult the old (B_\tau^{liq}) paper.

It may refer to the latest CVA paper for contractual derivations, and the FVA paper for funding mechanics, but should explain the minimum context needed to understand the recovery problem independently.

2. Length and presentation target

Target approximately 12–16 pages of main text, excluding references and genuinely useful technical appendices.

This is a soft target, not a quota. A shorter coherent document is preferable to a longer one with repeated explanations.

Use the following principles:

* One conceptual question per section.
* Each section must build naturally on the previous section.
* Define notation before using it.
* Explain economic intuition before introducing mathematical complexity.
* Introduce a formula, explain each important term, and immediately explain what the formula tells us.
* Keep a derivation next to the question it answers.
* Use numerical examples selectively to make an abstract point concrete.
* Avoid lengthy literature surveys in the main narrative.
* Avoid repeating the same conclusion in the introduction, main body, summary, and appendices.
* Do not send readers to an appendix to understand a central argument.
* Avoid excessive boldface, labels, boxed statements, rhetorical questions, and repeated declarations of importance.
* Use tables only when they genuinely improve comprehension.

Most importantly: do not preserve the existing section structure merely because it already exists. Rebuild the narrative.

3. Mandatory conceptual scope

The paper answers one principal question:

How should we determine the monetary value of sovereign inflation-linked bond collateral that can actually be realized following the contractual Original Collateral Default event?

The core output is:

[
B_\tau^{liq}.
]

The paper must distinguish:

[
B_{\tau^-}^{perf}
\longrightarrow
B_\tau^{def}
\longrightarrow
B_\tau^{liq}.
]

Explain clearly:

* Performing bond value immediately before default.
* Value of the defaulted sovereign claim or restructuring entitlement.
* Actual value realizable through the SPIRE disposal mechanism.
* Why these values are economically different.
* Which quantities the model must calculate, and which are merely conceptual intermediate quantities.

The model should be usable through the interface defined in the latest CVA paper.

Do not rederive the CVA formula, swap close-out methodology, waterfall or FVA funding framework unless a short explanation is essential to the (B_\tau^{liq}) modeling decision.

In particular, do not reproduce the old multi-page discussion of Delivery Cap binding. Refer to the FVA methodology and explain the boundary in a short subsection only.

4. Proposed narrative sequence

Use the following sequence as the starting structure. You may improve it if you can justify a clearer ordering, but do not scatter the same topic across unrelated sections.

Section 1 — What exactly are we modeling?

Start with the economic event.

A sovereign bond is performing, a qualifying collateral default occurs, and the SPIRE compartment must dispose of collateral under its contractual rules.

Define the target (B_\tau^{liq}), the event time (\tau), the collateral quantity (N), and the relevant inflation index ratio (IR_\tau).

Explain what is and is not included in (B_\tau^{liq}), particularly treatment of disposal costs versus senior waterfall deductions.

Establish the interface to the CVA paper.

Section 2 — What does sovereign default actually leave the bondholder with?

Explain the major economic outcomes before discussing statistical recovery models:

* Exchange into new bonds with principal/coupon changes.
* Cash or cash-like consideration.
* Maturity extension or payment moratorium.
* Redenomination and currency conversion.
* Repudiation or very low recovery.
* Contingent instruments, warrants, and other restructuring entitlements.

Use a compact package-value expression:

[
B_\tau^{def}

V_\tau^{cash}
+
\sum_k q_kP_{k,\tau}
+
V_\tau^{contingent}.
]

Explain that the original bond may cease to exist in its original form, while the holder receives an economically different claim or package.

Make clear that the package may not yet be known, delivered, or tradable immediately at the default event.

Section 3 — From defaulted claim to actual SPIRE liquidation proceeds

Explain the contractual liquidation window and why it matters.

Distinguish:

1. Event date.
2. Restructuring announcement or agreement.
3. Exchange/settlement date.
4. Date on which the resulting claims can be sold.
5. Actual disposal date or forced-sale cutoff.

Do not assume that the package observed at a later exchange date was available for sale immediately at (\tau).

Address how executable bid prices, liquidation timing, transaction costs, liquidity and forced-sale conditions enter the target.

Critically examine the existing v0.2 proposal to average bid-side prices across a 30-business-day window.

A contractual obligation to sell by a deadline does not imply uniform execution throughout that window.

Define a coherent baseline realization convention, identify the assumptions behind it, and explain what historical data could validate it.

Avoid double-counting liquidity haircuts already reflected in executable distressed prices.

Also explain how proceeds received after (\tau) are valued or discounted consistently with the CVA interface.

Section 4 — What is the appropriate recovery base?

Only after defining the economic realization should you introduce recovery ratios.

Present the alternative normalizations clearly:

* Recovery relative to face/index-adjusted principal.
* Recovery relative to pre-default market value.
* Recovery of a Treasury-like or contractual cash-flow benchmark, where relevant.

Explain the differences using a small numerical example.

Then assess the proposed preferred convention:

[
R_\tau^{liq}

\frac{B_\tau^{liq}(N)}
{IR_\tau N},
\qquad
B_\tau^{liq}(N)=IR_\tau N R_\tau^{liq}.
]

Explain why the indexed-principal denominator may be useful for this particular product.

Do not claim that the FV base is universally correct or that maturity effects automatically disappear. Explain the contractual and empirical conditions under which this normalization is appropriate.

Resolve, or explicitly flag, the relevant OATei indexation convention, deflation floor, accrued interest and redenomination treatment.

Distinguish the empirical normalization of default-state proceeds from RFV/RMV/RT conventions used in pricing performing defaultable bonds.

The full live-bond recovery-convention derivations should not occupy the main narrative.

Section 5 — What does historical evidence actually tell us?

Use Greece 2012 as one worked example, not as the sole foundation of a general sovereign recovery model.

Show the exchange package, the value of its cash and noncash components, and the correct denominator.

Explicitly distinguish:

* Face-value writedown.
* Present-value haircut.
* Market-price discount.
* CDS auction price.
* Default-state package realization.
* Actual executable SPIRE-like liquidation proceeds.

Audit the arithmetic before reproducing any numerical claims.

In particular, the existing v0.2 combines approximately 22 per 100 old face, (PV_{old}\approx77)–79 and a 59–65% PV haircut. These numbers do not automatically reconcile:

[
1-\frac{22}{77}\approx71.4%,
]

not 59–65%.

Determine whether the source definitions, dates, valuation conventions or included consideration differ. If they cannot be reconciled, correct or qualify the example rather than repeating the inconsistency.

Check whether the Greek default/event date, exchange date and observed market-price dates are compatible with the target SPIRE liquidation window.

Then summarize what cross-event sovereign evidence adds, and what remains unobservable.

Section 6 — How should we model (R_\tau^{liq})?

This is the central modeling section.

Start with the simplest model:

[
R_\tau^{liq}=\bar R_{\rm eff}.
]

Explain what this assumption means and what it does not mean.

Then introduce possible extensions in a natural order:

1. Constant recovery.
2. Recovery conditional on restructuring regime.
3. Recovery conditional on pre-default sovereign credit state, e.g. (\lambda_{\tau^-}).
4. Additional bond-specific spread information, only if economically and empirically justified.

Discuss the role of pre-default bond prices as potential information about expected default-state proceeds, not necessarily as the denominator of the recovery ratio.

Explain the arguments for and against using (\lambda_{\tau^-}) to predict conditional recovery.

Clearly distinguish economic hypotheses from established empirical findings.

Do not introduce stochastic intensity dynamics until there is a demonstrated reason to use them.

Remove M0/M1/M2 and S0/S1/S2/S3 as primary model labels.

The latest main CVA refactor has moved toward descriptive model names and a stable (B_\tau^{liq}) interface. Follow that direction.

If legacy mapping is necessary for cross-referencing old implementations, put it in a small appendix or migration table, not throughout the text.

Section 7 — How do we calibrate the model?

Explain a practical calibration process:

[
\text{source observation}
\rightarrow
\text{economic concept}
\rightarrow
\text{consistent valuation date}
\rightarrow
\text{normalized realization}
\rightarrow
\text{model parameter}.
]

Be explicit about the difference between historical (P)-measure observations and the (Q)-measure quantity required for CVA.

The paper should not imply that an empirical recovery estimate automatically becomes a risk-neutral recovery parameter.

Revisit the existing recommendation to use a conservative quantile.

The existing document alternates between a regime-weighted average and a conservative quantile. These are mathematically different:

[
\mathbb E[R]=\sum_k w_kR_k
]

versus

[
R_\alpha=F_R^{-1}(\alpha).
]

Explain which quantity is appropriate for baseline expected-loss pricing, which is appropriate for a conservative policy overlay, and how that choice affects the nonlinear loss function:

[
\mathbb E[(V-B)^+]
\neq
(V-\mathbb E[B])^+.
]

Do not quietly replace a distribution of recoveries with a conservative point estimate and present the result as an unbiased (Q)-measure CVA.

If the data cannot support a numerical French OATei recovery estimate, say so clearly and give an implementable parameter-selection and sensitivity framework without inventing a calibrated number.

Section 8 — How does the model enter CVA, and what matters economically?

Show the final interface:

[
B_\tau^{liq}

IR_\tau N R_\tau^{liq}
]

for the recommended indexed-principal normalization, subject to confirmed legal conventions.

Explain the interaction with the existing CVA model only as much as necessary.

Retain the important break-even recovery diagnostic:

[
R_\tau^*

\frac{V_\tau^{co}}
{IR_\tau N^{pool}},
]

under the simplified whole-pool assumptions.

Explain why recovery-model accuracy matters most when it changes the positive-part loss and the resulting CVA.

Discuss sensitivity and model materiality.

Clarify that the conclusion that cap binding does not affect the CVA recovery quantum is conditional on exact CSA/waterfall recombination and relevant Series terms. Do not state it as unconditional while the tier accounting remains unconfirmed.

Do not duplicate the FVA cap-binding derivation.

Section 9 — Recommendation, limitations and next decisions

Conclude with a concise answer to:

* What should we implement now?
* Why is this the preferred starting point?
* Which assumptions are material?
* Which questions are genuinely unresolved?
* What evidence would justify a more sophisticated recovery model?

Separate methodological decisions from calibration gaps and implementation checks.

Avoid a long research-plan-style work programme.

5. Specific technical issues to resolve during the refactor

Do not simply copy conclusions from v0.2. Review the following carefully.

A. CDS recovery versus collateral realization recovery

The CDS calibration convention (R^{CDS}) and SPIRE liquidation recovery (R^{liq}) are different economic objects.

The existing paper argues that moving (R^{liq}) alone overstates recovery sensitivity because rebootstraping the CDS hazard curve at the same recovery would partially offset the loss.

That conclusion is not generally valid.

Distinguish:

* Sensitivity to collateral realization with the CDS-calibrated survival curve held fixed.
* Sensitivity to the assumed CDS recovery convention, with the CDS curve re-bootstrapped.
* A genuinely joint recovery/default model, if one is explicitly specified.

Do not imply that changing (R^{liq}) automatically requires changing (R^{CDS}).

B. Redenomination

Investigate the existing paper’s statements about the absence of published evidence or historical examples of EUR sovereign redenomination.

Distinguish:

* No observed euro-area sovereign exit with forced redenomination of existing EUR bonds.
* Published academic/legal research on euro redenomination risk.
* Market-implied evidence from sovereign bonds, quanto CDS or related instruments.
* Historical currency-conversion episodes outside the euro area, whose transferability must be assessed.

The absence of an observed euro-exit event does not mean the literature is absent.

For French inflation-linked sovereign debt, explain how redenomination could affect the legal claim, inflation indexation, FX conversion and EUR liquidation value.

Keep this as a clearly explained scenario, not an unjustifiably calibrated stochastic regime.

C. Recovery-base mismatch

Retain the important finding that an FV-calibrated recovery percentage cannot simply be multiplied by an MV-based production proxy.

Explain the distinction with one clear derivation and numerical example.

Verify whether production actually contains the suspected mismatch before describing it as an existing implementation error.

D. Whole-pool CVA versus posted-slice FVA

Preserve the economic distinction, but do not expand this into another funding paper.

State exactly which legal and implementation conditions are needed for the CVA whole-pool conclusion.

Refer to the FVA paper for the Delivery Cap and performing bond-price dynamics.

E. Greece as evidence

Check primary-source definitions, arithmetic, event dates, and the comparability of observed package prices to actual event-window liquidation proceeds.

Do not use one episode to establish a universal recovery law.

F. Calibration statistic and risk measure

Separate empirical mean, regime mixture, quantile, stress scenario, policy overlay and (Q)-measure pricing assumptions.

These must not be interchangeable.

6. Notation and cross-document consistency

Before writing, inspect the latest CVA and FVA papers and build an internal notation crosswalk.

The new paper must use their current conventions for:

* (\tau): Original Collateral Default time.
* (V_\tau^{co}): Dealer contractual close-out value.
* (B_\tau^{liq}): default-state liquidation proceeds.
* (B_\tau^{avail}): resources available to Dealer after contractual adjustments.
* (N^{pool}) and (N_t^{post}): whole-pool versus posted collateral quantities.
* (IR_t): inflation index ratio.
* (P_t) and (B_t^{perf}): performing-state price and monetary bond value.
* (R_\tau^{liq}): dimensionless liquidation recovery.
* (R^{CDS}): CDS calibration recovery convention.
* (\lambda_t): sovereign default intensity.
* Discounting, valuation dates and (P/Q) probability measures.

These are expected meanings, not permission to override a different convention established in the latest companion papers. Where the latest papers differ, reconcile explicitly.

Do not reuse a symbol for two different quantities.

Avoid introducing aliases unless necessary.

Do not repeat the main CVA or FVA derivations.

7. Appendices and references

Keep only appendices that genuinely support the main narrative.

Potential appendices:

* Detailed Greek restructuring calculation and source reconciliation.
* RFV/RMV/RT derivations for readers who want the theory.
* Calibration dataset definitions and statistical validation protocol.
* Optional stochastic-intensity implementation.
* Legacy M/S model-label mapping, if needed.

Move detailed funding cap-binding work out of this document, or retain only a short cross-reference to the FVA paper.

References should identify the specific claim or quantity supported, rather than merely listing papers by topic.

Verify numerical claims and literature attributions wherever possible.

8. Deliverables

Produce:

Deliverable 1 — Refactored Phase 1 paper

A complete standalone LaTeX document, compiled to PDF, with clear sections, coherent notation, correct equations, and usable references.

It should read as a technical explanation written for an intelligent quant who is learning the sovereign default/restructuring recovery problem, not as a literature survey or internal research log.

Deliverable 2 — Short change log

Outside the paper, summarize:

* Major structural changes.
* Material technical conclusions corrected or qualified.
* Sections moved, merged or deleted.
* Outstanding questions that could affect the recommendation.
* Any unresolved notation conflict with the CVA/FVA papers.

Do not insert this change log into the methodology paper itself.

Deliverable 3 — Concise unresolved-issue list

Rank only the remaining issues that could materially change:

1. The definition of (B_\tau^{liq}).
2. The recommended recovery model.
3. Its calibration.
4. The CVA result.

Do not turn this into another extensive work programme.

9. Final quality test

Before delivering, read the new document as someone who has never seen v0.2.

Verify that the reader can answer, in order:

1. What happens economically when the collateral sovereign defaults?
2. What does the holder own afterward?
3. What can SPIRE actually liquidate, and when?
4. What is (B_\tau^{liq})?
5. Why choose a particular recovery denominator?
6. What does historical evidence tell us?
7. Why start with constant recovery rather than stochastic recovery?
8. How is the parameter calibrated, and what remains uncertain?
9. How does it enter the CVA calculation?
10. What would justify a more sophisticated model?

If the reader must jump between sections or appendices to follow this argument, reorganize the document.

If a paragraph does not materially contribute to answering these questions, shorten, move or remove it.

Success criterion: I should be able to read the Phase 1 paper from beginning to end, understand the modeling decisions and their evidence, and challenge the assumptions without consulting the old 37-page document.

Do not begin Phase 2 compression. Complete Phase 1 only.
