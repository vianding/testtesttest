Please revise the attached SPIRE Sovereign Bond Collateral CVA white paper, with primary focus on Section 6 (“Modelling Default-State Collateral Realisation B_liq”), but review the ENTIRE document for consistency because the treatment of B_liq has evolved materially.

IMPORTANT: This is a revision/consolidation exercise, not an instruction to make the paper longer.

The base white paper should now focus on the OVERALL CVA FRAMEWORK:

    contractual mechanics
        -> loss-relevant event tau
        -> Dealer close-out claim V_tau^co
        -> collateral/resources accessible to Dealer
        -> B_tau^liq / B_tau^avail
        -> Dealer loss L_tau
        -> CVA.

Detailed modeling and calibration of sovereign default-state collateral liquidation value B_tau^liq will be developed in a SEPARATE methodology paper.

Therefore, Section 6 of THIS paper should define the B_liq problem, establish the baseline model used by the CVA framework, explain the conceptual alternatives sufficiently to justify the modeling choice, and clearly define the interface to the separate B_liq methodology. It should NOT become a detailed sovereign recovery-modeling paper.

============================================================
1. UPDATED MODELING POSITION
============================================================

The current baseline / first implementation remains:

    B_tau^liq(N) ~= R_eff * IR_tau * N,

where R_eff is an effective all-in realization per unit of index-adjusted old principal.

R_eff is intended to approximate the market value/proceeds ultimately realizable through the SPIRE liquidation process. It is NOT:
- a universal sovereign recovery rate,
- a CSA Valuation Percentage,
- vehicle LGD,
- regulatory haircut,
- automatically a recovery-of-face convention.

Call this baseline “M0” if useful:

    M0: R_tau^liq = R_bar_eff.

This is still the appropriate transparent baseline for the CVA framework.

However, subsequent research has identified a meaningful potential refinement:

    M1: R_tau^liq = g(lambda_{tau-}, Z),

or in its simplest form

    R_tau^liq = g(lambda_{tau-}),

where lambda_t is the stochastic sovereign default-intensity / credit-state process and Z may represent relevant instrument characteristics.

The economic motivation is important:

    lambda_t
       -> default timing tau

but lambda_{tau-} may ALSO contain information about how the sovereign arrived at default and therefore about conditional recovery / liquidation severity.

Thus the same sovereign credit state can potentially drive:

    lambda_t
       -> survival/default probability
       -> tau

and

    lambda_{tau-}
       -> conditional R_tau^liq
       -> B_tau^liq.

This creates a coherent state-dependent recovery architecture rather than introducing an unrelated stochastic recovery mark.

DO NOT develop M1 fully in this base CVA paper. Mention it as a natural model refinement and point detailed construction/calibration to the separate B_liq methodology.

============================================================
2. IMPORTANT CHANGE TO THE OLD “BOND PRICE SIMULATION” DISCUSSION
============================================================

The existing Section 6.4 is now too centered on P_{tau-} and should be substantially revised or compressed.

The current conclusion is:

    Do NOT simulate bond price merely because the collateral is a bond.

A stochastic variable should be introduced only if it survives into the CVA loss functional.

Under M0,

    B_tau^liq = R_eff * IR_tau * N,

so pre-default bond price P_t is NOT required to determine B_tau^liq.

If a richer model is eventually required, the preferred primitive credit state is NOT raw bond price.

The emerging decomposition is:

    y_t^bond
        = y_t^benchmark
        + s_t^credit
        + s_t^idio,

with

    s_t^credit = F(lambda_t, recovery assumptions, term structure, ...).

Equivalently,

    s_t^bond = F(lambda_t, ...) + s_t^idio.

lambda_t should drive the sovereign-credit component because it is already the state variable generating default probabilities/default timing.

The residual s_t^idio may capture bond-specific liquidity, technicals and other idiosyncratic distress.

If empirical evidence ultimately shows that bond-specific spread contains material incremental information about SPIRE liquidation recovery, a still-richer model could be:

    R_tau^liq
      = g(lambda_{tau-}, s_{tau-}^idio, Z).

But that is NOT part of the baseline CVA methodology and should not be developed at length here.

Also note:

- if discussing a bond spread, do not mechanically define the benchmark as another bond from the same sovereign, because doing so may remove much of the sovereign-credit state we are trying to retain;
- an appropriate non-credit benchmark (e.g. swap/OIS/reference rate/real-rate curve depending on the instrument and currency) may be more appropriate;
- exact benchmark specification belongs in the separate B_liq methodology.

Do NOT propose simulating outright bond yield independently from lambda unless there is an identified incremental purpose. That can duplicate the sovereign-credit state and also reintroduce rates/inflation factors already modeled elsewhere.

============================================================
3. SECTION 6 SHOULD BECOME SHORTER AND MORE MODULAR
============================================================

Please redesign Section 6 approximately along these lines. Use judgment on exact headings and wording.

6. Modelling Default-State Collateral Realisation B_tau^liq

6.1 What quantity the CVA framework requires

Retain the useful distinction:

    B_{tau-}
       -> B_tau^def
       -> B_tau^liq

where:

B_{tau-}:
performing-state market value immediately before the event.

B_tau^def:
market value of the resulting sovereign default/restructuring claim or package.

B_tau^liq:
value/proceeds ultimately realizable through the SPIRE liquidation process.

Emphasize that these are CONCEPTUAL stages, not three quantities that must independently be simulated.

The CVA framework ultimately requires B_tau^liq because that is what can support the Dealer claim through the waterfall.

Keep the useful timing clarification that B_tau^liq is associated with event tau for CVA purposes even though actual realization occurs during the subsequent contractual Liquidation Period.

6.2 Baseline model M0: effective all-in realization

State:

    B_tau^liq(N)
        ~= R_eff * IR_tau * N.

Define R_eff carefully.

Explain why under M0 no collateral bond-price simulation is required for B_liq.

This should be the production/baseline modeling choice used by the rest of the CVA paper.

6.3 What lies behind R_eff

Briefly explain that actual sovereign default can produce a restructuring package:

    B_tau^def = PV_tau(R_package,tau)

followed by SPIRE liquidation:

    B_tau^liq
        = B_tau^def - C_tau^liq
        = (1-h_tau^liq) B_tau^def

schematically.

Then explain that M0 compresses this chain into R_eff.

Retain the important anti-double-counting point:

If R_eff is calibrated from distressed traded prices, CDS auction values, restructuring-package market values or post-default prices that already contain distress/liquidity effects, do NOT mechanically apply another generic liquidation haircut.

Any separate h_liq must represent an incremental SPIRE-specific forced-sale effect not already embedded in the calibration source.

The contractual 30-Reference-Business-Day liquidation / forced-sale provision remains a candidate source of such incremental effect.

6.4 State-dependent recovery as a refinement

Replace most of the existing “pre-default-price-dependent models” discussion with a concise introduction to M1:

    M1:
    R_tau^liq = g(lambda_{tau-}, Z).

Explain the economic motivation:
lambda_t is calibrated from sovereign credit markets and generates survival/default probabilities; lambda_{tau-} also describes the sovereign credit state immediately before default and may contain information about conditional recovery severity.

Therefore:

    lambda_t
       -> tau

and potentially

    lambda_{tau-}
       -> R_tau^liq
       -> B_tau^liq.

Mention, but DO NOT fully develop, that richer alternatives can test whether bond-specific spread/liquidity information adds explanatory power beyond lambda:

    s_t^bond
       = F(lambda_t) + s_t^idio,

    R_tau^liq
       = g(lambda_{tau-}, s_{tau-}^idio, Z).

The detailed empirical comparison between

    M0: R = constant,
    M1: R = g(lambda),
    M2: R = g(s_bond),
    M3: R = g(lambda, s_idio, Z)

belongs in the separate B_liq methodology, NOT this paper.

6.5 Calibration / evidence / model boundary

Compress the current literature discussion significantly.

The base paper only needs enough literature to establish:

1. sovereign restructuring recovery/haircuts vary substantially across episodes;
2. instrument characteristics such as maturity can matter;
3. pre-default market credit conditions contain information about subsequent restructuring severity;
4. therefore constant R_eff is a transparent baseline rather than a claim of universal constant recovery;
5. state-dependent recovery is a legitimate refinement requiring separate empirical calibration.

Keep Laurent et al., Sturzenegger–Zettelmeyer, Cruces–Trebesch, Asonuma et al. and Greek evidence where useful, but move detailed literature discussion to the separate B_liq methodology / appendix.

Do NOT imply that Laurent establishes the actual SPIRE liquidation recovery. Laurent is useful for internally consistent defaultable-bond recovery conventions and pre-default pricing, not for determining actual SPIRE proceeds.

End Section 6 with an explicit modeling boundary such as:

    “The present CVA methodology therefore uses M0 as the baseline
    representation of default-state collateral realization. Construction,
    calibration and validation of state-dependent recovery models, including
    dependence on the pre-default sovereign credit state, are addressed in
    the separate Default-State Collateral Realisation Methodology.”

Use better wording if appropriate.

============================================================
4. DO NOT LOSE THE CORE DISTINCTION BETWEEN CLAIM AND COLLATERAL
============================================================

Review the entire document to ensure that nothing in the old B_liq discussion has leaked into the Dealer close-out claim.

V_tau^co is evaluated using the REGULAR SWAP CALCULATOR / contractual scheduled swap cashflows.

Do NOT put sovereign distressed-bond pricing, restructuring recovery or B_liq modeling into V_tau^co.

The architecture is:

    What is Dealer owed?
        -> V_tau^co

    What resources can Dealer reach?
        -> collateral pool / waterfall

    What are those resources worth following Original Collateral Default?
        -> B_tau^liq

    What is unrecovered?
        -> L_tau.

This separation is fundamental.

============================================================
5. REVIEW ALL OTHER SECTIONS FOR CONSISTENCY
============================================================

Do not limit the edit mechanically to Section 6.

Search the full paper, including appendices, summary/conclusion, equations, tables and implementation discussion, for statements involving:

    B_tau^liq
    B_tau^def
    B_{tau-}
    R_eff
    recovery
    haircut
    bond price P_t
    bond yield
    spread
    hazard rate / lambda
    CDS
    liquidation
    30 Reference Business Days
    CSA collateral quantity N_t^post
    available resources B_tau^avail
    LGD.

Revise any statements inconsistent with the updated architecture.

In particular check for the following known traps:

A. B_def versus B_liq
The final Dealer-loss formula should use the value actually available through the liquidation/waterfall. Do not accidentally substitute B_def for B_liq unless an explicit approximation B_def ~= B_liq is being invoked.

B. Old categorical statement that P_t simulation is required
Remove/revise any statement saying CVA inherently requires stochastic collateral bond prices. It does not under M0.

C. Old cancellation logic
Delete/demote any remaining argument that the Dealer bond-leg close-out value cancels against collateral value. The illustrative transaction has a scheduled contractual bond-side obligation; collateral issuer default impairs the resources available to satisfy that obligation, not automatically the Dealer contractual claim itself.

D. R_eff versus LGD
R_eff is already a recovery/realization assumption inside the actual limited-recourse loss. Ensure the production CVA weighting does not introduce another generic LGD factor without demonstrating that it represents a distinct economic quantity.

E. Available resources
Ensure B_tau^avail is nonnegative and is handled consistently with the waterfall and positive Dealer claim. Do not cap resources against a signed negative V_tau^co.

F. CSA posted quantity
Distinguish clearly:
- P_t may be needed to determine performing-state CSA posting quantity N_t^post;
- that does NOT automatically imply P_t is required to determine B_tau^liq;
- nor does it automatically imply P_t survives into final CVA loss if whole-pool limited-recourse access/waterfall mechanics cause the posted/unposted split to recombine.

If the paper has not rigorously established whether N_t^post completely drops out after the exact CSA/waterfall accounting, flag this as an implementation/legal-mechanics check rather than asserting the result.

G. Structural wrong-way risk
The fact that the same sovereign event both triggers the CVA loss and impairs the collateral is structural wrong-way risk. Keep this concept separate from the question of whether a bond-price process is required.

Under M1 there is an additional economically meaningful dependence:

    deteriorating lambda
        -> higher default likelihood
        -> potentially lower conditional R_liq.

Do not confuse this with ordinary correlation between independently simulated market factors.

============================================================
6. KEEP THE BASE PAPER SELF-CONTAINED
============================================================

Although detailed B_liq methodology is being split out, the reader of this CVA paper must still be able to understand and reproduce the baseline CVA calculation.

Therefore do NOT remove:
- definition of B_tau^liq;
- conceptual B_{tau-} -> B_def -> B_liq chain;
- definition of R_eff;
- baseline M0 equation;
- treatment of liquidation period;
- calibration-source / double-counting warning;
- concise explanation of M1 as a potential refinement.

What should move out are the detailed questions of:
- stochastic lambda process specification (CIR, JCIR, etc.);
- CDS curve bootstrapping details;
- conversion from lambda to survival/default probability;
- CDS-option versus historical volatility calibration;
- P versus Q dynamics;
- detailed R_liq(lambda) functional forms;
- regressions/calibration datasets;
- detailed corporate versus sovereign empirical evidence;
- bond-spread decomposition calibration;
- M0/M1/M2/M3 out-of-sample model comparison.

Those belong in the separate B_liq methodology.

============================================================
7. OUTPUT REQUEST
============================================================

Before rewriting, provide a short “change map” identifying:

1. what in current Section 6 should remain;
2. what should be rewritten;
3. what should be removed/moved to the separate B_liq paper;
4. what consequential edits are required elsewhere in the CVA paper.

Then provide the revised text.

The objective is NOT to maximize sophistication or length.

The objective is to leave the base CVA paper with a clean modular interface:

    CVA framework requires B_tau^liq
                 |
                 v
    baseline M0 supplies B_tau^liq
                 |
                 v
    separate B_liq methodology owns calibration and richer M1+ models.

Please preserve useful existing material rather than rewriting for stylistic reasons alone, and keep notation consistent throughout.
