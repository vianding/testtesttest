Task: Update the FVA/ColVA paper to reflect the agreed ColVA2 treatment of the collateral bond price P_t, focusing primarily on §1.4 “Role and simulation of the bond price P_t” and making only consequential consistency edits elsewhere.

Work from the existing paper and preserve the current finite-pool derivation and structure wherever possible. This is a targeted modeling update, not a wholesale rewrite.

⸻

1. Preserve the existing economic role of P_t

Keep the current result

> B_t=P_tN_t^{post}
> =
> \begin{cases}
> V_t/q_{\rm CSA}, &
> V_t/q_{\rm CSA}<P_tN^{pool},\\[3pt]
> P_tN^{pool}, &
> V_t/q_{\rm CSA}\ge P_tN^{pool}.
> \end{cases}
>

Preserve the existing interpretation:

Pool available: the CSA adjusts the quantity posted,

> N_t^{post}=\frac{V_t}{q_{\rm CSA}P_t},
>

so

> B_t=P_tN_t^{post}=\frac{V_t}{q_{\rm CSA}}.
>

Thus P_t cancels from total posted market value, but still determines the amount of the finite base-notional pool consumed.

Pool exhausted: once

> N_t^{post}=N^{pool},
>

the posted collateral value becomes

> B_t=P_tN^{pool},
>

so P_t directly determines collateral/funding capacity and therefore can affect FVA/ColVA.

This is the key reason a performing-state bond-price process matters for this calculation.

⸻

2. Introduce a dedicated performing-state bond ASW parameter

Throughout this document the rate/spread notation uses r, so preserve that convention rather than introducing a new s-notation.

Define

> \boxed{r_{\mathrm{bond,ASW}}^{\mathrm{perf}}}
>

as the fixed performing-state bond asset-swap spread used in the collateral bond valuation.

Add it to the notation table, preferably near P_t, with:

> \begin{array}{lll}
> \text{Symbol} & \text{Meaning} & \text{Example}\\
> r_{\mathrm{bond,ASW}}^{\mathrm{perf}}
> &
> \text{Fixed performing-state bond ASW used in }P_t
> &
> 130\text{ bp}.
> \end{array}
>

Do not attach a t subscript to this baseline parameter:

> r_{\mathrm{bond,ASW}}^{\mathrm{perf}},
>

not

> r_{\mathrm{bond,ASW},t}^{\mathrm{perf}},
>

because the current ColVA2 assumption holds the ASW fixed through the simulation.

The value 130 bp is the current desk/example calibration, not part of the mathematical definition. Therefore use r_{\mathrm{bond,ASW}}^{\mathrm{perf}} throughout the derivation and state 130 bp only where the parameter value/example is specified.

⸻

3. Update the baseline P_t model to the agreed ColVA2 specification

The desk wants ColVA2 to value the inflation-linked collateral bond pathwise using the fixed performing-state ASW:

> r_{\mathrm{bond,ASW}}^{\mathrm{perf}}=130\text{ bp}
>

for the current case.

Represent the model schematically as

> \boxed{
> P_t^{proxy}
> =
> P^{IL}
> \left(
> r_t,\pi_t;
> r_{\mathrm{bond,ASW}}^{\mathrm{perf}}
> \right).
> }
>

Rates and inflation remain stochastic on each simulation path, and FX remains stochastic where relevant. Therefore P_t itself remains stochastic:

> (r_t(\omega),\pi_t(\omega),\ldots)
> \longrightarrow
> P_t^{proxy}(\omega).
>

What is held fixed is only the bond-specific ASW:

> r_{\mathrm{bond,ASW}}^{\mathrm{perf}}=\text{constant}.
>

Make this distinction explicit. Do not describe the bond price itself as static or deterministic.

⸻

4. Do not mechanically describe the model as “RFR + 130 bp”

Avoid hard-coding expressions such as

> DF(RFR+130bp)
>

unless this is demonstrably the exact production-pricer implementation.

An asset-swap spread is a bond-pricing input/convention and should not automatically be equated in the methodology paper with simply adding 130 bp to every RFR discount rate.

Prefer the implementation-neutral representation

> P^{IL}
> \left(
> r_t,\pi_t;
> r_{\mathrm{bond,ASW}}^{\mathrm{perf}}
> \right).
>

If the actual pricer mechanically converts the ASW into a particular discount/spread treatment, that implementation detail can be documented separately once confirmed.

⸻

5. Update the existing “Baseline model for P_t” paragraph

Replace the current implication that no bond-specific spread is present.

The revised text should convey approximately:

[MODEL CHOICE] Baseline model for P_t. The collateral IL bond is valued pathwise using the simulated rates and inflation factors, together with a fixed performing-state bond asset-swap spread r_{\mathrm{bond,ASW}}^{\mathrm{perf}}; FX is also stochastic where relevant. For the current desk case, r_{\mathrm{bond,ASW}}^{\mathrm{perf}}=130 bp. Thus P_t varies pathwise through the existing market factors, while no additional stochastic bond-specific credit-spread or liquidity factor is introduced.

Preserve the existing statement that P_tN^{pool} is generated on each simulation path.

⸻

6. Keep the FVA treatment conceptually separate from the CVA default-state collateral work

Do not import the CVA RFV/RMV recovery taxonomy into this section.

The two problems use the bond for different purposes:

> \boxed{
> \begin{aligned}
> \text{CVA:}&\quad B_\tau^{liq}
> &&\text{default-state collateral realization},\\
> \text{FVA/ColVA:}&\quad P_t
> &&\text{performing-state collateral market value/capacity}.
> \end{aligned}}
>

For CVA, a performing-state bond-price process may be unnecessary if B_\tau^{liq} can be modeled directly.

For FVA/ColVA, P_t genuinely matters during survival because it determines posted quantity, finite-pool consumption and, once the pool is exhausted, the collateral market value/funding capacity.

Therefore do not introduce the CVA default-state RFR+200 bp assumption into this FVA model. The two spread assumptions serve different purposes.

The FVA parameter

> r_{\mathrm{bond,ASW}}^{\mathrm{perf}}
>

is specifically a performing-state bond-price assumption.

⸻

7. Make the ColVA2 calculation chain explicit

The baseline calculation should be clear conceptually as

> (V_t,r_t,\pi_t)
> \longrightarrow
> P_t^{IL}
> \left(r_{\mathrm{bond,ASW}}^{\mathrm{perf}}\right)
> \longrightarrow
> B_t
> =
> \min\left(
> \frac{V_t}{q_{\rm CSA}},
> P_tN^{pool}
> \right)
> \longrightarrow
> F_t
> \longrightarrow
> \text{ColVA}.
>

Preserve the existing repo/funding mechanics after B_t, including

> F_t=\rho q_{\rm repo}B_t
>

or the precise notation already established in the paper.

The purpose of the bond-price model is therefore not to produce a bond-price forecast for its own sake; it supplies the pathwise collateral value required by the finite-pool/funding-capacity mechanics.

⸻

8. Preserve richer bond-spread dynamics only as an optional refinement

The paper may retain the existing richer-model discussion, but update it to distinguish the fixed-ASW baseline from a genuinely stochastic spread model.

Baseline:

> r_{\mathrm{bond,ASW}}^{\mathrm{perf}}
> =\text{constant}.
>

A future refinement could instead introduce

> r_{\mathrm{bond,ASW},t}
>

or an economically motivated decomposition such as

> r_{\mathrm{bond,ASW},t}
> =
> r_t^{credit}
> +
> r_t^{liq/idio},
>

potentially linking the credit component to an existing simulated sovereign credit state.

Do not present this richer model as automatically superior. Its incremental value for FVA/ColVA exists only if stochastic spread dynamics materially alter

> P_tN^{pool},
>

and hence the probability, timing or severity of pool exhaustion and the resulting funding shortfall.

The richer model should preserve rather than duplicate the existing stochastic rates, inflation and FX factors.

⸻

9. State the modeling principle succinctly

The section should make clear that the baseline is an intentional reduced-form engineering choice:

The required fidelity of the collateral bond model is determined by how P_t enters the FVA/ColVA calculation. The baseline therefore captures pathwise rate and inflation effects while holding the performing-state bond ASW fixed. Additional stochastic spread dynamics are warranted only if they materially affect finite-pool exhaustion or funding capacity.

Do not expand this into a general discussion of defaultable-bond pricing.

⸻

10. Review the rest of the FVA/ColVA paper for consistency

Search the full document for statements concerning:

* the definition or simulation of P_t;
* the baseline collateral bond model;
* absence/presence of bond-specific spread;
* ColVA2;
* pool exhaustion;
* stochastic credit/spread dynamics;
* any hard-coded references to 130 bp.

Make only the minimum consequential edits required.

Use

> r_{\mathrm{bond,ASW}}^{\mathrm{perf}}
>

consistently as the model parameter, with 130 bp appearing as the current desk/example calibration rather than being embedded throughout the mathematical formulation.

Do not introduce the CVA 200 bp default-state proxy into this paper’s performing-state bond-price specification.

Editorial requirement: Keep §1.4 concise and preserve the existing finite-pool derivation. The important update is to formalize ColVA2 as stochastic rates/inflation + fixed r_{\mathrm{bond,ASW}}^{\mathrm{perf}}, while clearly separating this from any future stochastic bond-spread model. Avoid unnecessary new notation or theoretical material.
