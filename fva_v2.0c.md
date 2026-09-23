One final implementation clarification for Section 6.

This SUPPLEMENTS the previous liability-side and CTD instructions. Please do
not expand Section 6 materially; instead, use this to make the implementation
choice explicit and close the discussion.

============================================================
CURRENT-PRICER PROXY FOR LIABILITY-SIDE COLLATERAL
============================================================

The economically complete liability-side treatment would recognize that the
dealer has a collateral-delivery choice among:

    - cash; and
    - eligible G10 government bonds,

with G10 contractual haircuts varying by the collateral agreement (e.g.
rating and remaining maturity).

Therefore, in principle, the dealer possesses a cheapest-to-deliver (CTD)
collateral option.

However, the current pricing infrastructure does not support a faithful
dynamic CTD treatment across eligible collateral securities. In particular,
it does not model all of the required ingredients such as:

    - security-specific eligible collateral sets;
    - contractual haircut schedules;
    - dynamic collateral selection/substitution;
    - dealer inventory;
    - security-specific repo/sourcing economics;
    - repo specialness;
    - collateral optimization.

Do NOT approximate this by selecting an arbitrary "representative G10 bond"
or an average G10 haircut. That would introduce false precision without
actually representing the contractual CTD option.

Instead, for the current implementation, use CASH COLLATERAL TREATMENT as the
liability-side proxy.

This is a natural proxy because cash is itself an eligible contractual
delivery choice.

Define:

    L_t = (-V_t)^+.

Under the cash proxy, the dealer posts:

    C_t^post = L_t

and the liability-side funding adjustment is the standard cash-posting FVA:

    VA_L^proxy
      = ∫ E[
          DF_t
          L_t
          (r_base,t - r_CoF,t)
        ] dt.

Under the current clean-valuation convention:

    r_base = RFR.

Therefore:

    VA_L^proxy
      = ∫ E[
          DF_t
          (-V_t)^+
          (RFR_t - r_CoF,t)
        ] dt.

Under the paper's sign convention this is negative when:

    r_CoF > RFR.

============================================================
INTERPRETATION OF THE PROXY
============================================================

Be precise about what this approximation means.

Do NOT state that:

    "the dealer is assumed always to post cash."

Instead state that:

    "The current implementation uses cash collateral economics as a proxy
     for the dealer-posting side because the existing pricing infrastructure
     does not support dynamic cheapest-to-deliver optimization across the
     contractual set of eligible cash and G10 collateral."

The actual dealer may post G10 securities when doing so is economically
preferable.

Therefore the cash proxy does NOT explicitly value the contractual
collateral-delivery / CTD option.

It should be regarded as a transparent implementation approximation, not as
a statement about actual collateral-management behavior.

============================================================
WHY THIS PROXY IS PREFERRED TO A REPRESENTATIVE G10 ASSUMPTION
============================================================

Briefly explain that cash treatment is preferred for the current pricer
because:

1. cash is an actually permitted collateral type under the agreement;

2. standard cash FVA is already supported by the existing pricing
   infrastructure;

3. it avoids introducing an arbitrary representative G10 security or average
   contractual haircut;

4. a single representative G10 cannot faithfully reproduce CTD economics,
   because the economically optimal security depends jointly on contractual
   haircuts, inventory, repo/sourcing economics and market state;

5. the approximation is transparent and can be replaced by explicit CTD
   treatment if the infrastructure is enhanced in the future.

Do NOT claim that cash is necessarily conservative unless this has been
demonstrated quantitatively.

The CTD option generally allows the dealer to choose the economically
preferred eligible collateral, but whether the cash proxy systematically
overstates or understates the full adjustment depends on the precise
collateral/funding economics and sign conventions.

============================================================
MATERIALITY FOR THE CURRENT TRANSACTION
============================================================

Retain the previous materiality point.

The approximation only affects states in which:

    V_t < 0,

because:

    L_t = (-V_t)^+.

Therefore validate:

    ENE_t = E[(-V_t)^+].

If ENE is negligible for the current transaction, the difference between:

    full CTD treatment

and

    cash-proxy treatment

is correspondingly expected to have limited valuation impact.

This provides additional justification for using the simple cash proxy in
the current implementation, while preserving the economically correct CTD
framework as the target methodology.

============================================================
FINAL PRESENTATION
============================================================

Please keep the final Section 6 concise and distinguish three layers clearly:

    ECONOMICALLY COMPLETE METHODOLOGY
        dealer chooses CTD among eligible cash/G10 collateral

    CURRENT PRICER
        cash collateral treatment used as implementation proxy

    CURRENT TRANSACTION
        liability-side impact likely immaterial if ENE ≈ 0

Do not reopen the asset-side methodology.

Do not introduce q_reg into the pricing formula.

Contractual G10 haircuts remain relevant to the full CTD methodology, but
they are not required in the current cash-proxy implementation.

Do not attempt to assign an effective G10 posting rate or derive a synthetic
CTD formula unless the required collateral optimization inputs become
available.
