Task: Rewrite/update Section 6, “Modelling Default-State Collateral Realisation B_\tau^{liq},” of the main CVA white paper.

Please use the current Section 6 as the starting point rather than creating a new standalone treatment. Preserve its scope and useful structure: this section is specifically about defining and modelling B_\tau^{liq}. The general CVA formula, exposure construction, close-out and loss calculation are covered elsewhere in the paper and should not be re-derived here.

The recent modeling discussion has clarified the conceptual structure and changed the practical baseline. Revise Section 6 accordingly, keeping it concise and preferably no longer than the current section.

1. Preserve the central distinction already present in §6.1, but sharpen it.

Retain the useful conceptual chain

> B_{\tau^-}(N)
> \xrightarrow{\text{sovereign credit event}}
> B_\tau^{def}(N)
> \xrightarrow{\text{SPIRE Liquidation Period}}
> B_\tau^{liq}(N).
>

Clarify that B_{\tau^-} is the defaultable bond’s market value immediately before the event. “Pre-default” does not mean healthy or normally performing; the bond may already be severely distressed at \tau^-.

Most importantly, make clear that B_\tau^{liq}, not B_{\tau^-}, is the quantity this section ultimately needs to provide to the CVA framework. The three quantities are conceptual stages, not three independent state variables that must necessarily be simulated.

This creates two distinct modeling questions:

> \text{pre-default bond pricing }B_t
> \qquad\text{vs.}\qquad
> \text{default-state realization }B_\tau^{liq}.
>

Section 6 is primarily concerned with the second. Correct modeling of the first is necessary only when the chosen realization convention uses B_{\tau^-}, or where pre-default dynamics otherwise feed into the realization model.

2. Introduce the standard recovery conventions briefly as the organizing framework for B_\tau^{def}/B_\tau^{liq}.

Use the Duffie/Duffie–Singleton taxonomy to distinguish the recovery base:

> \begin{aligned}
> \text{RFV-like:}&\quad R\,N_\tau,\\
> \text{RT-like:}&\quad R\,P_\tau^{df},\\
> \text{RMV-like:}&\quad R\,B_{\tau^-}^{risky}.
> \end{aligned}
>

For inflation-linked collateral, N_\tau naturally becomes IR_\tau N.

Explain these very briefly:

* RFV: recovery against face/index-adjusted principal;
* RT: recovery against the value of the otherwise equivalent default-free bond;
* RMV: recovery against the defaultable bond’s immediately pre-default market value.

The purpose is not to turn Section 6 into a literature review. The taxonomy simply gives a clean theoretical interpretation of the candidate B_\tau^{liq} models. Cite Duffie/Duffie–Singleton and Laurent et al. appropriately; leave detailed literature analysis to the companion B_{\rm liq} working paper.

3. Recast current M0 as the clean RFV-like benchmark, rather than the implementation baseline.

Preserve

> B_\tau^{liq}(N)\approx R_{\rm eff}IR_\tau N.
>

Explain that this is naturally an inflation-indexed RFV-like realization rule. It directly specifies the amount realized at the event without requiring a full simulated pre-default bond MV.

Preserve the useful existing interpretation of R_{\rm eff}: an effective all-in realization factor that may summarize restructuring/recovery and incremental SPIRE liquidation effects, with calibration based on appropriate empirical evidence and with care to avoid double counting.

Also retain the useful restructuring-package discussion in current §§6.2–6.3 where appropriate.

However, update the implementation conclusion. M0 is conceptually clean but cannot currently be represented faithfully through the available standard product/PV infrastructure. A conventional IL product projects future inflation and discounts future cashflows, whereas M0 requires the event-time inflation-adjusted principal IR_\tau N without introducing a maturity PV operation.

Therefore present M0 as a conceptual/reference benchmark, not the current production baseline.

4. Make the current practical baseline the existing RMV-style proxy.

Reflect the actual current implementation constraint. The baseline currently approximates the credit-event-state bond value by valuing the IL bond using a stressed/default-state discount curve, approximately

> B_\tau^{proxy}
> =
> PV_\tau^{IL}\!\left(DF^{RFR+s^*}\right),
>

with s^* currently represented by the default-state spread assumption (approximately 200 bp where applicable).

Then the realization is represented schematically as

> B_\tau^{liq}
> \approx
> R_{\rm eff}B_\tau^{proxy},
>

subject to checking the precise current implementation so that recovery is not counted twice.

Characterize this carefully as an RMV-style proxy, not exact Duffie–Singleton RMV. The stressed discount spread is standing in for credit deterioration that is not explicitly represented inside the IL bond object.

Do not claim RFR+200 bp is theoretically equivalent to a stochastic intensity model. Its justification is pragmatic: for this CVA application we primarily need an adequate approximation to the collateral value relevant at the credit event, rather than a perfect bond-price process at every performing-state observation.

5. Replace/recast current §6.4 with the natural full-RMV refinement.

The richer alternative is to make the IL bond itself credit-risky/defaultable:

> B_{\tau^-}^{risky}
> =
> B(r_\tau,\pi_\tau,\lambda_{\tau^-},\ldots),
>

and use that pre-default value as the RMV recovery base:

> B_\tau^{liq}
> =
> R_{\rm liq}\,B_{\tau^-}^{risky},
>

again with recovery notation chosen carefully to avoid double counting recovery already embedded in the defaultable-bond pricing framework.

This requires the sovereign credit state/credit curve to enter the IL bond valuation. Conceptually, the desired architecture is

> \lambda_t
> \rightarrow
> \text{pathwise credit curve}
> \rightarrow
> \text{credit-aware IL bond pricer}
> \rightarrow
> B_t^{risky}.
>

Existing credit dynamics (e.g. those available through LGM_IR_credit_FX) may provide the simulated credit state, but implementation would require wiring the resulting pathwise credit information into the IL bond pricer. Keep this at a high level; code design belongs in the companion working paper.

6. Be explicit about why full credit-driven RMV is useful — and why its benefit is bounded for this CVA use case.

Its main advantage is not simply that it is a “more correct bond model.” It permits

> \lambda_t\uparrow
> \Rightarrow
> B_t^{risky}\downarrow
>

pathwise, preserving the relationship between sovereign credit deterioration, event timing and collateral value. This becomes particularly relevant for state dependence and wrong-way-risk effects.

However, the incremental benefit is bounded by what the CVA framework actually consumes. If the current stressed-spread proxy adequately approximates the relevant conditional default-state collateral values, building a full stochastic-\lambda bond process may add limited CVA benefit relative to its implementation complexity.

This should be stated neutrally as a model/implementation trade-off requiring validation, rather than presenting full RMV as automatically superior.

7. Clarify the role of the current RFR+200 bp assumption.

If the +200 bp spread exists to proxy the missing sovereign credit component, then a future implementation that explicitly incorporates \lambda_t/a pathwise credit curve into the bond should replace, rather than simply add to, that credit proxy. Otherwise sovereign credit could be double counted.

Any residual spread retained alongside explicit credit dynamics would need a separate interpretation, e.g. liquidity/basis, and separate justification.

8. End with a short model hierarchy, not another CVA derivation.

The section should leave the reader with three clearly distinguished alternatives:

> \begin{array}{lll}
> \text{RFV-like benchmark} &
> B_\tau^{liq}\sim R\,IR_\tau N &
> \text{conceptually direct; current infra limitation},\\
> \text{RMV proxy (current baseline)} &
> B_\tau^{liq}\sim R\,PV_\tau^{IL}(RFR+s^*) &
> \text{practical default-state approximation},\\
> \text{credit-driven RMV} &
> B_\tau^{liq}\sim R\,B(r_\tau,\pi_\tau,\lambda_{\tau^-}) &
> \text{pathwise credit/state-dependent refinement}.
> \end{array}
>

These should not be presented as a ranking from inferior to superior. They make different assumptions and have different implementation requirements.

Preserve the current paper’s boundary with the companion B_{\rm liq} working paper: Section 6 should explain what alternatives exist, what the baseline is, and why. Detailed functional forms for R_{\rm liq}, calibration, sovereign-event evidence, Laurent-style defaultable-bond construction, spread decomposition, validation of the 200 bp proxy, and model comparisons belong in the companion paper.

Editorial instruction: Work from the existing Section 6 text and preserve good material rather than rewriting for stylistic novelty. Keep the result concise, preferably at or below the current length. Do not repeat the general CVA formula or re-explain Section 7. Make only minimal consequential edits elsewhere in the main paper if terminology such as M0/M1 or “performing-state value” is now inconsistent.
