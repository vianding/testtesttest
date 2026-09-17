Next Revision of SPIRE CVA Methodology

Please produce the next revision of the SPIRE CVA methodology note. This revision should not simply append the latest findings to the existing draft. Reorganize the document so that the argument is visibly top-down, and incorporate the latest contractual/economic findings concerning Dealer recourse, swap close-out, collateral liquidation, and the potential cancellation of the bond economics for this specific trade.

The objective is to make the document substantially easier to reason about:

> \boxed{
> \text{General CVA framework}
> \rightarrow
> \text{SPIRE contractual mechanics}
> \rightarrow
> \text{event loss}
> \rightarrow
> \text{modeling of its components}
> \rightarrow
> \text{trade-specific simplification}
> \rightarrow
> \text{CVA}.
> }
>

Do not let detailed discussions of sovereign bond pricing, recovery conventions, CSA posting, Laurent, liquidation, curves, etc. obscure this hierarchy.

⸻

1. Reorganize the document around a top-down loss framework

Start from the general CVA expression:

> CVA_0
> =
> E^Q[D(0,\tau)L_\tau^{Dealer}]
>

or equivalently

> CVA_0
> =
> \int_0^T
> DF(0,t)
> E^Q[L_t^{Dealer}\mid\tau=t]
> (-dS(t)).
>

Explain briefly and precisely:

* 0 is the valuation date;
* Q is the pricing/risk-neutral measure;
* S(t) is the survival probability implied by the hazard-rate curve calibrated from market CDS spreads, so the default distribution is already a pricing-measure/default-intensity object;
* -dS(t) is the marginal probability of the modeled loss-relevant event occurring around t;
* DF(0,t) discounts a loss occurring at t to the valuation date. Do not automatically identify this with any default-state bond valuation curve such as ESTR+212bp.

Then make the Dealer event loss the central object:

> \boxed{
> L_\tau^{Dealer}
> =
> [V_\tau^{co}-B_\tau^{avail}]^+
> }
>

where:

* V_\tau^{co} is the Dealer’s contractual swap close-out claim/value following the loss-relevant event;
* B_\tau^{avail} is the value of the limited-recourse resources ultimately available to satisfy that claim through the SPIRE liquidation/waterfall mechanics.

The remainder of the methodology should essentially answer two questions:

> \boxed{1.\quad\text{How do we determine }V_\tau^{co}?}
>

> \boxed{2.\quad\text{How do we determine }B_\tau^{avail}?}
>

Everything else in the existing paper should be subordinated to one of these questions or to the modeling of \tau.

⸻

2. Explicitly map the existing material into this hierarchy

Reorganize rather than discard useful analysis.

The contractual transaction architecture should establish:

> \text{Original Collateral Default}
> \rightarrow
> \text{Early Redemption Trigger}
> \rightarrow
> \text{swap termination + collateral liquidation}
> \rightarrow
> \text{Available Proceeds/waterfall}
> \rightarrow
> L_\tau^{Dealer}.
>

Retain the important distinction among:

* Original Collateral;
* the asset/cashflows economically represented in the swap;
* CSA collateral;
* Mortgaged Property / limited-recourse assets.

They overlap materially for this trade but are legally/economically distinct concepts.

The pre-default section should contain V_t, B_t, CSA posting, 85% Valuation Percentage, MTA, Delivery Cap, N_t^{post}, finite-pool/cap exhaustion and the pre-default sovereign-credit dynamics.

Make clear that these quantities describe performing-state exposure and collateralization. In particular, N_t^{post} remains relevant to pre-default CSA mechanics, but it should no longer automatically be treated as the amount limiting Dealer recovery after the contractual unwind.

The event-state section should contain V_\tau^{co}, default/restructuring value, liquidation realization, Available Proceeds, waterfall priority and L_\tau^{Dealer}.

The discussions of Laurent, RMV/RFV/RT, credit-risky bond pricing, bond yield, R_{\rm eff}, ESTR+5bp, ESTR+212bp, liquidation delay and recovery should be placed underneath the particular quantity they are intended to model. Do not present these as parallel modeling topics without saying which term in the loss equation they affect.

⸻

3. Update the treatment of Dealer recourse at the event

Incorporate the latest documentary finding.

The Dealer’s ultimate recovery upon the contractual unwind should not be represented merely by the value of the Original Collateral quantity posted under the CSA immediately before default, i.e. do not use

> B_\tau^{post}
> =
> P_\tau N_{\tau-1}^{post}
>

as the general post-event recovery limit.

The Base Prospectus establishes limited recourse to the Series’ Mortgaged Property, with the relevant collateral/assets liquidated and Available Proceeds applied according to the contractual waterfall. Amounts owing to the Swap Counterparty rank ahead of the Noteholders, subject to the applicable senior costs/items and precise priority provisions.

Therefore distinguish:

> N_t^{post}
>

as a pre-event CSA posting quantity, from

> B_\tau^{avail}
>

as the post-event limited-recourse resources available to satisfy the Dealer claim.

Do not say that the Dealer “owns the entire pool upon default.” The more accurate statement is that its contractual claim has priority recourse, through the liquidation/waterfall, to the relevant Series assets/Mortgaged Property subject to the contractual priority structure.

⸻

4. Reframe the bond modeling problem

Previously the paper spent substantial effort on modeling a sovereign bond price P_\tau, including:

* pre-default credit-risky bond valuation / bond-yield approaches;
* Laurent’s reduced-form treatment and RMV/RFV/RT concepts;
* default/restructuring value;
* the proposed

> B^{liq}=R_{\rm eff}IR_\tau N
>

treatment.

Do not simply delete this analysis, but reframe it.

There are potentially two economically different bond quantities:

> B_\tau^{co}
> =
> \text{bond-leg value entering }V_\tau^{co}
>

and

> B^{liq}
> =
> \text{value/proceeds realized from the collateral through liquidation}.
>

In a sufficiently general SPIRE transaction these need not be identical.

If useful, write

> B_\tau^{co}
> =
> P_\tau^{co}IR_\tau N^{pool}
>

and

> B^{liq}
> =
> R^{liq}IR_\tau N^{pool},
>

rather than misleadingly giving both quantities the same spot-time P_\tau notation.

R^{liq} may represent proceeds realized over the contractual Liquidation Period rather than an observable market price exactly at \tau.

The potential modeling object is therefore the basis

> \boxed{
> \Delta B_\tau=B_\tau^{co}-B^{liq}.
> }
>

A nonzero basis could arise from differences between close-out valuation and actual realization, liquidation delay, distressed-market liquidity/bid-offer, restructuring uncertainty or other contractual valuation differences.

Do not, however, assume such a basis exists merely because it can be modeled.

⸻

5. Incorporate the timing issue explicitly

Do not casually write P_\tau=P_\tau^{liq} without acknowledging that liquidation may occur after the default/termination event.

Distinguish:

> \tau=\text{Original Collateral Default / contractual event time}
>

from the subsequent liquidation process and realization of proceeds.

The trade has a 30-Reference-Business-Day Liquidation Period Cut-off.

The relevant documentary question is therefore not simply whether a “default price” equals a “liquidation price”, but whether the contractual close-out amount may use or ultimately be determined using proceeds/information realized during the liquidation process.

The Base Prospectus contains evidence that, at least for certain early-termination collateral mechanics, value used in calculating the termination payment can be based on proceeds realized by the Disposal Agent within the Liquidation Period. Use this as relevant structural evidence, but do not overextend that provision to this bond leg unless the documentation actually supports doing so.

Also note that for this trade the Calculation Agent and Disposal Agent are the Dealer. This makes operational coordination of valuation and realization plausible but does not by itself establish contractual equality between close-out value and liquidation proceeds. Nor should the methodology assume opportunistic Dealer misvaluation. Use contractual valuation standards/actual realization mechanics rather than hypothesizing strategic marking.

⸻

6. Add a dedicated trade-specific structural simplification section

This is an important new finding and should be clearly separated from the reusable methodology.

Do not modify the general loss framework to assume cancellation.

First retain:

> L_\tau^{Dealer}
> =
> [V_\tau^{co}-B_\tau^{avail}]^+.
>

Then, in a dedicated section such as:

“Trade-Specific Structural Simplification”

analyze this particular transaction.

The swap economics can be decomposed schematically into:

> V_\tau^{co}
> =
> B_\tau^{co}
> +
> PV_\tau^{co}(\text{remaining Note-side cashflows})
>

with signs stated explicitly from the Dealer perspective. If the actual convention in the existing implementation uses the opposite sign for the Note leg, use that convention consistently rather than forcing this notation.

The crucial observation is that the same Original Collateral economically forms the bond-side component of the swap and the principal limited-recourse asset available through the liquidation waterfall.

Therefore:

> V_\tau^{co}-B_\tau^{avail}
>

potentially contains an offsetting bond component.

In the general case:

> L_\tau^{Dealer}
> =
> \left[
> \underbrace{B_\tau^{co}-B^{liq}}_{\Delta B_\tau}
> +
> PV_\tau^{co}(\text{Note-side cashflows})
> +\text{any other relevant waterfall adjustments}
> \right]^+.
>

If, for this transaction and under the adopted modeling treatment,

> B_\tau^{co}=B^{liq},
>

then the entire sovereign bond component cancels:

> \boxed{
> L_\tau^{Dealer}
> =
> [PV_\tau^{co}(\text{remaining Note-side cashflows})]^+
> }
>

subject again to the chosen Dealer-side sign convention.

This is a trade-structure-specific result, not a general SPIRE CVA identity.

⸻

7. Carefully establish whether the trade-specific result actually implies zero CVA

Do not jump directly from bond cancellation to CVA=0.

First demonstrate, using the actual cashflow/sign structure of this trade, whether the residual Note-side term can ever represent a positive Dealer claim after the Original Collateral Default.

If it cannot, then:

> L_\tau^{Dealer}=0
>

pathwise for this modeled event, and consequently

> \boxed{CVA_0=0}
>

for this particular loss channel.

Present this as a structural consequence of this trade’s cashflow and collateral architecture, not as an assumption and not as a generic statement that SPIRE transactions have zero CVA.

Also be precise about scope: if other contractual events could generate Dealer loss through a different mechanism, the zero result for Original Collateral Default does not automatically prove that every conceivable SPIRE counterparty-loss channel is zero.

⸻

8. Reassess how much sovereign bond modeling remains necessary

After deriving the trade-specific cancellation, revisit the existing Laurent / credit-risky bond / R_{\rm eff} material.

Do not remove technically valid material merely because it cancels for this trade. Instead classify it correctly:

General methodology: sovereign bond/default-state valuation may matter for B_\tau^{co}, B^{liq}, their basis, pre-default V_t, CSA collateral capacity, cap exhaustion and event timing.

This particular trade’s CVA: if the common-value treatment

> B_\tau^{co}=B^{liq}
>

is justified and the bond component therefore cancels, sophisticated modeling of the absolute event-state bond value is not required to calculate Dealer loss from this event.

This distinction is important. Do not continue developing a sophisticated P_\tau model merely because earlier drafts assumed one was necessary.

Laurent/RMV/RFV/RT should therefore be retained only to the extent that they:

1. support the general methodology;
2. explain possible modeling of event-state collateral value or close-out/liquidation basis;
3. remain relevant to pre-default pricing/collateral dynamics; or
4. provide useful sensitivity analysis.

They should not dominate the trade-specific CVA calculation if the relevant bond value cancels algebraically.

⸻

9. Preserve unresolved points honestly

Do not manufacture contractual certainty.

In particular, unless documentary or implementation evidence establishes it, do not state as contractual fact that

> B_\tau^{co}=B^{liq}.
>

It may instead be adopted as the cleanest modeling treatment for this trade, with a sensitivity around

> \Delta B_\tau\neq0.
>

Likewise, do not state that the bond close-out must use the last pre-default market price P_{\tau^-}. We currently have no documentary basis for freezing the bond value immediately before default.

Do not introduce ISDA close-out conventions unless the transaction documentation actually incorporates or supports them. We have not identified an ISDA reference in the relevant trade documentation.

Keep genuinely unresolved contractual or implementation matters as [TO CONFIRM]; keep genuine modeling assumptions as [MODEL CHOICE].

⸻

10. Desired final structure

The revised paper should read approximately:

1. Scope and transaction architecture
Define SPIRE, Dealer, Notes, Original Collateral, swap, CSA, Mortgaged Property and modeled event.

2. General CVA and Dealer-loss framework
CVA_0, Q, S(t), \tau, DF, L_\tau^{Dealer}.

3. Performing-state economics and collateralization
V_t,B_t,N_t^{post}, CSA 85%, MTA, Delivery Cap, finite pool, pre-default sovereign-credit dynamics.

4. Contractual transition at Original Collateral Default
Trigger → early redemption → swap termination → liquidation → waterfall.

5. Dealer close-out claim V_\tau^{co}
Swap-leg decomposition, close-out valuation assumptions and relevant curves.

6. Available limited-recourse resources B_\tau^{avail}
Default/restructuring value, liquidation, Available Proceeds, waterfall and timing.

7. General event-loss model
Combine Sections 5 and 6 and introduce \Delta B_\tau=B_\tau^{co}-B^{liq} where appropriate.

8. Trade-specific structural simplification
Demonstrate bond-leg offset/cancellation and determine whether the remaining Note-side cashflows imply pathwise zero Dealer loss.

9. CVA result and sensitivities
State the resulting trade-specific CVA implication and, where useful, sensitivity to nonzero close-out/liquidation basis.

10. Modeling implementation / inputs / limitations
CDS hazard calibration, curves, Laurent/recovery treatment where still relevant, implementation mapping and remaining [TO CONFIRM] items.

The main discipline for this revision is:

> \boxed{
> \text{derive the economics first; model only quantities that survive the derivation.}
> }
>

Do not preserve modeling complexity simply because it appeared in an earlier draft. Conversely, do not turn a trade-specific cancellation into a general SPIRE assumption. The final document should make it immediately apparent what is contractual fact, what is general methodology, what is a modeling choice, and what simplifies only because of the particular structure of this trade.
