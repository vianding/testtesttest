Thanks. I agree with your diagnosis and proposed architecture. Before proceeding with the rewrite, please incorporate the following decisions and refinements.

1. Page target: choose Option 1

Target approximately 5–6 pages of main text, plus 2–3 pages of appendices.

This is not a strict page limit. Clarity, correct derivation and technical completeness take priority. Do not compress explanations to the point where readers must reconstruct missing reasoning.

The main text should be independently understandable. Appendices should support and substantiate it, not contain essential missing steps.

Please proceed with the rewrite rather than requiring another round of approval for minor structural choices.

2. Consolidate model notation across CVA and B_liq

I am not satisfied with the current coexistence of M0/M1/M2 in the CVA paper and S0/S1/S2/S3 in the B_liq paper.

The companion paper correctly identifies three distinct dimensions:

* Recovery base: FV, MV or RT.
* Implementation: direct, stressed-curve proxy or credit-aware bond model.
* State dependence: constant or state-dependent recovery fraction.

However, the labels mix these dimensions, making the model hierarchy unnecessarily confusing.

My preferred solution is to use descriptive model names in both papers rather than separate M and S numbering schemes.

For example:

* Direct FV benchmark (formerly M0)
* Stressed-curve baseline (formerly M1)
* Credit-aware refinement (formerly M2)

State-dependent recovery fractions should be described as refinements to these constructions, rather than as another competing sequence of model numbers.

Use explicit notation for the actual mathematical quantities, but avoid multiplying model labels.

Please review this proposal critically. If there is a compelling engineering or documentation reason to preserve numeric identifiers, propose one consolidated scheme instead, with a precise mapping from both legacy systems.

Whatever you choose, the two papers must use the same terminology and mathematical definitions. Do not retain conflicting authoritative labels.

One important issue: the B_liq paper currently recommends M0 + S0 while the CVA paper identifies M1 as the current production baseline.

Do not silently resolve this inconsistency by redefining the labels or changing the implementation recommendation.

Distinguish:

1. The preferred conceptual or methodological benchmark.
2. The current executable production baseline.
3. Future modelling refinements.

The CVA paper must remain agnostic about the eventual collateral-realisation model where appropriate.

3. Simplify senior waterfall deductions and consider 99% LGD

The contractual derivation currently includes senior deductions (K_\tau^{sen}).

I agree these belong in the general formulation, but I am reluctant to introduce a separate stochastic process or calibration for this term in the production baseline.

The desk has proposed a 99% LGD convention. Please investigate whether this can be used as a practical severity approximation.

Importantly, distinguish:

* Senior deductions that reduce available collateral proceeds.
* Loss severity on the Dealer’s residual claim after application of proceeds.

These are not mathematically equivalent.

In particular, deducting senior costs increases potential loss, whereas multiplying a shortfall by 0.99 reduces it.

The contractual framework indicates that the unpaid residual is extinguished, suggesting a legal residual LGD of 100%. The 99% desk treatment is therefore an approximation or convention, not the contractual result.

My preferred presentation is:

* Retain the general waterfall treatment in the contractual derivation or appendix.
* Use zero senior deductions as the baseline approximation, unless material costs are established.
* Explain the optional 99% desk LGD convention separately, without claiming it represents senior costs.
* Avoid implementing an independent (K_\tau^{sen}) stochastic model unless sensitivity analysis indicates it is material.

Assess whether the desk convention should be implemented as a multiplier on positive residual loss, and whether the existing engine already applies any recovery or LGD factor. We must avoid an accidental second severity adjustment.

Include one concise explanation of the distinction in the final paper.

4. Improve the alignment between the CVA formula and the XVA engine

I agree with your observation that the existing engine appears to use survival-curve-based default weights rather than explicitly simulated default times.

If this is supported by our code findings, we should present the methodology in a way that naturally fits that architecture.

However, distinguish the mathematical definition from the numerical implementation.

Start from the continuous-time expected event loss and then show how the existing engine computes or approximates the required expectation.

In particular, verify:

* Whether the engine has access to a conditional event-state loss at each exposure bucket.
* Whether its marginal default probabilities are unconditional or conditional on simulated market factors.
* Whether discounting is pathwise or factored outside the expectation.
* Whether wrong-way dependence between sovereign default, rates, inflation, close-out valuation and collateral proceeds is retained.
* Whether any default-event conditioning or hazard-weighted integration must be added.

Do not claim that applying marginal survival probabilities to ordinary unconditional exposure is equivalent to the full joint expectation.

If the current engine lacks the necessary conditional-dependence mechanism, state that limitation and identify the minimum extension required.

We are looking for the simplest correct implementation using existing infrastructure, not a new simulation architecture unnecessarily.

5. Preserve the central contractual conclusions, not the legal prose

I agree with your proposed drastic compression of the legal material.

The main text must nevertheless establish, in a logically connected explanation:

1. Why sovereign Original Collateral Default causes swap termination.
2. Why the Dealer’s close-out claim is based on the Issuer’s scheduled swap obligations, rather than the defaulted bond’s market value.
3. Why the Dealer can access the posted and unposted collateral through separate contractual mechanisms.
4. Why the posted slice must not be credited twice.
5. Why any unpaid residual is extinguished.

Use ordinary financial language, with concise contractual references.

Move detailed clause quotations and legal evidence to the appendix.

The scheduled-obligation versus pass-through distinction should be supported clearly, including any remaining requirement to confirm the executed Swap Agreement.

Do not repeat the same contractual conclusion in several sections.

6. Make the model interface more important than model labels

The key output required from the companion methodology is the conditional default-state realisation, (B_\tau^{liq}), on a clearly defined notional basis.

The CVA paper should explain what this input represents, which simulated factors or event information it may require, and how it enters the Dealer loss calculation.

It should not prescribe the calibration or detailed construction of the recovery model.

Please ensure that any future choice among direct FV recovery, stressed-curve valuation and credit-aware valuation can be incorporated without changing the core CVA derivation.

Avoid unnecessary M0/M1/M2 construction details in the main paper.

7. Prioritise the open items

The current open-items list is much too long for the revised main document.

Retain only issues that could materially affect the formula or production implementation, especially:

* Confirmation of the scheduled swap obligation.
* Any contractual waterfall override.
* Close-out valuation and posted collateral treatment.
* Conditional default-loss integration in the XVA engine.
* Choice and calibration of the collateral-realisation input.
* Treatment of senior costs and optional desk LGD convention.
* Default, close-out and liquidation timing where material.

Other items can move to a technical appendix, companion methodology or implementation checklist.

Distinguish genuine blockers from optional enhancements.

8. Write for the scientific reader

Our primary feedback is that the original document is too long and difficult to understand despite being read by someone very comfortable with technical and scientific papers.

Please improve readability through structure, not by weakening the mathematics.

In particular:

* Introduce concepts before symbols.
* Explain the intuition immediately surrounding important equations.
* Use short paragraphs and direct language.
* Avoid excessive legal vocabulary.
* Avoid repeating conclusions.
* Avoid defensive explanations of why alternative approaches are wrong unless necessary.
* Avoid unnecessary subheadings.
* Keep the reasoning continuous from transaction economics to CVA.
* Prefer a few well-chosen equations over multiple equivalent formulations.

The final result should read as a concise quantitative methodology paper, not a legal opinion, research diary or collection of implementation notes.

9. Deliverables

Proceed to produce the rewritten LaTeX document.

Also provide a brief accompanying change summary that identifies:

* The consolidated model terminology and legacy mapping.
* The treatment of senior deductions and the proposed 99% LGD convention.
* The engine-compatible CVA formulation and any unverified integration assumptions.
* Major content moved to appendices or the companion methodology.
* Remaining material open issues.

If notation changes are required in the B_liq companion paper, provide a corresponding proposed patch or mapping so both documents can be updated consistently.

Do not rewrite the entire companion paper as part of this task.

Final objective: A concise, mathematically sound, reader-friendly CVA methodology whose core results remain valid independently of the eventual choice of collateral-realisation model.
