 Refine Sections 5.2–5.3 and the Bond-Value Timing Distinction

Please revise Sections 5.2–5.3 to sharpen the treatment of the bond value entering swap close-out. The current discussion is directionally correct, but it compresses several economically distinct quantities into the binary “Impaired reading” versus “Scheduled reading.”

1. Distinguish three bond-value objects explicitly

Introduce:

> B_{\tau^-}
> =
> \text{performing/pre-event bond value immediately before the credit event},
>

> B_\tau^{event}
> =
> \text{impaired/default-state value assigned to the terminated bond leg for close-out},
>

and

> B^{liq}
> =
> \text{actual/modelled collateral realization proceeds over the liquidation process}.
>

Do not identify B_\tau^{event} with B^{liq} merely because both reflect an impaired bond. Liquidation may occur after \tau, including over the contractual Liquidation Period, whereas B_\tau^{event} is conceptually the event-state value relevant to close-out.

The key timing chain is:

> B_{\tau^-}
> \quad\longrightarrow\quad
> B_\tau^{event}
> \quad\longrightarrow\quad
> B^{liq}.
>

2. Reframe the current two “readings”

Keep the documentary uncertainty, but avoid suggesting that the two interpretations are necessarily equally natural economically.

The first question is:

> \boxed{
> B_\tau^{co}=B_{\tau^-}
> \quad\text{or}\quad
> B_\tau^{co}=B_\tau^{event}\ ?
> }
>

The event-state reading recognizes the credit event when valuing the terminated bond-side cashflows.

The alternative performing/frozen reading would effectively leave the bond close-out value near B_{\tau^-}. Retain this as [TO CONFIRM] if documentary/production implementation evidence could support it, but do not infer it simply from the fact that the close-out values “scheduled cashflows.” Scheduled cashflows can still be valued conditional on the obligor having defaulted.

In particular, revise the current sentence that “scheduled cashflows without reflecting the credit event” necessarily imply a performing valuation. That conclusion requires additional contractual or implementation support.

Also retain the existing important finding that no documentary basis has been identified for mechanically freezing the bond at the last pre-default market price P_{\tau^-}.

3. Treat equality with liquidation value as a separate question

After determining the appropriate close-out reading, ask:

> \boxed{
> B_\tau^{event}\overset{?}{=}B^{liq}.
> }
>

This is distinct from the first question.

If close-out recognizes the impaired state but is independently valued at \tau, while collateral is subsequently liquidated, then in general:

> B_\tau^{event}\neq B^{liq}.
>

Define the resulting basis:

> \Delta B_\tau
> =
> B_\tau^{co}-B^{liq}.
>

It can capture liquidation delay, distressed-market liquidity/bid-offer, realization uncertainty or differences between the close-out valuation convention and actual liquidation proceeds.

Conversely, if the transaction mechanics or an explicit modeling choice justify using the collateral realization value as the bond-side close-out value, then:

> B_\tau^{co}=B^{liq}
>

and therefore:

> \Delta B_\tau=0.
>

Clearly label this as either a contractual conclusion or [MODEL CHOICE], depending on the available evidence.

The fact that the Dealer acts as both Calculation Agent and Disposal Agent may make a common valuation/realization treatment operationally plausible, but does not by itself prove the equality.

4. Connect this directly to the trade-specific bond cancellation

Retain:

> V_\tau^{co}
> =
> B_\tau^{co}
> -
> PV_\tau^{co}(\text{remaining Note-side cashflows}),
>

using the established Dealer-perspective sign convention.

Since the Dealer’s limited-recourse recovery includes the collateral realization value B^{liq},

> L_\tau^{Dealer}
> =
> \left[
> B_\tau^{co}
> -
> PV_\tau^{co}(\text{remaining Note-side cashflows})
> -
> B^{liq}
> \right]^+,
>

or:

> \boxed{
> L_\tau^{Dealer}
> =
> \left[
> \Delta B_\tau
> -
> PV_\tau^{co}(\text{remaining Note-side cashflows})
> \right]^+.
> }
>

This is the cleanest expression for understanding what remains to be modeled.

Under the trade-specific common-value treatment:

> B_\tau^{co}=B^{liq},
>

so:

> \boxed{
> L_\tau^{Dealer}
> =
> [-PV_\tau^{co}(\text{remaining Note-side cashflows})]^+.
> }
>

Then separately demonstrate from the actual Note-side cashflow structure/sign that this positive part is zero for this trade. Only after that demonstration conclude that the trade-specific CVA is zero.

5. Update Section 5.3 accordingly

The distinction between the performing-state curve and event/default-state valuation basis remains useful:

> r_{\rm base}
> \qquad\text{versus}\qquad
> r_{\rm base,def}.
>

Keep the important statement that r_{\rm base,def}, if used, is a valuation basis for terminated/default-state cashflows, whereas DF(0,t) in the CVA integral discounts the loss from t back to today. They are different objects.

However, do not present ESTR+212bp or another default-state bond model as a required input to this particular trade’s CVA calculation before determining whether B_\tau^{co} survives the structural cancellation.

Instead state conditionally:

* if B_\tau^{co} must be modeled independently from B^{liq}, a default-state valuation basis such as the proposed ESTR+212bp treatment may be required;
* if the adopted trade-specific treatment is B_\tau^{co}=B^{liq}, the absolute bond value cancels from Dealer loss, and an independent default-state bond valuation is unnecessary for calculating this trade’s CVA.

6. Update the implementation consequence

This point should flow through the remainder of the document.

If the trade-specific derivation establishes pathwise:

> L_\tau^{Dealer}=0,
>

then:

> CVA_0=0
>

structurally.

In that case, do not state that P_t simulation is nevertheless required for this trade’s CVA. No Monte Carlo simulation of P_t, exposure, collateral posting or default timing is required merely to reproduce a zero CVA integrand.

Such modeling may remain relevant for general SPIRE methodology, ordinary valuation, CSA/VM behavior, FVA/DVA, sensitivities or other trades, but it is not required for the CVA calculation of this specific trade once zero Dealer loss has been established analytically.

The governing principle should remain:

> \boxed{\text{derive first; model only quantities that survive the derivation.}}
>

Please make these changes without expanding the document unnecessarily. The goal is to sharpen the existing Sections 5.2–5.3 and ensure the later modeling/implementation sections follow consistently from the structural result, rather than adding another parallel layer of discussion.
