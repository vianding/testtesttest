We need to substantially upgrade and restructure the existing working paper on
default-state sovereign collateral realisation / B_liq for the SPIRE transaction.

Please treat this as a MAJOR REWRITE based on an updated methodology view, not
as a light edit of the existing draft.

The existing draft contains useful research, notation, literature directions,
M0/M1/M2/M3 ideas, data requirements and model-development questions. Preserve
useful material, but reorganize it into a coherent quantitative methodology
working paper rather than a research-plan/note.

The revised paper should be capable of eventually serving as the dedicated
companion methodology document to the main SPIRE CVA white paper.

======================================================================
1. UPDATED PURPOSE OF THIS PAPER
======================================================================

The main CVA paper already establishes the broader chain:

    contractual mechanics
        -> Original Collateral Default tau
        -> Dealer contractual close-out claim V_tau^co
        -> resources available through SPIRE
        -> B_tau^liq / B_tau^avail
        -> Dealer loss L_tau
        -> CVA.

Do NOT re-derive the whole CVA framework here.

This companion paper owns one narrower but important problem:

    Conditional on Original Collateral Default at tau,
    how should the sovereign Original Collateral be valued for purposes
    of determining the value/proceeds realizable through the SPIRE
    liquidation process?

The primary CVA output is:

    B_tau^liq(N).

The paper should develop and justify the methodology that produces this quantity.

The updated central problem is NOT simply:

    "Should recovery be constant or depend on lambda?"

There is a more fundamental question first:

    What is the appropriate default-state recovery/value convention and
    recovery base?

In particular:

    RFV-like versus RMV-like recovery

must be addressed before M0 versus M1.

The paper should therefore establish the chain:

    B_{tau-}
        -- sovereign default -->
    B_tau^def
        -- SPIRE liquidation -->
    B_tau^liq.

These are conceptual stages. They do NOT imply that all three quantities must
be simulated.

======================================================================
2. CRITICAL DISTINCTION: PERFORMING B_t VERSUS DEFAULT-STATE B_tau^liq
======================================================================

The revised paper must explicitly distinguish two modeling problems that have
become clearer through subsequent CVA and FVA work.

A. Performing-state bond/collateral valuation:

    B_t^perf or P_t^perf,    t < tau

B. Default-state collateral realization:

    B_tau^def
        -> B_tau^liq.

They serve different purposes and should not be conflated.

The B_liq companion paper is primarily about B.

However, DO NOT state that performing B_t is irrelevant.

Our updated understanding is more nuanced.

Even if performing B_t does not mechanically enter an RFV-like formula for
B_tau^liq, B_t can be important elsewhere in the transaction because it affects
the geometry of collateralization/exposure and, in particular, whether a finite
collateral pool / Delivery Cap binds.

A poor performing-bond valuation model can therefore produce an incorrect
exposure profile by incorrectly concluding that the cap is never reached.

The correct distinction is:

    "Does B_t mechanically enter B_tau^liq?"

versus

    "Does B_t matter to the exposure/collateral state from which CVA is
     calculated?"

These are separate questions.

The paper should explain this boundary clearly and cross-reference the
performing-state / FVA / exposure methodology where appropriate.

Do NOT turn this paper into a general performing bond-pricing paper.

But include a concise interface section explaining that a performing B_t model
may still require validation through spread/rate/inflation stresses for
exposure and cap-binding analysis.

======================================================================
3. START WITH THE ECONOMIC OBJECTS
======================================================================

Define carefully:

    B_{tau-}(N)
        = performing-state market value of N units of Original Collateral
          immediately before the event;

    B_tau^def(N)
        = market value at tau of the sovereign default/restructuring claim
          or package resulting from the event;

    B_tau^liq(N)
        = value/proceeds actually realizable through the SPIRE liquidation
          process.

Where useful define corresponding per-unit quantities:

    P_{tau-},
    P_tau^def,
    P_tau^liq.

But keep B and P distinct.

B is the monetary collateral value/proceeds relevant to the CVA resource
calculation.

P is a per-unit market price/quotation.

Because the Original Collateral is inflation-linked, also define:

    R_tau^liq
        := B_tau^liq(N) / (IR_tau N)

when using an RFV-like normalization.

The paper should be very precise about whether N is base principal, indexed
principal or another contractual notional convention.

======================================================================
4. RFV VERSUS RMV MUST COME BEFORE M0 VERSUS M1
======================================================================

This is now one of the most important sections of the paper.

Compare at least:

A. RFV-like architecture

    B_tau^def or B_tau^liq
        = R_tau * IR_tau N

versus

B. RMV-like architecture

    B_tau^def
        = R_tau^RMV * B_{tau-}

or equivalently a recovery defined relative to pre-default market value.

Also discuss recovery-of-Treasury / replacement-value conventions where useful,
but do not allow the taxonomy to obscure the main economic question.

For each convention explain:

- What is the recovery base?
- What economic interpretation does it have?
- Is it primarily a live-bond pricing convention or a representation of actual
  restructuring economics?
- What data calibrate it?
- Does it require simulated B_{tau-} / P_{tau-}?
- What happens to the default jump?
- How does it interact with inflation indexation?
- Is it appropriate for measuring proceeds realizable from a sovereign
  restructuring package?
- What are its implications for CVA implementation?

Our current hypothesis is that an RFV-like architecture is the more natural
baseline for this SPIRE problem:

    B_tau^liq(N)
        = IR_tau N R_tau^liq,

because the sovereign creditor has an old contractual/index-adjusted claim and
receives a restructuring/default package against that claim.

Under this interpretation:

    IR_tau N
        -> sovereign restructuring/default claim/package
        -> market/executable value
        -> SPIRE liquidation proceeds.

By contrast, RMV mechanically defines recovery relative to B_{tau-}.

Do NOT simply assert that RFV is correct.

Provide a reasoned assessment using:

- sovereign restructuring economics;
- relevant literature;
- market practice;
- consistency with the SPIRE contractual liquidation mechanism;
- implications for calibration and implementation.

The paper should reach a recommended convention, identify alternatives, and
explain the consequences.

This choice is upstream of:

    recovery denominator
        -> calibration target
        -> need for P_{tau-}
        -> need for performing bond simulation in the recovery calculation
        -> B_tau^liq.

======================================================================
5. IMPORTANT: RFV DOES NOT MEAN PRE-DEFAULT STATE IS NECESSARILY IRRELEVANT
======================================================================

Avoid a false dichotomy.

Under an RFV-like model we can still have:

    B_tau^liq
        = IR_tau N g(lambda_{tau-}, Z).

This remains RFV-like because the recovery BASE is index-adjusted principal.

Pre-default credit information determines the recovery FRACTION, not the
denominator.

Likewise, if empirical evidence eventually supports bond-specific information:

    B_tau^liq
        = IR_tau N
          g(lambda_{tau-}, s_{tau-}^idio, Z)

is still RFV-like.

Therefore distinguish carefully:

    recovery convention/base

from

    state dependence of recovery.

This distinction should be explicit throughout the paper.

======================================================================
6. DECOMPOSE SOVEREIGN DEFAULT VALUE FROM SPIRE LIQUIDATION
======================================================================

Do not hide everything immediately inside R_eff.

Conceptually separate:

    B_tau^def
        = value of sovereign default/restructuring claim/package;

and:

    B_tau^liq
        = value/proceeds realizable through SPIRE disposal.

A useful schematic representation is:

    B_tau^liq
        = B_tau^def - C_tau^liq

or

    B_tau^liq
        = (1 - h_tau^liq) B_tau^def.

But do NOT automatically introduce h_liq as another haircut.

The paper should determine whether an incremental SPIRE liquidation adjustment
is actually necessary.

If B_tau^def is calibrated from:

- distressed traded prices;
- post-default executable prices;
- CDS auction values;
- restructuring-package market values during distress;

then some market distress/liquidity effect may already be embedded.

An additional h_liq must capture only the incremental effect of the SPIRE
liquidation mechanism not already represented.

Avoid double counting.

======================================================================
7. THE CONTRACTUAL LIQUIDATION HORIZON IS CENTRAL
======================================================================

The SPIRE documents provide a Liquidation Period and, potentially, a forced sale
irrespective of price obtainable by the contractual cut-off (approximately 30
Reference Business Days in the illustrative transaction).

Therefore the calibration target should NOT automatically be:

    ultimate sovereign recovery.

Nor should it automatically be:

    instantaneous price exactly at tau.

The paper should ask:

    What value is economically realizable over the contractual
    event-to-liquidation horizon?

Compare possible observables:

- actual disposal proceeds;
- executable distressed prices;
- restructuring-package market value;
- default-date market price;
- CDS auction price;
- post-default bond prices;
- approximately 30-business-day post-event prices;
- ultimate restructuring recovery.

Develop a hierarchy or mapping of these proxies.

Do not assume they are interchangeable.

The target should be defined before the calibration sample is assembled.

======================================================================
8. BASELINE M0
======================================================================

Once the recovery convention and target are established, define the baseline.

For an RFV-like baseline:

    M0:

    R_tau^liq = R_bar_eff

and

    B_tau^liq(N)
        = R_bar_eff IR_tau N.

Explain exactly what R_bar_eff compresses.

Potentially:

    sovereign restructuring/default recovery
        +
    market value of resulting package
        +
    any incremental SPIRE liquidation effect.

R_bar_eff is NOT automatically:

- recovery of face;
- 1 minus a published haircut;
- CDS recovery;
- CDS auction price;
- CSA Valuation Percentage;
- regulatory LGD;
- SPV recovery;
- recovery of market value.

The paper should provide a precise operational definition.

======================================================================
9. CALIBRATION MUST NOW BE A CENTRAL PART OF THE PAPER
======================================================================

The previous draft treated calibration too much like future work.

Upgrade this materially.

The revised paper should develop an actual calibration methodology:

    historical observation
        -> identify recovery concept
        -> normalize to the required recovery base
        -> adjust/match valuation horizon
        -> construct R_i^liq proxy
        -> comparable historical sample
        -> central estimate
        -> sensitivity range
        -> stress range.

The difficult part is NOT simply taking an average or median.

The difficult part is making observations economically comparable.

For each historical observation determine whether the source reports:

- face-value haircut;
- NPV haircut;
- restructuring-package value;
- recovery value;
- CDS auction price;
- defaulted-bond price;
- ultimate recovery;
- executable market value at a particular date.

Do not mix these quantities without reconciliation.

Where published papers provide only a haircut statistic, derive carefully
whether and how it can be transformed into the quantity required here.

If transformation is not defensible, do not use the observation directly.

======================================================================
10. INCLUDE AT LEAST ONE SERIOUS WORKED REAL-WORLD EXAMPLE
======================================================================

The paper should include a detailed worked historical example.

Greece 2012 is a strong candidate because of:

- the scale of the restructuring;
- multiple components of the exchange package;
- market-observable post-restructuring instruments;
- CDS auction information;
- extensive academic and institutional literature.

The worked example should demonstrate the FULL calibration logic, not merely
quote "22% recovery" or another headline number.

Show conceptually:

    old claim
        -> exchange/restructuring terms
        -> securities/package received
        -> market value of package at relevant date
        -> normalize against appropriate old indexed/principal claim
        -> R_package
        -> determine whether an additional SPIRE liquidation adjustment is
           necessary
        -> resulting proxy for R_liq.

Explicitly distinguish:

    face haircut,
    NPV haircut,
    package market value,
    recovery value,
    CDS auction value,
    liquidation realization.

Explain why they can differ.

Greece should be a worked example, NOT the entire calibration.

Also include a cross-event historical perspective so that one episode does not
drive the methodology.

======================================================================
11. LITERATURE REVIEW SHOULD SERVE THE METHODOLOGY
======================================================================

Do not create a generic literature-summary section.

For each paper/source answer:

    What does it observe?
    What recovery concept does it use?
    What denominator does it use?
    At what date is value measured?
    Is it sovereign or corporate?
    Is it P-measure empirical evidence or Q/market-implied information?
    Is it about default probability, recovery conditional on default, or both?
    Can it directly calibrate R_liq?
    If not, what limited inference can we safely draw?

At minimum seriously review and verify relevant work including:

1. Sturzenegger & Zettelmeyer
   "Haircuts: Estimating Investor Losses in Sovereign Debt Restructurings"

Use it for:
- recovery/haircut definitions;
- valuation of old versus new claims;
- heterogeneity across restructurings;
- methodological lessons for normalizing observations.

2. Cruces & Trebesch
   "Sovereign Defaults: The Price of Haircuts"

Use it for:
- broad historical restructuring sample;
- distribution and heterogeneity of sovereign losses;
- calibration ranges and stresses.

Do NOT equate their haircut measure mechanically with our R_liq.

3. Asonuma, Niepelt & Rancière
   "Sovereign Bond Prices, Haircuts and Maturity"

Use it to investigate:
- instrument-specific recovery heterogeneity;
- maturity;
- relationship between pre-restructuring market state and haircut/recovery;
- whether a constant issuer-level recovery is too crude.

4. Pan & Singleton
   "Default and Recovery Implicit in the Term Structure of Sovereign CDS Spreads"

Use it for:
- risk-neutral sovereign credit intensity;
- information contained in CDS term structures;
- default/recovery modeling under Q.

Do NOT use it as direct evidence for SPIRE liquidation proceeds.

5. Laurent et al.
   sovereign recovery-scheme / pricing work.

Use it specifically for:
- RFV/RMV/recovery-of-Treasury distinctions;
- consequences for live defaultable-bond pricing;
- consistency between lambda, recovery convention and bond valuation.

Do NOT treat pricing conventions as empirical proof of actual disposal proceeds.

6. Greece-specific academic/institutional studies.

Use these for the worked calibration example.

7. Corporate defaulted-bond literature where useful.

Use corporate evidence particularly for:
- event-window price behavior;
- default-date versus 30-day price evolution;
- liquidity and forced-sale effects.

But label it clearly as corporate evidence and discuss transferability limits.

======================================================================
12. STATE-DEPENDENT RECOVERY M1 IS A CHALLENGER, NOT THE STARTING ASSUMPTION
======================================================================

After establishing the recovery convention, target and M0 calibration, ask:

    Does pre-default sovereign credit state contain material information
    about recovery conditional on default?

Formally the question is:

    E[R_tau^liq |
      tau=t,
      lambda_{tau-},
      Z]

versus

    E[R_tau^liq | tau=t].

This is different from:

    P(default | lambda).

The CDS curve already addresses default likelihood.

M1 asks whether the state in which default occurs also predicts loss severity.

Define:

    M1:
        R_tau^liq
            = g(lambda_{tau-}, Z)

and therefore:

        B_tau^liq
            = IR_tau N
              g(lambda_{tau-}, Z).

Economic intuition may include:

- prolonged severe distress before restructuring;
- abrupt/default events from less distressed pre-event states;
- restructuring expectations;
- debt sustainability;
- market-implied severity.

But do not assume the relationship exists.

Test it.

======================================================================
13. DO NOT OVERDEVELOP STOCHASTIC LAMBDA BEFORE M1 EARNS ITS PLACE
======================================================================

The old draft spent too much main-text space on:

- CIR;
- CIR++;
- CDS-option volatility;
- stochastic intensity implementation;
- P-versus-Q dynamics.

These topics remain relevant IF M1 is adopted.

But research ordering should be:

    Does lambda_{tau-} materially predict R_tau^liq?

before:

    What is the optimal stochastic Q process for lambda_t?

If M1 does not materially improve M0, much of the additional stochastic recovery
machinery is unnecessary.

Therefore move detailed stochastic-intensity derivations to an appendix or
later implementation section unless empirical evidence establishes M1 as a
serious production candidate.

======================================================================
14. M2/M3 SHOULD BE SECONDARY, NOT THE ORGANIZING PRINCIPLE
======================================================================

The old draft gave substantial weight to:

    M2:
        R = g(s_bond)

and

    M3:
        R = g(lambda, s_idio, Z).

Demote these.

We now have a clearer separation between:

A. performing bond valuation; and
B. default-state recovery/liquidation.

The performing bond model may already require an ASW/bond-spread assumption for
FVA/ColVA/exposure analysis.

That does NOT imply the same spread should drive B_liq.

Only investigate bond-specific spread as an incremental predictor if evidence
supports:

    s_t^bond
        = F(lambda_t, ...) + s_t^idio

and:

    s_{tau-}^idio

contains material information about liquidation recovery beyond lambda.

Then a possible refinement is:

    R_tau^liq
        = g(lambda_{tau-},
            s_{tau-}^idio,
            Z).

Do not introduce another stochastic process unless it materially improves the
quantity we need.

======================================================================
15. UPDATED ROLE OF PERFORMING B_t / EXPOSURE ANALYSIS
======================================================================

Include a dedicated short section clarifying an important conclusion from the
subsequent exposure analysis.

Even under RFV:

    B_tau^liq
        = IR_tau N R_tau^liq

and therefore performing B_t may disappear mechanically from the default-state
recovery formula.

However, this does NOT imply that performing B_t can be modeled poorly without
consequence.

In the transaction, finite collateral resources / Delivery Cap mechanics can
make exposure nonlinear.

B_t may affect whether and when the cap binds.

Therefore a poor performing-bond model can create an incorrect assumption:

    "the cap is not reached"

and consequently an incorrect exposure profile.

State the correct validation question:

    Is the exposure/cap-binding conclusion materially sensitive to the
    assumed performing B_t dynamics?

This may require testing:

- fixed-ASW performing bond valuation;
- spread stresses;
- rate/inflation stresses;
- possibly richer spread dynamics.

But this is a PERFORMING-STATE exposure-model validation question.

It is not a reason to force B_{tau-} into an RFV-like B_liq formula.

This distinction should be very clear.

======================================================================
16. P VERSUS Q MUST BE HANDLED EXPLICITLY
======================================================================

Historical sovereign restructurings give primarily real-world empirical
information:

    E^P[R_liq | state].

CVA is a pricing quantity under Q.

Do not silently estimate g(lambda) from historical events and call it a
risk-neutral recovery model.

Discuss:

- historical recovery calibration;
- market-implied recovery information;
- CDS-implied default/recovery information;
- possible pricing/risk-premium overlays;
- conservative/stressed calibration where Q recovery cannot be directly
  identified.

The paper does NOT necessarily need to solve the entire P-to-Q recovery problem.

But it must state clearly what measure each calibration input represents and
what assumption bridges it into CVA pricing.

This may be more important than choosing a sophisticated functional form for
g(lambda).

======================================================================
17. MODEL VALIDATION / MATERIALITY
======================================================================

Do not evaluate M1 solely by regression fit.

The ultimate question is whether additional recovery complexity matters to CVA.

Trace:

    change in recovery model
        -> Delta R_liq
        -> Delta B_liq
        -> Delta B_avail
        -> Delta L
        -> Delta CVA.

A model can improve statistical recovery prediction but still be immaterial for
the transaction.

Conversely, modest recovery differences may matter greatly near exposure /
resource thresholds.

Validation should therefore include both:

A. empirical predictive performance;

and

B. transaction/CVA materiality.

For M0 versus M1 consider:

- out-of-sample or leave-one-event-out tests where feasible;
- RMSE / MAE;
- bias;
- tail-event errors;
- sensitivity to individual sovereign episodes;
- parameter uncertainty;
- stability by restructuring type;
- CVA impact.

Sparse sovereign data should be treated honestly.

Do not overfit.

A regime model or M0 + sensitivities may be preferable to a falsely precise
continuous regression.

======================================================================
18. CALIBRATION OUTPUT SHOULD BE OPERATIONAL
======================================================================

The revised paper should ultimately be capable of recommending something such as:

Baseline:

    R_eff = X%

Sensitivity:

    R_eff in [X1%, X2%]

Stress:

    R_eff = X_stress%

OR, if M1 is supported:

    R_tau^liq
        = g(lambda_{tau-}, Z)

with:

- calibrated parameters;
- parameter uncertainty;
- floor/cap behavior;
- extrapolation rules;
- fallback when market data are unavailable;
- stress treatment.

Do NOT invent numerical values if evidence is not yet sufficient.

Instead identify exactly what additional data/calibration work is required to
populate them.

======================================================================
19. INTERFACE TO THE MAIN CVA PAPER
======================================================================

The companion paper should ultimately expose a clean function/interface:

    B_liq_model(
        tau,
        N,
        IR_tau,
        sovereign_credit_state,
        relevant instrument characteristics
    )
        -> B_tau^liq.

For M0:

    B_tau^liq
        = R_bar_eff IR_tau N.

For M1:

    B_tau^liq
        = IR_tau N
          g(lambda_{tau-}, Z).

The main CVA paper should then consume B_tau^liq without re-deriving this
methodology.

Do not re-derive:

- V_tau^co;
- the whole SPIRE waterfall;
- generic CVA equations;
- Dealer default/DVA;
- general FVA methodology.

Only explain those items where necessary to establish the interface.

======================================================================
20. REVISED PAPER STRUCTURE
======================================================================

Please rebuild the document approximately along the following lines.

1. Executive Summary
   - problem;
   - recommended baseline;
   - key unresolved calibration questions;
   - relationship to main CVA methodology.

2. Scope and Modeling Boundary
   - what B_liq paper owns;
   - performing B_t versus default-state B_liq;
   - interface to CVA/FVA/exposure work.

3. Economic Definition of Default-State Collateral Realisation
   - B_{tau-};
   - B_tau^def;
   - B_tau^liq;
   - P versus B;
   - inflation-linked normalization.

4. Choice of Recovery Convention
   - RFV;
   - RMV;
   - other conventions if relevant;
   - economic interpretation;
   - recommendation for SPIRE.

5. From Sovereign Default Claim to SPIRE Liquidation Proceeds
   - restructuring package;
   - package market value;
   - contractual liquidation horizon;
   - forced-sale effect;
   - avoiding double-counting liquidity.

6. Baseline Model M0
   - definition;
   - R_eff;
   - assumptions;
   - strengths and limitations.

7. Calibration Methodology
   - recovery-concept reconciliation;
   - historical dataset;
   - normalization;
   - timing;
   - central/sensitivity/stress calibration.

8. Worked Historical Example: Greece 2012
   - old claim;
   - restructuring terms;
   - package;
   - market value;
   - different haircut/recovery concepts;
   - mapping to R_liq;
   - limitations.

9. Cross-Event Sovereign Evidence
   - historical distribution;
   - heterogeneity;
   - maturity/instrument effects;
   - lessons for M0.

10. Does Pre-Default Credit State Predict Recovery?
    - empirical question;
    - lambda;
    - M1;
    - proposed tests;
    - evidence/results if available.

11. Optional Bond-Specific Refinement
    - only if supported;
    - lambda versus s_bond versus s_idio;
    - M2/M3 as secondary challengers.

12. Measure and Pricing Considerations
    - P versus Q;
    - CDS-implied information;
    - implications for CVA calibration.

13. Performing B_t and Exposure/Cap-Binding Interface
    - why B_t may matter even if RFV recovery does not use it;
    - fixed-ASW approximation;
    - sensitivity/stress validation;
    - boundary with FVA/exposure methodology.

14. Model Validation and CVA Materiality
    - empirical tests;
    - parameter uncertainty;
    - impact on B_avail / L / CVA.

15. Recommended Methodology and Implementation
    - M0 unless richer model earns its place;
    - clean B_liq interface;
    - implementation requirements.

16. Limitations and Open Items

Appendices:
A. Recovery-convention derivations
B. CDS/intensity mathematics
C. Detailed literature/evidence table
D. Historical calibration dataset
E. Greece calculations
F. M0/M1/M2/M3 statistical tests
G. Performing B_t sensitivity analysis, if useful

======================================================================
21. WRITING STYLE — IMPORTANT
======================================================================

The current draft reads too much like a research note / project plan.

The revised version should read like a quantitative methodology working paper.

Reduce:

- long lists of questions;
- repeated [OPEN QUESTION] labels;
- research-task instructions;
- acceptance-gate language in the main text;
- excessive M0/M1/M2/M3 bookkeeping;
- generic literature summaries;
- premature stochastic-process detail.

Increase:

- coherent prose;
- economic reasoning;
- derivation;
- evidence;
- explicit conclusions;
- clear distinction between established result and unresolved issue;
- worked calculations/examples;
- calibration logic.

Use labels such as [MODEL CHOICE], [EMPIRICAL EVIDENCE], [OPEN ITEM] only where
they genuinely aid governance. Do not let them dominate the document.

Every major section should answer a question and reach a conclusion.

For example:

    Question:
        Should B_tau^liq be defined using RFV or RMV?

    Analysis:
        economics + literature + implementation consequences.

    Conclusion:
        preferred architecture and why.

That will make the paper read as methodology rather than notes.

======================================================================
22. WHAT NOT TO DO
======================================================================

Do NOT:

1. simply polish the existing draft;
2. make the paper longer by adding all recent discussion;
3. assume M1 is the final answer;
4. assume RMV merely because P_{tau-} contains information;
5. assume RFV means pre-default credit state is irrelevant;
6. force performing B_t into B_liq because B_t matters for exposure analysis;
7. claim performing B_t is irrelevant because it disappears from the RFV
   recovery formula;
8. mix ultimate recovery, NPV haircut, CDS auction recovery and liquidation
   proceeds;
9. add an arbitrary 30-day liquidity haircut on top of prices that already
   embed distress/liquidity;
10. build CIR/CIR++ before demonstrating that state-dependent recovery matters;
11. use corporate evidence as though it were sovereign evidence;
12. silently mix P and Q;
13. overfit a sparse sovereign default sample;
14. reproduce the main CVA paper;
15. invent a calibration number unsupported by evidence.

======================================================================
23. DELIVERABLE FOR THIS ROUND
======================================================================

Please do TWO things.

PART A — METHODOLOGY UPGRADE MAP

Before rewriting the paper, provide a concise but substantive upgrade map
showing:

1. which conclusions from the existing B_liq draft remain valid;
2. which conclusions need modification;
3. which sections should be deleted/demoted/moved to appendix;
4. what new sections are required;
5. how the updated CVA and FVA/exposure understanding changes the B_liq paper;
6. what empirical/calibration work is still required;
7. what you currently regard as the leading baseline methodology and why.

In particular, explicitly state your current view on:

    RFV versus RMV;
    M0 versus M1;
    role of B_{tau-};
    role of lambda_{tau-};
    role of bond spread;
    30-business-day liquidation;
    P versus Q;
    performing B_t and cap-binding/exposure analysis.

PART B — REVISED WORKING PAPER

Then produce the substantially rewritten paper.

Where evidence is available, reach a conclusion.

Where evidence is insufficient, state precisely what remains unresolved and what
test/data would resolve it.

The objective is NOT to make the document look sophisticated.

The objective is to arrive at the simplest defensible methodology for:

    B_tau^liq

that is:

- economically coherent;
- consistent with the SPIRE mechanics;
- empirically calibratable;
- auditable;
- implementable;
- and sufficiently accurate for the resulting CVA.

The key discipline remains:

    derive first;
    identify what matters;
    calibrate to the quantity actually required;
    add stochastic complexity only when it earns its place.
