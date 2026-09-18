MAJOR STRUCTURAL REVISION — SPIRE SOVEREIGN-BOND-COLLATERAL CVA METHODOLOGY

Read the ENTIRE latest white-paper draft, including all appendices, before editing. Also review the supplied summary, but treat it only as a structural aid: some conclusions in the summary are dated, especially the former trade-specific cancellation / CVA=0 argument.

This is NOT an instruction to append another recovery section. It is a structural revision and consolidation of the existing paper based on where the analysis has now converged.

Preserve strong contractual analysis, equations, citations, empirical evidence and useful distinctions. But move, merge, shorten, demote or delete material where necessary. Do not preserve exploratory complexity merely because it appeared in an earlier draft.

Before rewriting, first provide a concise CHANGE MAP:
1. sections substantially retained;
2. sections restructured;
3. material moved;
4. material consolidated/deleted;
5. equations/conclusions requiring substantive correction.

Then produce the revised paper.


============================================================
I. WHERE THE ANALYSIS HAS NOW CONVERGED
============================================================

The methodology should now read top-down as:

General CVA
    ↓
loss-relevant contractual event τ
    ↓
two quantities at the event:
    (A) Dealer contractual close-out claim Vτ^co
    (B) resources contractually available to meet it Bτ^avail
    ↓
event loss Lτ
    ↓
CVA
    ↓
production implementation.

The primitive CVA statement remains:

CVA_0
= E^Q[D(0,τ)Lτ 1_{τ≤T}]
= ∫ DF(0,t) E^Q[L_t | τ=t] (-dS(t)).

The central event-loss object is:

Lτ^Dealer = [Vτ^co - Bτ^avail]^+,

subject to exact ETA/CSA/waterfall accounting.

The entire methodology therefore ultimately answers:

1. What is the Dealer contractually owed at τ?
2. What resources are contractually available to satisfy that claim?

The work has now substantially settled the contractual architecture. The principal remaining QUANTITATIVE MODELING problem is:

    conditional on Original Collateral Default at τ,
    what is the value actually realised from the sovereign
    Original Collateral through the SPIRE liquidation process?

That quantity is Bτ^liq.


============================================================
II. IMPORTANT SETTLED CONCLUSIONS TO REFLECT
============================================================

Treat the following as the current adopted interpretation unless an unresolved documentary item genuinely contradicts it.

1. LOSS-RELEVANT EVENT

Original Collateral Default is the relevant contractual event for the illustrative trade.

It triggers the contractual chain:

Original Collateral Default
    → Early Redemption Trigger Date
    → deemed Swap Early Termination Date
    → Liquidation Event
    → liquidation of Collateral
    → Condition 15 waterfall
    → residual Dealer loss, if any.

This is contractual, not a modeling assumption.

The modeled event probability is proposed to be generated from a hazard process calibrated to the sovereign collateral issuer's market credit curve/CDS spreads.

Retain the caveat that the SPIRE Original Collateral Default trigger set is not necessarily identical to one particular CDS Credit Event definition. That is event-definition basis risk, not a reason to use SPV's own generic credit curve or Dealer credit.


2. THE DEALER'S CLAIM IS AGAINST SPIRE / THE ISSUER

The sovereign bond plays several roles in the structure, but those roles must remain distinct.

For the illustrative trade, the swap bond-side payments are contractual obligations of the Issuer/SPV to the Dealer, referenced to the scheduled coupons and scheduled redemption amounts of the Original Collateral.

The latest documentary reading is the SCHEDULED-OBLIGATION reading, not the pass-through reading.

In particular, Appendix 1 defines amounts by reference to:

- aggregate scheduled interest amounts due on the Original Collateral; and
- aggregate scheduled redemption amounts due on the Original Collateral,

rather than merely amounts actually received by SPIRE from the sovereign.

Therefore:

    sovereign default impairs the collateral supporting the Dealer's claim,
    but does not itself rewrite the Issuer's scheduled swap obligation.

This is the crucial reason that the Dealer's contractual claim and the distressed collateral realization do NOT mechanically cancel.


3. Vτ^co IS NOT THE NEW SOVEREIGN-RECOVERY MODELING PROBLEM

Vτ^co should be obtained using the existing REGULAR SWAP CALCULATOR / pricing library.

Conceptually:

Vτ^co
= PVτ^co(scheduled bond-side cashflows)
  - PVτ^co(remaining Note-side cashflows),

using the contractual close-out methodology.

Do NOT introduce:

- R_eff;
- Bτ^liq;
- sovereign liquidation recovery;
- a special defaulted-bond valuation curve;
- or a bespoke sovereign recovery model

inside Vτ^co merely because the Original Collateral has defaulted.

The swap claim and the collateral realization are separate objects.

Do not resurrect the earlier assumption:

    Bτ^co = Bτ^liq

simply because the same sovereign bond economically appears on both sides.

The Issuer's contractual scheduled payment obligation and the impaired collateral asset are not the same claim.


4. WHOLE-POOL ACCESS

The Dealer is not economically restricted to the collateral quantity Nτ^post that happened to have been posted under the CSA before default.

Through Condition 15 and the Mortgaged Property / Available Proceeds waterfall, the Dealer has priority recourse ahead of Noteholders to the relevant Series resources, subject to senior-ranking items.

This is PRIORITY RECOURSE, not ownership of the collateral pool.

The important structural conclusion is approximately:

Nτ^avail ≈ N^pool

in the sense relevant to determining how much Original Collateral can ultimately support the Dealer's claim, subject to the waterfall and senior deductions.

Nτ^post remains relevant to performing-state collateralisation and to the contractual split of recovery routes, but it is not itself the upper bound on post-default recovery.


5. CSA ANTI-DOUBLE-COUNTING

Posted CSA collateral is credited inside the contractual termination mechanics.

Condition 15 then separates the Dealer's extraction through the relevant tiers and caps the residual Swap Counterparty claim appropriately.

Do NOT subtract posted collateral a second time outside that structure.

Preserve and clarify the anti-double-counting argument.


6. LIMITED RECOURSE / RESIDUAL SEVERITY

Condition 17 extinguishes any residual unpaid claim after the Mortgaged Property is exhausted and Available Proceeds have been applied.

This can be described as 100% severity on the residual claim that remains AFTER collateral realization and waterfall.

However, if Lτ is already defined as the actual unrecovered post-waterfall dollar loss, do NOT multiply Lτ by another generic LGD.

Reconcile this explicitly with production implementation if the production default weight already contains a factor such as (1-R).

Do not double-count recovery/severity.


============================================================
III. REORGANIZE THE PAPER AROUND THE FOLLOWING STRUCTURE
============================================================

The preferred main structure is approximately:

1. General CVA and Dealer-Loss Framework
2. The Loss-Relevant Event τ
3. Performing-State Economics and Collateralisation
4. Dealer Close-Out Claim Vτ^co
5. Available Resources: What Collateral Can the Dealer Reach?
6. Modeling Default-State Collateral Realisation Bτ^liq
7. General Event Loss and CVA
8. Production Implementation, Sensitivities, Refinements and Open Items
Appendices: contractual evidence, literature, calibration evidence, detailed implementation material.

Do not follow this numbering mechanically if a better local organization emerges, but preserve the conceptual separation.


============================================================
IV. SECTION 1 — GENERAL CVA
============================================================

Keep §1 compact.

Retain:

CVA_0 = E^Q[D(0,τ)Lτ 1_{τ≤T}]

and its factorised/discrete representations.

Define:

- Q as the pricing measure;
- S(t) as survival probability implied by the hazard curve calibrated to market CDS spreads;
- -dS(t) as the marginal event/default probability;
- DF(0,t) as the outer CVA discount factor.

Make clear that CDS calibration supplies the pricing-measure default distribution; no separate ad hoc "Q transformation" of hazard rates is required.

Do not confuse the outer CVA discount factor with any default-state collateral valuation basis.

Keep only a SHORT discrete implementation bridge here.

Move detailed discussion of:

- production default weights;
- embedded (1-R);
- conditional-default treatment;
- common-factor hazard adjustments;
- copula dependence;
- wrong-way-risk implementation;
- bucket-end exposure convention

to the later production implementation section.

The introductory section should establish the economics, not overwhelm the reader with production machinery.


============================================================
V. SECTION 2 — EVENT τ
============================================================

Keep the strong contractual derivation already present.

Clearly distinguish:

- the sovereign issuer default / restructuring event;
- Original Collateral Default as the SPIRE contractual trigger;
- consequential swap termination;
- liquidation;
- early redemption.

Do not describe these as several independent defaults.

Retain the proposed sovereign-issuer CDS/hazard curve as the timing model, together with the event-definition basis caveat.


============================================================
VI. SECTION 3 — PERFORMING STATE
============================================================

Preserve the performing swap economics and sign convention.

However, REVIEW §3.2 CAREFULLY.

Earlier drafts gave P_t simulation a central role because it was assumed that future pre-default bond prices were necessarily required both for swap valuation and for CVA.

That conclusion is now too broad.

Separate:

A. V_t / V_t^co:
   supplied by the existing regular swap calculator.

B. Performing CSA dynamics:
   P_t may be needed for collateral market value and therefore for

   N_t^post
   = min(V_t^+/(q_CSA P_t), N^pool).

C. Event-state collateral recovery:
   whether P_t is needed depends on the chosen B_t^liq model.

Do not claim that collateral P_t simulation is intrinsically required for CVA.

If the baseline recovery model is:

B_t^liq = R_eff IR_t N,

then P_t is NOT required to determine default-state collateral realization.

P_t may still matter for:

- performing-state collateral calls;
- margin/funding analysis;
- richer recovery models;
- other applications.

Retain the credit-risky-bond route versus bond-yield route only to the extent that it remains relevant.

If the detailed discussion is no longer necessary for the baseline CVA methodology, move it to a refinement/appendix.

The paper should not spend more effort modeling P_t than the final loss formula requires.


============================================================
VII. SECTION 4 — DEALER CLOSE-OUT CLAIM Vτ^co
============================================================

Make this section conceptually clean.

Its governing question is:

    What is the Dealer contractually owed when the transaction terminates?

Preserve the scheduled-obligation analysis.

Emphasize:

Vτ^co
= ordinary swap close-out valuation
= PV of the Issuer's remaining scheduled bond-side obligations
  minus PV of remaining Dealer Note-side obligations,

under the applicable close-out convention.

The bond-side cashflows are scheduled contractual amounts owed by SPIRE.

The sovereign's default does not itself reduce those contractual amounts to the distressed value of the sovereign collateral.

Therefore:

Vτ^co ≠ Bτ^liq - PVτ(Note leg)

as a mechanical identity.

Operational conclusion:

    Vτ^co is computed by the existing swap calculator.

Do not create a separate sovereign-default valuation model for Vτ^co.

Keep any genuinely unresolved ETA / CSA-credit mechanical question marked [TO CONFIRM], but do not leave the old scheduled-vs-pass-through question looking equally unresolved if the documents now substantially settle it.

If useful, retain pass-through and extinguisher constructions only as short alternative structures explaining when the result would differ.


============================================================
VIII. SECTION 5 — AVAILABLE RESOURCES:
     WHAT COLLATERAL CAN THE DEALER REACH?
============================================================

THIS SECTION SHOULD BE REBUILT.

Its purpose should be predominantly CONTRACTUAL / ACCESS / QUANTITY based.

Do NOT make §5 the sovereign-recovery modeling section.

The governing question is:

    Once the event occurs, what assets/resources can legally be applied
    to satisfy the Dealer's positive close-out claim?

Organize the section around:

1. Mortgaged Property;
2. Available Proceeds;
3. Condition 15 waterfall;
4. Dealer priority at tiers (i) and (vi);
5. senior tiers (ii)-(v), represented as Kτ^sen where appropriate;
6. posted versus unposted Original Collateral;
7. anti-double-counting of CSA collateral;
8. limited recourse / extinguishment.

Derive the whole-pool conclusion:

Nτ^avail ≈ N^pool,

subject to the contractual waterfall.

Be explicit:

    Dealer reaches the pool through priority recourse,
    not because it owns the pool.

The key distinction should become:

    §5 answers HOW MUCH / WHICH RESOURCES can support the claim.
    §6 answers WHAT THOSE RESOURCES ARE WORTH.

Do not mix these two questions.

Bτ^avail remains the final monetary resource quantity, but its principal valuation input Bτ^liq is developed in §6.

Conceptually:

N^pool
    -- default/liquidation valuation in §6 -->
Bτ^liq(N^pool)
    -- Condition 15 / senior costs / claim cap in §5 -->
Bτ^avail.

Review any existing formula such as:

Bτ^avail ≈ min(Bτ^liq(N^pool)-Kτ^sen, Vτ^co).

A recovery amount cannot become negative and should be capped by the Dealer's POSITIVE contractual claim rather than a signed Vτ^co.

Correct the formula as necessary, subject to exact ETA/CSA mechanics.

Do not allow a negative Vτ^co to imply negative Bτ^avail.


============================================================
IX. NEW SECTION 6 — MODELING DEFAULT-STATE COLLATERAL
    REALISATION Bτ^liq
============================================================

This should become the principal unresolved QUANTITATIVE MODELING section.

Its governing question is:

    Conditional on Original Collateral Default occurring at τ,
    what value is actually realised from the sovereign Original
    Collateral through the SPIRE liquidation process?

Start by defining three conceptually distinct collateral values:

B_{τ-}
= performing-state market value immediately before the event;

Bτ^def
= market value of the resulting sovereign default/restructuring
  claim or restructuring package;

Bτ^liq
= realization/proceeds ultimately obtained through the SPIRE
  liquidation process.

Show:

B_{τ-}
    -- sovereign credit event -->
Bτ^def
    -- SPIRE Liquidation Period -->
Bτ^liq.

IMPORTANT:

These are conceptual stages.

Do NOT imply that all three must be independently simulated.

Which quantities actually need models depends on the chosen recovery architecture.


------------------------------------------------------------
6.1 WHAT CVA ACTUALLY NEEDS
------------------------------------------------------------

State clearly that the final collateral-side quantity needed for the loss calculation is ultimately:

Bτ^liq,

because the question is what resources the Dealer can actually obtain from the impaired collateral through SPIRE liquidation.

Bτ^def may be an intermediate modeling quantity.

B_{τ-} may or may not be required depending on recovery convention.

Also distinguish the clocks:

τ = contractual sovereign/default event;

the close-out valuation occurs at/about the contractual termination date;

actual collateral realization may occur over the subsequent Liquidation Period.

Therefore Bτ^liq is notation for the realization associated with the event at τ; it is NOT necessarily a spot executable market price observed exactly at τ.


------------------------------------------------------------
6.2 BASELINE MODEL — ALL-IN EFFECTIVE RECOVERY
------------------------------------------------------------

Present the proposed first implementation:

Bτ^liq(N)
≈ R_eff IRτ N.

Define R_eff carefully.

It is NOT:

- a universal sovereign recovery rate;
- the CSA Valuation Percentage;
- SPV LGD;
- a regulatory haircut;
- automatically a recovery-of-face convention.

It is:

    an effective all-in realization per unit of index-adjusted
    principal intended to approximate the market value/proceeds
    ultimately realised from the defaulted/restructured sovereign
    collateral through the SPIRE liquidation process.

If R_eff is calibrated from actual restructuring-package values,
CDS auction values, distressed transaction prices, or comparable
market observations, specify exactly what those observations represent.

CRITICAL COMPUTATIONAL CONSEQUENCE:

Under this baseline:

B_t^liq = R_eff IR_t N,

so COLLATERAL P_t SIMULATION IS NOT REQUIRED to determine liquidation value.

There is no need to simulate:

P_t → P_t^def → P_t^liq

merely because such a chain can be constructed theoretically.

At each hypothetical default bucket t, the collateral realization can be obtained directly from R_eff, IR_t and N.

Make this conclusion prominent.


------------------------------------------------------------
6.3 RICHER MODEL — RESTRUCTURING-PACKAGE VALUATION
------------------------------------------------------------

Introduce a richer structural model.

Let:

R_package,τ
= the actual restructuring consideration received by holders,
  e.g. new bonds, cash, GDP-linked instruments, etc.

Then:

Bτ^def = PVτ(R_package,τ).

SPIRE subsequently realizes those assets:

Bτ^liq
= Bτ^def - Cτ^liq

or equivalently:

Bτ^liq
= (1-hτ^liq) Bτ^def.

Explain that this mirrors actual sovereign restructuring economics more closely.

The old bond is not simply assigned an arbitrary percentage recovery; creditors receive a package whose market value can be estimated.

Show that Model 6.2 is a reduced-form compression of this model:

R_eff
= Bτ^liq / (IRτ N).

This gives R_eff a clear economic interpretation.


------------------------------------------------------------
6.4 PRE-DEFAULT-PRICE-DEPENDENT / FULL STATE-TRANSITION MODEL
------------------------------------------------------------

Present the richer alternative:

P_{τ-}
    → Bτ^def
    → Bτ^liq.

This is where recovery conventions such as RMV/RFV/RT become relevant.

Use Laurent et al. carefully.

Laurent is useful for:

- distinguishing recovery conventions;
- understanding how recovery specification affects pre-default bond pricing;
- understanding when pre-default market price P_t enters the recovery rule;
- understanding consistency between hazard, recovery and bond valuation.

Laurent is NOT direct authority for:

- SPIRE contractual liquidation;
- the actual Greek restructuring package;
- the legally correct SPIRE liquidation proceeds.

Do not overextend it.

State the key model-selection conclusion explicitly:

    Simulation of P_t is NOT intrinsically required by CVA.

It becomes required if:

1. the selected recovery convention makes Bτ^liq or Bτ^def depend on P_{τ-}; or
2. a richer dynamic state-transition model is deliberately adopted; or
3. P_t is separately required for performing-state CSA/margin mechanics.

Thus:

    "Do we need P_t simulation?"

is downstream of:

    "How do we model Bτ^liq?"


------------------------------------------------------------
6.5 CALIBRATION / EMPIRICAL EVIDENCE
------------------------------------------------------------

Consolidate the currently scattered recovery literature here.

Use the existing three main sources:

1. Laurent et al.;
2. Greek sovereign-debt empirical/recovery paper;
3. Greek debt-management/restructuring-process paper.

Also incorporate, where useful:

- Sturzenegger & Zettelmeyer:
  sovereign restructuring recovery/haircut measurement based on
  PV of instruments received;

- Cruces & Trebesch:
  cross-country empirical sovereign restructuring/haircut evidence;

- Asonuma, Niepelt & Rancière:
  bond-specific prices, maturity and restructuring haircuts.

Do NOT turn this section into a generic literature review.

Every citation should help answer one of the actual modeling questions:

1. Is constant R_eff defensible as a baseline?
2. What does R_eff economically represent?
3. What empirical range/sensitivity should be used?
4. Is recovery materially instrument-specific?
5. Does recovery depend materially on pre-default bond price?
6. How much does maturity/coupon/restructuring-package composition matter?
7. Are observed recovery values already distressed/liquidity-adjusted?

Distinguish clearly:

THEORY
vs
EMPIRICAL CALIBRATION
vs
CONTRACTUAL SPIRE MECHANICS.


------------------------------------------------------------
6.6 FROM DEFAULT-STATE VALUE TO SPIRE LIQUIDATION VALUE
------------------------------------------------------------

Use the legal work already done on the Liquidation Period.

The Disposal Agent must liquidate within the contractual period, and the process can end in a forced sale irrespective of price obtainable.

Therefore distinguish:

Bτ^def
= fundamental/market value of restructuring consideration;

Bτ^liq
= value actually realised under SPIRE's constrained liquidation process.

Investigate whether the baseline can reasonably take:

Bτ^liq ≈ Bτ^def,

or whether an incremental liquidation adjustment is warranted:

Bτ^liq
= (1-hτ^liq) Bτ^def.

Avoid double-counting.

If R_eff is calibrated using:

- distressed transaction prices;
- restructuring-package market values;
- CDS auction prices;
- actual post-default market prices

that already contain relevant liquidity/distress effects, do NOT mechanically apply another generic liquidity haircut.

A separate liquidation haircut should represent ONLY an incremental effect not already embedded in calibration.

The documented 30-Reference-Business-Day forced-sale provision is the principal candidate for such an incremental effect.

Treat this as a refinement unless empirical/materiality evidence justifies making it baseline.


============================================================
X. SECTION 7 — GENERAL EVENT LOSS AND CVA
============================================================

Once §§4–6 are complete, §7 should become SHORTER and largely mechanical.

Start from:

Lτ^Dealer
= [positive Dealer contractual claim
   - resources actually available to satisfy it]^+.

Then:

Lτ^Dealer
= [Vτ^co - Bτ^avail]^+,

with the exact positive-claim convention and ETA/CSA accounting stated carefully.

Use the ETA / posted-collateral decomposition only to demonstrate why this compact expression is contractually correct and does not double-count collateral.

Then substitute the §5/§6 resource model as appropriate.

Be consistent about B^def versus B^liq.

The current draft sometimes says B^liq is the CVA-relevant realization but later writes the final loss using B^def.

Resolve this.

If the model assumes:

Bτ^liq = Bτ^def,

state that explicitly as a modeling approximation.

Otherwise the final event-loss equation should use the liquidation/available-resource object that the methodology actually derives.


============================================================
XI. DELETE / DEMOTE THE OLD CANCELLATION RESULT
============================================================

The previous trade-specific argument:

Bτ^co = Bτ^def = Bτ^liq
    → ΔBτ = 0
    → CVA = 0

is DATED under the current contractual interpretation.

Do NOT retain it as the adopted result.

The reason is now clear:

The bond-side swap claim is the Issuer's contractual scheduled obligation.

The collateral realization is the impaired sovereign asset.

Therefore they are different objects.

The fact that the same sovereign bond economically underlies both sides does not imply accounting cancellation.

If useful, retain the old cancellation only as a brief ALTERNATIVE CONSTRUCTION:

- pass-through swap;
- extinguisher structure;
- or hypothetical close-out convention under which claim tracks realization.

Make explicit that these are not the adopted reading for the illustrative trade.

Do not state CVA=0 based on this cancellation.


============================================================
XII. SECTION 8 — PRODUCTION IMPLEMENTATION
============================================================

Rebuild the implementation discussion around the clean separation:

EXISTING COMPONENT:

market simulation
    → regular swap calculator
    → V_t^co.

NEW COMPONENT:

hypothetical sovereign default at t
    + recovery/liquidation model
    → B_t^liq.

CONTRACTUAL RESOURCE MAPPING:

B_t^liq
    + Condition 15 waterfall
    + senior deductions/caps
    → B_t^avail.

EVENT LOSS:

(V_t^co, B_t^avail)
    → L_t.

EXISTING CVA AGGREGATION:

L_t
    + default weights
    + discounting
    → CVA.

The principal implementation question is therefore NOT:

    "How do we insert a whole new sovereign bond pricer into CVA?"

It is:

    "How do we generate the conditional default-state liquidation
    value of non-cash collateral at each exposure date/scenario and
    feed it into the existing exposure/loss calculation?"

Under baseline Model 6.2:

B_t^liq = R_eff IR_t N,

this may be a very small new component.

P_t simulation is model-dependent, not automatically required.


============================================================
XIII. CONDITIONAL EXPOSURE / WRONG-WAY RISK
============================================================

Preserve the important structural WWR insight but simplify its presentation.

The theoretically correct quantity is:

E^Q[L_t | τ=t],

not automatically E^Q[L_t].

The sovereign event both:

- determines event timing; and
- impairs the collateral realization.

This is structural wrong-way dependence.

However, do not confuse "structural WWR exists" with "therefore P_t must be simulated."

Under the baseline model:

τ governed by sovereign hazard,
Bτ^liq = R_eff IRτ N,

the default event itself already switches collateral into the impaired recovery state.

A separate pre-default P_t process is not required merely to create this dependence.

The production question is whether the existing engine correctly represents:

E^Q[L_t | τ=t],

or approximates it through:

- unconditional exposure;
- survival/default weights;
- conditional-default overlay;
- common-factor hazard adjustment;
- copula correlation;
- simultaneous-default treatment.

Move the detailed production investigation here.

Do not bury it in §1.


============================================================
XIV. RECOVERY / LGD DISCIPLINE
============================================================

Keep three completely different concepts separate:

1. R_eff:
   sovereign collateral realization/recovery.

2. q_CSA / Valuation Percentage:
   performing-state collateral posting scalar.

3. residual vehicle severity / LGD:
   treatment of the Dealer claim remaining after waterfall.

Never substitute one for another.

If L_t already equals the actual residual dollar loss after applying collateral realization and waterfall, then:

CVA = E[D L]

does not require another generic LGD multiplier.

If production weights contain (1-R), determine whether that factor must be set consistently (potentially effectively 1 on the already-defined residual) rather than blindly multiplying the loss again.

Flag as [TO CONFIRM] if implementation remains unresolved.


============================================================
XV. SENSITIVITIES AND MODEL HIERARCHY
============================================================

Rewrite the sensitivity section around quantities that actually survive the derivation.

BASELINE modeling sensitivities should include:

- R_eff;
- V_t^co;
- IR_t where relevant;
- senior deductions K_t^sen;
- sovereign hazard/default curve;
- any incremental liquidation adjustment.

Potential richer-model sensitivities:

- stochastic recovery;
- restructuring-package composition;
- bond-specific recovery;
- maturity dependence;
- pre-default P_t dependence;
- recovery/default dependence;
- explicit Liquidation Period dynamics.

Do not rank N_t^post or margin-period price diffusion as first-order CVA loss drivers merely because they are technically interesting if whole-pool access means they do not materially survive into final recovery.

The governing principle remains:

    DERIVE FIRST;
    MODEL ONLY QUANTITIES THAT SURVIVE THE DERIVATION.


============================================================
XVI. APPENDICES / LITERATURE
============================================================

Reorganize literature by FUNCTION rather than chronology.

A. RECOVERY-CONVENTION THEORY

Laurent et al.

Purpose:
- RMV/RFV/RT;
- pre-default pricing implications;
- recovery convention consistency;
- when P_t enters the recovery rule.

Do not use Laurent as empirical proof of SPIRE liquidation recovery.


B. EMPIRICAL SOVEREIGN RESTRUCTURING / RECOVERY

Greek empirical paper;
Sturzenegger-Zettelmeyer;
Cruces-Trebesch;
Asonuma-Niepelt-Rancière.

Purpose:
- observed recovery/haircut distributions;
- bond-specific effects;
- maturity effects;
- relationship between prices and eventual restructuring outcomes;
- calibration/sensitivity evidence.


C. ACTUAL RESTRUCTURING PROCESS / CASE STUDY

Greek debt-management/restructuring paper and related factual material.

Purpose:
- what creditors actually received;
- how old claims were transformed;
- timing;
- restructuring-package composition;
- how market realization differs from simple face-value recovery.


D. SPIRE CONTRACTUAL EVIDENCE

Keep detailed prospectus / Series terms / CSA / waterfall citations here where too lengthy for the main derivation.


============================================================
XVII. DOCUMENT-WIDE EDITING DISCIPLINE
============================================================

The revised document should NOT become materially longer merely because the recovery analysis is richer.

CONSOLIDATE AGGRESSIVELY.

In particular:

- explain scheduled-vs-pass-through once in §4;
- explain whole-pool access once in §5;
- explain anti-double-counting once in §5;
- define B_{τ-}, Bτ^def and Bτ^liq once in §6;
- define R_eff once in §6;
- explain Laurent once;
- explain structural WWR once;
- move implementation-specific details to §8;
- remove repeated warnings where a cross-reference suffices;
- demote abandoned exploratory branches;
- preserve [MODEL CHOICE] for genuine modeling choices;
- preserve [TO CONFIRM] only for genuinely unresolved documentary or implementation questions.

The paper should become easier to read even if the underlying analysis is sophisticated.


============================================================
XVIII. TARGET FINAL NARRATIVE
============================================================

The revised paper should leave the reader with this mental model:

1. CVA measures discounted loss on the Dealer's contractual claim.

2. Original Collateral Default is the contractual event that triggers
   termination and liquidation.

3. The Dealer's close-out claim Vτ^co is valued using the ordinary
   swap calculator. The Issuer owes scheduled bond-referenced
   amounts; sovereign default does not rewrite that promise.

4. Through the SPIRE waterfall the Dealer can reach substantially the
   whole relevant collateral pool, subject to senior claims. It is
   not restricted to Nτ^post.

5. The key new modeling problem is therefore:

       what is the defaulted sovereign collateral actually worth
       when SPIRE liquidates it?

6. Baseline:

       Bτ^liq ≈ R_eff IRτ N^pool.

7. Under that baseline, collateral P_t simulation is NOT needed for
   recovery.

8. Richer alternatives can model:

       restructuring package
           → default-state market value
           → SPIRE liquidation value,

   and only models that depend on P_{τ-} require the corresponding
   pre-default price simulation for recovery.

9. Condition 15 converts Bτ^liq into resources available to the
   Dealer.

10. The event loss is:

       Lτ^Dealer = [Vτ^co - Bτ^avail]^+,

    with contractual CSA crediting and senior claims handled without
    double-counting.

11. The existing CVA machinery then integrates the pathwise loss
    against the sovereign-event distribution.

The main intellectual progression should therefore be:

CONTRACT
    → CLAIM
    → ACCESS
    → RECOVERY / LIQUIDATION VALUE
    → LOSS
    → CVA
    → IMPLEMENTATION.

The principal new quantitative section is §6:

    MODELING DEFAULT-STATE COLLATERAL REALISATION Bτ^liq.

That section should make clear both why the reduced-form R_eff model
is an attractive first implementation and exactly what evidence or
materiality would justify moving to a richer restructuring-package or
P_t-dependent model.
