Please revise the current FVA / ColVA for Non-Cash VM white paper by adding a concise new:

    Section 6 — Liability-Side Treatment: Dealer-Posted G10 Bond Collateral

This is a LIMITED ADDITION ONLY.

Do NOT rewrite or materially restructure the existing asset-side methodology.
Do NOT build a symmetric mirror image of the asset-side framework.
Do NOT introduce unnecessary new state variables, funding channels, or policy
cases.

The purpose of Section 6 is simply to:

1. state the generic base-case liability-side methodology;
2. explain why its economics differ from the asset side;
3. document why the liability contribution is expected to be immaterial for
   the current example transaction if negative dealer exposure is negligible.

============================================================
SECTION 6 — LIABILITY-SIDE TREATMENT
============================================================

Start by defining the liability-side exposure from the dealer perspective:

    L_t = (-V_t)^+.

Thus:

    V_t > 0:
        counterparty owes dealer; this is the asset-side case treated in the
        main body of the paper.

    V_t < 0:
        dealer owes the counterparty and may be required to post collateral.

For the liability-side base case, assume:

- dealer posts eligible G10 government bonds;
- suitable G10 bonds are either already owned by the dealer/Treasury or can
  be sourced under ordinary market conditions;
- the economic cost is valued from an opportunity-cost perspective;
- there is no binding dealer-side collateral inventory cap;
- the clean/base derivative valuation is independent of the collateral
  actually posted and uses the approved clean RFR valuation convention;
- ignore more complex collateral sourcing, scarcity/specialness, balance-sheet
  and securities-borrowing effects in the base case.

------------------------------------------------------------
6.1 Contractual collateral requirement
------------------------------------------------------------

Let

    q_CSA^G10

be the contractual CSA valuation percentage applicable to the G10 bond
collateral.

The required bond market value is:

    B_t^post
        = L_t / q_CSA^G10
        = (-V_t)^+ / q_CSA^G10.

Make clear that q_CSA determines the QUANTITY / market value of bonds that
must be posted. It is not itself the economic cost of posting collateral.

Do not introduce q_reg into this derivation.

------------------------------------------------------------
6.2 Economic difference from the asset side
------------------------------------------------------------

Explain explicitly that the liability side should NOT be obtained merely by
changing the sign of the asset-side equations.

On the asset side:

    dealer receives bond collateral
        ->
    dealer can monetize/repo it
        ->
    collateral creates secured funding capacity.

On the liability side:

    dealer posts its own/sourced G10 bond collateral
        ->
    the bond becomes encumbered
        ->
    dealer loses the alternative economic use of that bond.

Therefore the natural liability-side question is:

    What is the marginal opportunity cost of encumbering the G10 bonds as VM?

Under the base case, assume that an unencumbered G10 bond could otherwise be
repoed.

Let:

    q_repo^G10
        = repo advance rate for the eligible G10 bond;

    r_repo^G10
        = repo funding rate for the G10 bond;

    r_CoF
        = dealer marginal unsecured cost of funds.

Posting B_t^post removes approximately

    F_t^lost
        = q_repo^G10 B_t^post

of alternative secured funding capacity.

If that funding capacity must be replaced at the dealer's marginal unsecured
funding rate, the instantaneous opportunity cost is:

    Cost_t^post
        = F_t^lost
          (r_CoF,t - r_repo,t^G10).

Therefore, under the paper's sign convention in which a funding cost is a
negative valuation adjustment:

    VA_L
      = - ∫ E[
            DF_t
            q_repo^G10 B_t^post
            (r_CoF,t - r_repo,t^G10)
          ] dt

or equivalently:

    VA_L
      = - ∫ E[
            DF_t
            (q_repo^G10 / q_CSA^G10)
            (-V_t)^+
            (r_CoF,t - r_repo,t^G10)
          ] dt.

This should be presented as the BASE-CASE liability-side methodology.

------------------------------------------------------------
6.3 Interpretation
------------------------------------------------------------

Explain the equation economically rather than adding more algebra.

The liability-side cost consists of:

    amount of G10 repo funding capacity forgone
        ×
    spread between replacement unsecured funding and G10 repo funding.

This is fundamentally an opportunity/encumbrance cost.

Do NOT use the G10 bond yield or asset-swap spread as the funding spread.

The G10 bond's yield/ASW determines the market value of the security where
relevant; it is not automatically the dealer's marginal cost of posting that
security.

Likewise, do NOT put the collateral bond's ASW into the clean swap discount
curve.

The clean derivative valuation remains based on the approved RFR convention.
The collateral-posting cost is added explicitly as a valuation adjustment.

------------------------------------------------------------
6.4 Why no asset-side-style three-component decomposition is needed
------------------------------------------------------------

Briefly explain why the liability side is simpler.

There is no need in this base case for the asset-side:

    S_t,
    U_t^f,
    X_t^f,

or Full Monetization vs Trade-Limited treatments.

Those quantities were needed because received non-cash collateral can create
different amounts of repo funding relative to the dealer's funding target,
including possible excess funding capacity.

Here, the dealer is posting G10 collateral.

The base-case economic effect is simply the loss of the collateral's
alternative funding use.

Do not manufacture a symmetric three-component decomposition.

------------------------------------------------------------
6.5 Relevance to the current transaction
------------------------------------------------------------

End the section by distinguishing the GENERAL methodology from the CURRENT
TRANSACTION result.

The liability-side adjustment is driven by:

    (-V_t)^+.

Therefore, if the dealer's simulated negative exposure is negligible:

    E[(-V_t)^+] ≈ 0,

then:

    B_t^post ≈ 0

and consequently:

    VA_L ≈ 0.

However, do NOT infer this merely from positive expected swap exposure.

The appropriate check is the negative-exposure profile / pathwise exposure
distribution, for example:

    ENE_t = E[(-V_t)^+].

State that preliminary inspection suggests dealer-negative exposure is
negligible for the current example transaction, so the liability-side
contribution is expected to be immaterial, subject to confirmation from the
simulated negative-exposure profile.

This is an important conclusion:

    the methodology should support the liability side generically,
    but the current trade may not materially exercise it.

------------------------------------------------------------
6.6 Scope / caveat
------------------------------------------------------------

Finish with a short paragraph, not an extensive new appendix.

State that the base case assumes ordinary G10 collateral availability and
repo economics.

More complex cases could require separate treatment if:

- the dealer must borrow specific scarce securities;
- bonds are special in repo;
- collateral sourcing incurs material securities-borrowing costs;
- Treasury applies an internal collateral transfer price;
- balance-sheet or liquidity constraints materially alter the opportunity
  cost;
- the contractual collateral arrangement differs from the assumed bilateral
  G10 posting mechanics.

These are outside the current paper's base-case scope.

============================================================
IMPORTANT PRESENTATION REQUIREMENTS
============================================================

Keep Section 6 concise — approximately 1–2 pages maximum.

The section should read as a natural completion of the existing asset-side
paper, not the beginning of another research project.

The key contrast should be immediately understandable:

    ASSET SIDE
    receive non-cash collateral
        ->
    monetize collateral
        ->
    funding benefit / shortfall

versus

    LIABILITY SIDE
    post G10 collateral
        ->
    encumber collateral
        ->
    lose alternative repo/funding use
        ->
    opportunity cost.

Do not reintroduce regulatory haircut q_reg.

Do not use bond ASW as the swap discount spread.

Do not use RFR+5 bp unless its economic meaning has been independently
confirmed.

Use the clean RFR valuation convention for V_t and the paper's approved
discounting convention for the VA cashflows.

Finally, review numbering after inserting Section 6 so that the existing
summary/appendices are renumbered naturally if required.
