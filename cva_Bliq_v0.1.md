Please develop a standalone quantitative working paper on the modeling of default-state sovereign collateral realization for the SPIRE CVA problem.

Working title:

    Default-State Sovereign Collateral Realisation:
    Modelling B_tau^liq for SPIRE CVA

This is a companion methodology paper to the main SPIRE Sovereign Bond Collateral CVA white paper.

The main CVA paper establishes the overall architecture:

    contractual mechanics
        -> loss-relevant event tau
        -> Dealer close-out claim V_tau^co
        -> collateral/resources accessible through the waterfall
        -> B_tau^liq / B_tau^avail
        -> Dealer loss L_tau
        -> CVA.

This new paper should NOT repeat that entire framework.

Its job is to investigate one quantitative object deeply:

    Given Original Collateral Default at tau,
    what value/proceeds can actually be realised from the sovereign
    collateral through the SPIRE liquidation process?

The quantity consumed by the CVA framework is B_tau^liq.

However, the underlying modeling object should generally be the normalized
liquidation realization/recovery:

    R_tau^liq
        := B_tau^liq(N) / (IR_tau N),

so that

    B_tau^liq(N)
        = IR_tau N R_tau^liq.

The central research question is therefore:

    What determines R_tau^liq, and can a state-dependent model improve
    materially on a constant effective realization assumption?

Do NOT begin from the assumption that a sophisticated stochastic model is
required. The paper should determine whether additional complexity earns
its place.

============================================================
1. RESEARCH PHILOSOPHY
============================================================

Use the following discipline throughout:

    derive the quantity required by CVA first;
    identify which state variables actually survive into that quantity;
    introduce stochastic factors only when they have an identified
    economic and empirical role.

Do NOT simulate bond price merely because the collateral is a bond.

Do NOT start from a preferred stochastic model and search for a use for it.

Do NOT treat historical correlation as causation.

Distinguish carefully:

    driver
    observable indicator / state variable
    calibration variable
    derived market quantity
    model output.

In particular, bond price/yield/spread may contain information about
sovereign distress without being the primitive cause of default.

The model should be as simple as possible, but not simpler than supported
by the economics and evidence.

============================================================
2. START BY DEFINING THE TARGET PRECISELY
============================================================

The paper must first distinguish:

    B_{tau-}(N)
        = performing-state market value immediately before the event;

    B_tau^def(N)
        = market value of the resulting sovereign default claim or
          restructuring package;

    B_tau^liq(N)
        = value/proceeds ultimately realizable through the SPIRE
          liquidation process.

Conceptually:

    B_{tau-}
        -> sovereign credit/default event
        -> B_tau^def
        -> SPIRE Liquidation Period
        -> B_tau^liq.

These are conceptual stages, NOT three quantities that must independently
be simulated.

The CVA framework ultimately requires B_tau^liq.

Also define carefully:

    P_{tau-}
    P_tau^def
    P_tau^liq

when useful as per-unit market observations.

Do not casually identify P_tau^liq with B_tau^liq.

B is the monetary collateral value/proceeds consumed by the CVA loss
calculation. P is a per-unit market-price observation.

Because the collateral is inflation linked, keep the normalization
explicit:

    R_tau^liq
        = B_tau^liq(N)/(IR_tau N).

Explain why R_tau^liq is the natural quantity for recovery modeling while
B_tau^liq is the quantity ultimately supplied to CVA.

============================================================
3. DEFINE WHAT “LIQUIDATION RECOVERY” ACTUALLY MEANS
============================================================

This is critical.

R_tau^liq is NOT automatically:

- ultimate sovereign recovery;
- recovery of face;
- recovery of market value;
- CDS contractual recovery;
- CDS auction price;
- restructuring haircut;
- regulatory LGD;
- CSA Valuation Percentage;
- SPV recovery;
- default-date traded price.

The paper should determine how each observable relates to the actual
target.

SPIRE can liquidate collateral during a contractual Liquidation Period,
with a forced-sale mechanism / cut-off approximately 30 Reference Business
Days after the relevant event under the applicable documents.

Therefore distinguish at least:

    event/default-state value,
    restructuring-package value,
    distressed market value,
    liquidation-window realizable value,
    ultimate restructuring recovery.

Ask explicitly which historical observable is the best empirical proxy for

    R_tau^liq.

A key candidate is a market value realizable over an event-to-approximately-
30-business-day window rather than ultimate sovereign recovery years later.

If actual SPIRE liquidation mechanics differ from historical market
observations, identify the required adjustment.

============================================================
4. MODEL M0 — CONSTANT EFFECTIVE REALIZATION
============================================================

The baseline model is:

    M0:
        R_tau^liq = R_bar_eff

and therefore

        B_tau^liq(N)
            = R_bar_eff IR_tau N.

Explain the economic interpretation.

R_bar_eff is an all-in effective realization per unit of index-adjusted old
principal intended to approximate the value/proceeds ultimately realizable
through SPIRE liquidation.

Discuss how it can be calibrated from:

- restructuring-package market values;
- distressed sovereign bond prices;
- post-default traded prices;
- CDS auction values where relevant;
- historical sovereign restructuring studies;
- case studies such as Greece.

But be very precise about what each source measures.

Do not combine fundamentally different recovery concepts without
normalization.

Discuss appropriate central calibration and sensitivity/stress ranges.

M0 should remain a credible production baseline unless a richer model
demonstrably improves the representation of liquidation recovery.

============================================================
5. EMPIRICAL QUESTION 1:
DO BOND MARKETS ANTICIPATE DEFAULT?
============================================================

Review the empirical literature separately for:

A. sovereign bonds;
B. corporate / non-sovereign bonds.

Do NOT mix the evidence between these populations.

Investigate whether default/restructuring events are generally preceded by:

    falling bond prices,
    rising yields,
    widening benchmark spreads,
    widening CDS spreads / hazard rates.

The purpose is NOT merely to establish that markets anticipate default.
That fact alone does not justify a state-dependent recovery model.

The stronger question is:

    Does the pre-default market state contain information about
    post-default recovery / liquidation value?

Review evidence around event windows such as:

    tau - 90d
    tau - 60d
    tau - 30d
    tau -
    tau
    tau + 30d

where data are available.

For corporate bonds, examine empirical work using traded defaulted-bond
prices around default and subsequent recovery.

For sovereigns, examine instrument-level restructuring datasets and
historical market-price datasets.

Clearly state differences in sample size, market structure, recovery
mechanism and applicability to SPIRE.

============================================================
6. EMPIRICAL QUESTION 2:
DOES PRE-DEFAULT CREDIT STATE PREDICT RECOVERY CONDITIONAL ON DEFAULT?
============================================================

This is the key empirical question of the paper.

Do not confuse:

    P(default | credit state)

with:

    E[R_liq | default, pre-default credit state].

The CDS curve already addresses the first.

For state-dependent recovery to add value, we need evidence for the second.

Investigate:

    E[R_tau^liq |
      tau=t,
      lambda_{tau-},
      bond spread_{tau-},
      instrument characteristics].

Review evidence linking:

- pre-default prices to subsequent recovery;
- pre-default spreads to recovery;
- pre-default CDS/default intensity to recovery;
- maturity to sovereign restructuring haircut;
- liquidity/distressed trading conditions to realized recovery;
- macro/default-cycle state to recovery.

Distinguish statistical association from a stable predictive relationship.

Look for out-of-sample evidence where available.

============================================================
7. RECONCILE BOND YIELD/SPREAD WITH THE CREDIT INTENSITY PROCESS
============================================================

This section is important.

Do NOT independently simulate sovereign credit intensity lambda_t and
outright bond yield without explaining why both are needed.

Start from:

    y_t^bond
        = y_t^benchmark
        + s_t^bond,

and decompose:

    s_t^bond
        = s_t^credit
        + s_t^idio.

The sovereign-credit component should be linked to the intensity model:

    s_t^credit
        = F(lambda_t, recovery assumptions, term structure, ...).

Therefore:

    s_t^bond
        = F(lambda_t, ...) + s_t^idio.

Interpret:

    lambda_t
        = issuer-level sovereign credit/default state;

    s_t^idio
        = residual bond-specific component potentially reflecting
          liquidity, technicals, instrument-specific distress,
          restructuring treatment, etc.

Discuss benchmark choice carefully.

If the benchmark bond is another bond of the SAME sovereign, much of the
issuer-level sovereign credit component may cancel. That may be useful for
some relative-value questions but is undesirable if the purpose is to
measure the sovereign credit state.

For this problem, an appropriate non-credit benchmark may be preferable,
for example a swap/OIS/reference-rate or real-rate curve depending on the
instrument/currency.

For EUR instruments, consider appropriately whether €STR/OIS, swap or
another reference curve is conceptually suitable.

Do not mechanically prescribe a benchmark without considering the
inflation-linked nature of the collateral.

============================================================
8. FROM CDS CURVE TO LAMBDA AND DEFAULT PROBABILITY
============================================================

Provide a first-principles derivation.

Explain that lambda_t is an instantaneous conditional default intensity.

For deterministic lambda:

    S(0,t)
        = exp[- integral_0^t lambda(u) du],

    PD(0,t)
        = 1 - S(0,t),

and bucket default probability is

    q_i
        = S(0,t_{i-1}) - S(0,t_i).

Explain how lambda is calibrated/bootstrapped from sovereign CDS by
equating CDS premium and protection legs under the chosen recovery
convention.

The approximation

    CDS spread ~= lambda (1-R)

may be used for intuition only.

Production calibration should use the full CDS pricing equations.

Then extend to stochastic intensity:

    S(0,t)
        = E^Q[
            exp(- integral_0^t lambda_s ds)
          ].

Explain pathwise default-time generation through cumulative hazard:

    tau
      = inf {
          t :
          integral_0^t lambda_s ds >= E
        },

where E is an independent unit exponential random variable.

This should make explicit how the simulated lambda state generates default
timing.

============================================================
9. STOCHASTIC DYNAMICS AND CREDIT VOLATILITY
============================================================

Once lambda is stochastic, distinguish:

A. calibration of today's CDS term structure;
B. calibration of future lambda volatility/dynamics.

Consider parsimonious positive intensity processes such as CIR or suitable
alternatives:

    d lambda_t
      = kappa(theta - lambda_t) dt
        + sigma_lambda sqrt(lambda_t) dW_t

as an illustrative starting point.

Do not choose CIR automatically. Discuss requirements:

- positivity;
- ability to fit today's survival curve;
- realistic spread dynamics;
- tractability;
- compatibility with the existing CVA engine;
- ability to calibrate volatility;
- behavior in severe sovereign distress.

Discuss deterministic-shift / CIR++ style constructions if useful for
simultaneously fitting today's term structure and stochastic dynamics.

Critically examine volatility calibration.

Potential hierarchy:

1. CDS-option / CDS-swaption implied volatility where liquid and available;
2. historical sovereign CDS spread/intensity dynamics;
3. proxy or stressed volatility where direct option markets are inadequate.

Explain:

    rates -> swaption vol,
    FX -> FX option vol,
    credit intensity -> conceptually CDS-option vol.

But explicitly investigate whether sovereign CDS-option markets provide
sufficient liquidity/data for the relevant issuer.

Distinguish P-measure historical dynamics from Q-measure pricing dynamics.

Do not silently use historical volatility as Q volatility.

Explain what assumptions/market-price-of-risk treatment are required if
historical dynamics are used.

============================================================
10. MODEL M1 — STATE-DEPENDENT RECOVERY
============================================================

The principal challenger to M0 is:

    M1:
        R_tau^liq
            = g(lambda_{tau-}, Z),

or initially:

        R_tau^liq
            = g(lambda_{tau-}).

Economic interpretation:

lambda_t determines default arrival but lambda_{tau-} also describes HOW
distressed the sovereign was immediately before default.

This may distinguish, for example:

- a prolonged, heavily anticipated restructuring;
- an abrupt/default event following a comparatively moderate pre-event
  market state.

The model should investigate whether this distinction predicts
liquidation severity.

The resulting architecture is:

    lambda_t
       -> survival/default probability
       -> tau

and simultaneously

    lambda_{tau-}
       -> conditional R_tau^liq
       -> B_tau^liq.

Then:

    B_tau^liq
       = IR_tau N
         g(lambda_{tau-}, Z).

This is the core M1 proposition.

============================================================
11. HOW TO CONSTRUCT g(lambda)
============================================================

Do not jump immediately to an arbitrary functional form.

Start from historical default/restructuring observations i.

For each event, ideally construct:

    R_i^liq

together with pre-event states such as:

    lambda_{i,-90},
    lambda_{i,-60},
    lambda_{i,-30},
    lambda_{i,tau-}.

Where CDS data are unavailable, investigate whether bond-spread or other
market-credit proxies can extend the sample.

A simple empirical specification might begin with:

    R_i^liq
       = alpha
         + beta lambda_{i,tau-}
         + gamma' Z_i
         + epsilon_i,

but bounded models may be preferable, e.g.

    logit(R_i^liq)
       = alpha
         + beta lambda_{i,tau-}
         + gamma' Z_i
         + epsilon_i.

Discuss:

- monotonicity;
- bounds;
- sparse sovereign-default samples;
- parameter uncertainty;
- cross-country heterogeneity;
- maturity effects;
- restructuring type;
- inflation-linked versus nominal instruments;
- liquidity;
- possible nonlinearity;
- extreme lambda behavior;
- robustness/stress treatment.

Do NOT overfit a small sovereign dataset.

If evidence only supports a coarse regime model, consider something such as

    normal / distressed / severe-distress

recovery regimes rather than a falsely precise continuous regression.

============================================================
12. MODEL M2 — BOND-SPREAD-STATE RECOVERY
============================================================

Investigate the challenger:

    M2:
        R_tau^liq
            = g(s_{tau-}^bond, Z).

This tests whether the actual bond market state predicts liquidation
recovery better than issuer-level lambda.

Explain why bond spread may contain information beyond lambda:

    liquidity,
    bond-specific technicals,
    maturity,
    instrument treatment,
    restructuring expectations.

But also explain why raw bond yield/price can be contaminated by:

    rates,
    inflation expectations,
    coupon/maturity effects,
    liquidity,
    risk premium.

Therefore benchmark-adjusted spread is conceptually preferable to raw
price/yield when testing incremental credit/liquidity information.

============================================================
13. MODEL M3 — CREDIT + BOND-SPECIFIC STATE
============================================================

If evidence supports it, consider:

    M3:
        R_tau^liq
          = g(
              lambda_{tau-},
              s_{tau-}^idio,
              Z
            ),

where

    s_t^idio
       = s_t^bond - F(lambda_t,...).

This has an attractive interpretation:

    lambda
        -> issuer-level default/recovery state;

    s_idio
        -> this particular bond's incremental
           liquidity/technical/distress state.

This may be especially relevant because SPIRE must liquidate a PARTICULAR
bond, not an abstract sovereign claim.

However, M3 should only be adopted if s_idio has material incremental
predictive power.

============================================================
14. MODEL SELECTION — MAKE THE COMPLEXITY EARN ITS PLACE
============================================================

The paper should explicitly frame the models as a horse race:

    M0:
        R = constant

    M1:
        R = g(lambda)

    M2:
        R = g(s_bond)

    M3:
        R = g(lambda, s_idio, Z).

Compare them using appropriate empirical tests.

Do not select the most complicated model by construction.

Consider:

- out-of-sample RMSE / MAE;
- bias;
- tail/default-state errors;
- stability across sovereign episodes;
- sensitivity to individual restructurings;
- parameter uncertainty;
- interpretability;
- calibration availability;
- implementation complexity.

For our application, place particular weight on prediction error in:

    distressed/default-state liquidation value,

rather than generic statistical fit.

If M1 does not materially outperform M0, retain M0.

If M2 outperforms M1, investigate what information bond spread contains
beyond lambda.

If M3 materially outperforms both and remains robust, then a separate
bond-specific spread process may earn its place in the CVA simulation.

============================================================
15. LIQUIDATION PERIOD AND FORCED-SALE EFFECT
============================================================

Treat separately:

    B_tau^def

and

    B_tau^liq.

A richer representation may be:

    B_tau^liq
       = (1 - h_tau^liq) B_tau^def.

But h_tau^liq must represent an INCREMENTAL SPIRE-specific liquidation
effect.

If the calibration source is already:

- a distressed traded price;
- a CDS auction value;
- a post-default market price;
- a restructuring-package market value observed during distress;

then some or all liquidity/distress may already be embedded.

Do not double-count it.

Use corporate defaulted-bond literature as potentially useful evidence
about price behavior around default and over approximately 30-day trading
windows, but clearly label the population as corporate and explain limits
to transferability to sovereign collateral.

Use sovereign evidence wherever available for the actual target.

============================================================
16. WRONG-WAY RISK / DEPENDENCE
============================================================

Explain the dependence created by M1.

Under M0:

    default probability depends on lambda,
    recovery is constant.

Under M1:

    lambda deterioration
        -> higher conditional default likelihood

and simultaneously potentially

    lambda deterioration
        -> lower R_liq.

Thus default probability and loss severity become state dependent through
a common sovereign credit state.

Explain why this is economically meaningful wrong-way dependence.

Do not describe it merely as an arbitrary correlation between independent
default and recovery processes.

If the empirical sign or strength of g(lambda) is not robust, say so.

============================================================
17. RELATION TO LAURENT / RECOVERY CONVENTIONS
============================================================

Use Laurent et al. carefully.

Explain RMV, RFV and recovery-of-Treasury/replacement-type conventions and
their implications for pre-default bond pricing.

But do NOT treat Laurent as evidence for actual SPIRE liquidation proceeds.

The important distinction is:

    pricing convention for a live defaultable bond

versus

    empirical realization from an actual sovereign restructuring
    followed by SPIRE liquidation.

Laurent helps ensure internal consistency between:

    lambda,
    recovery assumptions,
    pre-default bond valuation.

It does not tell us what the Disposal Agent will realize.

============================================================
18. LITERATURE REVIEW
============================================================

Conduct a serious literature review using primary papers and high-quality
institutional sources wherever possible.

At minimum investigate relevant work from:

- Laurent et al. on sovereign recovery conventions;
- Pan & Singleton on default and recovery information in sovereign CDS;
- Asonuma / Niepelt / Rancière on sovereign bond prices, haircuts and
  maturity;
- Asonuma / Trebesch where relevant to preemptive versus post-default
  restructuring;
- Sturzenegger & Zettelmeyer;
- Cruces & Trebesch;
- Meyer / Reinhart / Trebesch historical sovereign bond-price datasets;
- empirical studies of defaulted corporate bond prices and recovery,
  including event-window trading behavior;
- CDS/default-intensity stochastic modeling literature;
- CDS-option / credit-spread-option volatility calibration literature.

Do not merely summarize papers.

For every source ask:

    What quantity does this paper actually observe?
    What population/time period does it cover?
    Is it P or Q information?
    Does it concern probability of default, recovery conditional on
    default, or both?
    Does it measure ultimate recovery or market realization near default?
    Is it directly usable for SPIRE B_liq?
    If not, what limited inference can we safely take from it?

Keep sovereign and corporate evidence clearly separated.

============================================================
19. DATA REQUIREMENTS
============================================================

Produce a concrete data-requirements section.

For an ideal empirical calibration dataset, identify fields such as:

- issuer;
- bond identifier;
- currency;
- nominal vs inflation-linked;
- coupon;
- maturity;
- seniority;
- restructuring terms;
- default/restructuring dates;
- daily/weekly pre-default bond prices;
- benchmark yields;
- bond spreads;
- CDS term structure;
- CDS-option vol where available;
- event/default-date prices;
- CDS auction values;
- post-default prices;
- restructuring-package composition;
- package market value;
- 30-day post-event prices;
- liquidity/trading-volume measures;
- macro state.

Identify which variables are essential for M0, M1, M2 and M3.

Discuss likely data limitations honestly.

============================================================
20. IMPLEMENTATION MAPPING
============================================================

End by showing how the selected recovery model plugs into the existing CVA
engine.

For M0:

    tau
       -> R_bar_eff
       -> B_tau^liq
       -> B_tau^avail
       -> L_tau.

For M1:

    simulate lambda_t
       -> generate / condition on default tau
       -> observe lambda_{tau-}
       -> R_tau^liq = g(lambda_{tau-},Z)
       -> B_tau^liq = IR_tau N R_tau^liq
       -> B_tau^avail
       -> L_tau
       -> CVA.

If lambda is already part of the CVA credit simulation, emphasize the
potential implementation advantage: M1 may reuse the existing sovereign
credit state rather than adding an independent bond-yield process.

Only add s_idio simulation if M2/M3 evidence demonstrates incremental value.

============================================================
21. IMPORTANT MODELING / WRITING DISCIPLINE
============================================================

Throughout the paper:

- distinguish contractual facts from modeling choices;
- distinguish observed data from assumptions;
- distinguish P-measure evidence from Q-measure calibration;
- distinguish default prediction from recovery prediction;
- distinguish issuer-level credit state from bond-specific liquidity state;
- distinguish ultimate sovereign recovery from SPIRE liquidation
  realization;
- distinguish price per unit P from monetary collateral value B;
- distinguish B_def from B_liq;
- avoid false precision where sovereign samples are sparse;
- do not introduce variables without identifying where they enter
  B_tau^liq or CVA;
- do not assume a richer model is better;
- do not double-count recovery or liquidity effects;
- explicitly flag unresolved empirical questions.

The tone should be quantitative/model-methodology quality: rigorous,
neutral, auditable and suitable for review by experienced quants/model
risk stakeholders.

============================================================
22. PROPOSED PAPER STRUCTURE
============================================================

Use this as a starting structure, but improve it if the research suggests
a cleaner organization:

1. Executive Summary and Modeling Decision
2. Scope and Interface with the SPIRE CVA Framework
3. Definition of Default-State Collateral Realisation
4. Contractual Liquidation Horizon and Economic Target
5. Baseline M0: Constant Effective Realisation
6. What Historical Defaults Tell Us
   6.1 Sovereign Evidence
   6.2 Corporate Evidence
   6.3 What Can and Cannot Be Transferred to SPIRE
7. From Sovereign Credit State to Default
   7.1 CDS Curve and Hazard/Intensity
   7.2 Survival and Default Probability
   7.3 Stochastic Intensity Dynamics
   7.4 Credit Volatility Calibration
8. Linking Bond Spread and Sovereign Intensity
9. M1: State-Dependent Recovery R_liq(lambda)
10. M2/M3: Does Bond-Specific Spread Add Information?
11. Calibration and Empirical Testing Strategy
12. Liquidation-Period / Forced-Sale Adjustment
13. Model Comparison and Validation
14. CVA Engine Implementation
15. Limitations, Sensitivities and Open Questions
16. Conclusions and Recommended Next Steps

Appendices should contain detailed derivations, literature tables,
calibration equations, dataset definitions and implementation details
that would otherwise interrupt the main argument.

============================================================
23. FIRST DELIVERABLE
============================================================

Do NOT immediately produce a polished final paper.

First produce a research/design draft containing:

A. proposed final structure;
B. central modeling thesis;
C. mathematical architecture;
D. literature/evidence map;
E. what is already established versus still unproven;
F. proposed empirical tests for M0/M1/M2/M3;
G. required data;
H. unresolved questions that could materially change the methodology;
I. recommended scope for Version 1 of the paper.

For every major modeling conclusion, classify it as:

    [CONTRACTUAL FACT]
    [EMPIRICAL EVIDENCE]
    [MODEL CHOICE]
    [PROPOSED REFINEMENT]
    [OPEN QUESTION]

where appropriate.

Most importantly:

Do not write the paper toward a predetermined conclusion that
R_liq(lambda) must be used.

The paper should test whether

    M1: R_liq = g(lambda_{tau-})

provides enough economic and empirical improvement over

    M0: R_liq = constant

to justify its additional calibration and simulation complexity.

The final methodology should follow the evidence.
