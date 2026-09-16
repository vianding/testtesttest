# Prompt for Agent B --- Major Revision of "Pricing CVA --- Sovereign Bond Collateral"

You are revising the existing note **"Pricing CVA --- Sovereign Bond
Collateral."** This is the next major iteration, not a cosmetic edit.

The current draft has a good conceptual skeleton---pathwise exposure,
finite non-cash collateral pool, distinction between CSA valuation
percentage and event-state collateral value, distinction between
sovereign collateral recovery and residual vehicle LGD, and recognition
of structural wrong-way risk. **Preserve those strengths.**

However, the draft was written before we had a sufficiently complete
understanding of the actual SPIRE transaction mechanics. The next
version must rebuild the analysis around the actual contractual/economic
state transition:

\[ `\boxed{
\text{performing SPIRE}
\rightarrow
\text{contractual early-redemption trigger}
\rightarrow
\text{swap close-out + collateral realization}
\rightarrow
\text{Available Proceeds / waterfall}
\rightarrow
\text{Dealer loss}
\rightarrow
\text{CVA}.
}`{=tex} \]

The document should become a defensible **SPIRE CVA methodology**,
rather than primarily a generic non-cash-collateral CVA abstraction.

## Annotation convention

Retain only the existing **\[MODEL CHOICE\]** and **\[TO CONFIRM\]**
annotations.

Use **\[MODEL CHOICE\]** only for substantive modeling assumptions or
choices among economically plausible alternatives. Use **\[TO
CONFIRM\]** only for genuinely unresolved documentary or implementation
questions.

Do **not** introduce `[DOC]`, `[MARKET]`, `[DERIVED]`, `[SENSITIVITY]`,
or other inline labels. Established contractual facts, observed inputs,
derivations and sensitivity cases should be communicated naturally
through the prose, equations, tables, citations and input-provenance
table.

As transaction mechanics are now established from SPIRE documentation,
remove **\[MODEL CHOICE\]** from statements that are contractual facts.
In particular, the linkage from **Original Collateral Default → early
redemption/unwind** for this Series should no longer be presented as a
modeling choice. The modeling choice is the stochastic
representation/calibration of that contractual event.

------------------------------------------------------------------------

## 1. Documentation basis and hierarchy

Use the **2025 SPIRE Base Prospectus as the governing Base Prospectus
for this transaction**, because the Series-specific documentation refers
to the 2025 prospectus. Do not silently substitute the 2026 prospectus.

Use the transaction's Series-specific terms, Appendix 1 swap terms and
Appendix 2 CSA terms wherever they override or instantiate the
programme-level mechanics.

The 2026 prospectus may be mentioned only as a cross-check/later
programme version. If useful, retain a short footnote saying that a
targeted review found no material change in the provisions relevant to
Original Collateral Default, ERA, swap close-out, limited recourse and
liquidation waterfall, while making clear this is not a complete legal
redline.

Do not invent contractual provisions or implementation behavior. Where
something remains genuinely unknown, mark it **\[TO CONFIRM\]**.

------------------------------------------------------------------------

## 2. Rebuild Section 1 around the actual loss-relevant event

The current draft treats C2---collateral-issuer default/restructuring
causes transaction unwind---as a modeling assumption. Correct this.

For this transaction the Original Collateral is the sovereign
inflation-linked bond. Under **Condition 8(c), Original Collateral
Default is directly a contractual early-redemption trigger**. Once the
Calculation Agent determines an Original Collateral Default and the
relevant notice is given, the Notes become due at their Early Redemption
Amount.

Explain the contractual sequence:

\[ `\text{Original Collateral Default}`{=tex} `\rightarrow`{=tex}
`\text{Early Redemption Trigger}`{=tex} `\rightarrow`{=tex}
```{=tex}
\begin{cases}
\text{swap unwind/termination},\\
\text{collateral liquidation},\\
\text{ERA / Available Proceeds mechanics}.
\end{cases}
```
\]

An Early Redemption Trigger generally constitutes a Liquidation Event.
For this Series, the **Liquidation Period Cut-off is 30 Reference
Business Days**.

Do not describe collateral default, swap termination and note early
redemption as three unrelated simultaneous defaults. For the modeled
sovereign-credit scenario, Original Collateral Default is the primary
contractual trigger and the other state changes follow from the
resulting transaction unwind.

Briefly explain that Condition 8 contains other early-redemption
routes---tax, Original Collateral Call, swap/repo/SL termination and
counterparty events, illegality, Original Collateral Disruption,
reference-rate events, Issuer Call, noteholder option and note-level
Events of Default---but do not clutter the core model with irrelevant
routes. For this Series:

-   Repo = N/A;
-   securities lending = N/A;
-   Issuer Call = N/A;
-   Noteholder Early Redemption Option = N/A.

Most importantly, distinguish:

\[ `\boxed{
\text{Original Collateral}
\neq
\text{generic swap reference asset}
\neq
\text{CSA collateral}.
}`{=tex} \]

These roles happen to overlap materially in this transaction. Condition
8(c) is triggered because the sovereign bond is **Original Collateral**,
not merely because its cashflows form a swap leg.

Retain a general-methodology observation: in another SPIRE Series where
a risky bond is merely a swap reference asset and Original Collateral is
something else such as cash, default of the reference bond would not
automatically constitute Condition 8(c) Original Collateral Default. The
Series-specific swap terms / Confirmation (including applicable Swap
Agreement terms) would have to determine what happens to the swap. If
those terms cause an Early Termination Date, the generic SPIRE Swap
Termination Event / early-redemption machinery may then apply.

The **modeling choice** is therefore not whether Original Collateral
Default causes early redemption---it does contractually for this Series.
The modeling choice is how the contractual Original Collateral Default
event is represented probabilistically, e.g. by a sovereign
hazard/intensity process calibrated from an issuer credit curve.

------------------------------------------------------------------------

## 3. Introduce the actual transaction economics before defining exposure

The revised paper must explain the actual swap before introducing (V_t).

Based on Appendix 1, schematically:

\[ `\text{SPIRE}`{=tex}`\rightarrow`{=tex}`\text{Dealer}`{=tex}:
C\_{`\rm Bond`{=tex}}(t)+A_T, \]

\[ `\text{Dealer}`{=tex}`\rightarrow`{=tex}`\text{SPIRE}`{=tex}:
C\_{`\rm Note`{=tex}}(t)+N\_{`\rm Note`{=tex}}
+`\text{Transaction Specific Costs}`{=tex}. \]

Be precise:

-   Party B/SPIRE pays Dealer the scheduled Original Collateral coupons.
-   At maturity Party B pays the aggregate **scheduled redemption
    amount** of the Original Collateral, (A_T).
-   (A_T) is not (A_0) and is not "market value at maturity." For an
    inflation-linked bond it reflects the applicable scheduled
    index-adjusted redemption amount, approximately base principal times
    the applicable inflation/index ratio subject to the bond convention.
-   Dealer pays SPIRE the Note coupons.
-   Dealer pays the Note Final Redemption Amount at maturity, which for
    this Series is 100% of Specified Denomination.
-   Dealer separately pays the periodic Transaction Specific Costs under
    the Interim Exchange Amount provision.

Explain the economic intuition carefully: the Dealer receives much of
the performing economics of the Original Collateral through the swap and
supplies the economics required for SPIRE to service the Note, while
SPIRE remains the legal holder/issuer structure. Do **not** say that
Dealer legally buys the bond or legally issues the Note.

The Original Collateral principal/redemption amount exceeds Note
principal. This is consistent with the prospectus description of
leveraged exposure to Original Collateral. Do not interpret this as
ordinary investor upside leverage in the performing state: the Note can
still pay fixed coupons plus par. The leverage is particularly relevant
to adverse/termination states and to the amount of collateral economics
sitting behind the smaller Note claim.

Then define the performing-state Dealer-side swap MTM schematically:

\[ V_t\^{Dealer} = PV_t(C\_{`\rm Bond`{=tex}}+A_T) -
PV_t(C\_{`\rm Note`{=tex}}+N\_{`\rm Note`{=tex}}+`\text{costs}`{=tex}),
\]

with exact implementation conventions left to the pricing model.

------------------------------------------------------------------------

## 4. Add a proper pre-default risky-bond valuation section

This is a major omission in the current draft.

Today's bond market value is observed:

\[ B_0=P_0\^{market}. \]

The modeling problem is **not primarily pricing today's bond**. It is
generating coherent future pre-default bond values:

\[ B_t(`\omega`{=tex}). \]

Those future values matter through at least two channels:

\[ B_t `\rightarrow`{=tex}
```{=tex}
\begin{cases}
V_t & \text{through the bond-side swap economics},\\
\text{CSA collateral capacity/posting} & \text{because the bond is eligible VM}.
\end{cases}
```
\]

Present two possible implementation architectures without prematurely
selecting one unless production architecture already establishes the
choice.

### Credit-risky bond route

\[ B_t=B(t;r_t,`\pi`{=tex}\_t,`\lambda`{=tex}\_t,R,`\ldots`{=tex}), \]

where the issuer credit process contributes both to pre-default
valuation and to survival/default timing.

### Yield / bond-option-model route

\[ B_t=B(t;y_t,`\pi`{=tex}\_t,`\ldots`{=tex}), \]

while a separate sovereign intensity process (`\lambda`{=tex}\_t)
determines survival/default timing.

If the second architecture is used, explicitly state that the joint
dynamics of (y_t) and (`\lambda`{=tex}\_t) must be economically
coherent. The model should not routinely produce states with a very high
near-term sovereign default probability while the same sovereign bond
remains implausibly highly valued.

Do not require a one-to-one mapping because bond yield/spread can
contain rates, liquidity, risk premia, technicals and other components
beyond expected default loss.

Make clear why a stochastic bond/bond-option model may be needed: **not
because the bond necessarily has embedded optionality, but because CVA
requires conditional future bond MTMs across Monte Carlo states.**

If an explicit credit-risky model is used, today's market price should
anchor/calibrate the model rather than be replaced by a theoretical
price without reason.

------------------------------------------------------------------------

## 5. Preserve but update the CSA/posting section

Preserve the current finite-pool logic, but instantiate it with the
actual Series terms.

The Original Collateral is explicitly eligible VM for SPIRE. Its
contractual CSA valuation percentage is **85%**. Daily valuation
applies. MTA is **EUR 250,000** for each side. **Delivery Cap =
Applicable.**

Define clearly:

\[ N\^{pool} =
`\text{total Original Collateral base notional in the pool}`{=tex}, \]

\[ N_t\^{post} =
`\text{base notional actually posted under the CSA immediately before the event}`{=tex}.
\]

Also distinguish where useful:

\[ B_t^{pool}=P_tN^{pool}, `\qquad`{=tex} B_t^{post}=P_tN_t^{post}, \]

and the unposted remainder, without implying that all of these
quantities have identical legal ownership status.

Subject to exact CSA call/MTA/cap mechanics, retain the reduced-form
representation:

\[ N_t\^{post} = `\min`{=tex}`\left`{=tex}(
`\frac{V_t^+}{q_{CSA}P_t}`{=tex}, N\^{pool} `\right`{=tex}), \]

where for this transaction (q\_{CSA}=85%), unless another exact
contractual adjustment is identified.

Distinguish:

\[ q\_{CSA} \]

from sovereign default/restructuring recovery. The 15% CSA haircut is
**not** a 15% expected/default loss assumption.

Explain the two regimes:

**Cap not binding:** deterioration in (P_t) can cause more bond quantity
to be called, partly preserving collateral credit.

**Delivery Cap binding:** available eligible collateral is exhausted;
further bond deterioration directly reduces collateral coverage and
leaves residual Dealer exposure.

Importantly, because Delivery Cap is Applicable, exposure above
available collateral is a **contractually capped residual exposure**,
not automatically a failure by SPIRE to satisfy an additional uncapped
collateral delivery obligation. Do not make the stronger universal claim
that a Delivery Cap can never coexist with another default/termination
event.

Keep "cap is expected to bind before default" as a **\[MODEL CHOICE\]**,
not a documentary fact. A sudden event can occur before exhaustion, so
the `min` formulation remains useful even if cap exhaustion is the base
case.

Eligibility of the Original Collateral as VM does not mean the entire
bond pool is automatically transferred to Dealer. Posting is driven by
the CSA-required amount subject to the finite pool, Delivery Cap, MTA
and exact return mechanics.

------------------------------------------------------------------------

## 6. Completely expand the definition and modeling of (V\_`\tau`{=tex}\^{uw})

The current equation

\[
V\_`\tau`{=tex}\^{uw}=PV\_`\tau`{=tex}(`\text{remaining derivative cashflows; unwind assumptions}`{=tex})
\]

is insufficient.

Explicitly distinguish:

\[
V_t,`\qquad `{=tex}V\_{`\tau`{=tex}^-},`\qquad `{=tex}V\_`\tau`{=tex}^{uw}.
\]

Define:

-   (V_t): performing-state Dealer-side swap MTM;
-   (V\_{`\tau`{=tex}\^-}): swap MTM immediately before Original
    Collateral Default;
-   (V\_`\tau`{=tex}\^{uw}): Dealer claim/value under the **contractual
    unwind state**.

Map the latter to the contractual Swap **Early Termination Amount /
Close-out Amount**:

\[ `\boxed{V_\tau^{uw}\equiv ETA_\tau}`{=tex} \]

as the model representation, while noting that exact legal ETA and a
model PV need not be numerically identical unless the implementation
reproduces the contractual close-out methodology.

Explain that ETA is based on the economic/replacement equivalent of the
terminated transaction and that the documentation specifies CSA
collateral is taken into account.

Therefore:

\[ V\_{`\tau`{=tex}\^-}`\longrightarrow `{=tex}ETA\_`\tau`{=tex} \]

rather than simply carrying the performing-state MTM through default
unchanged.

Investigate/document what the production system currently does for the
unwind valuation basis.

In particular, the current draft lists:

\[ r\_{`\rm base`{=tex}}=`\text{ESTR}`{=tex}+5bp \]

and

\[ r\_{`\rm base,def`{=tex}}=`\text{ESTR}`{=tex}+212bp. \]

Do **not** merely retain these as unexplained constants.

Determine, if possible:

-   which cashflows are discounted using ESTR + 212;
-   whether this curve is used only in the default/unwind state;
-   whether it is intended as a distressed/default-state valuation
    convention, something analogous to an RT-style treatment, or
    something else;
-   whether it contains an effective credit/distress component absent
    from the normal-state valuation.

There is a plausible interpretation that ESTR + 212 is a manually
marked/default-state curve used to value a recovery/restructuring
cashflow in a manner analogous to an RT-type object, with the spread
effectively reflecting credit/distress not otherwise represented. **Do
not assert this without implementation evidence.** Until established,
label the interpretation **\[TO CONFIRM\]**.

Also consider whether (V\_`\tau`{=tex}\^{uw}) should be represented by
separately valuing the remaining legs under the contractual unwind
assumptions rather than forcing a single discount curve across
economically different cashflows. Survival/default treatment should not
be added mechanically if the cashflows being valued are already
conditional on having reached the unwind state.

------------------------------------------------------------------------

## 7. Rebuild the collateral-at-event section around a liquidation model

This is another major quantitative gap.

Do not collapse:

\[ B\_{`\tau`{=tex}\^-},`\qquad`{=tex}
B\_`\tau`{=tex}\^{def/restr},`\qquad`{=tex} B\_`\tau`{=tex}\^{liq}. \]

Define them separately.

-   (B\_{`\tau`{=tex}^-}^{post}): performing-state market value of the
    quantity already posted immediately before the event.
-   (B\_`\tau`{=tex}\^{def/restr}): value of the
    claim/security/restructuring package resulting from sovereign
    default or restructuring.
-   (B\_`\tau`{=tex}\^{liq}): actual or modeled liquidation realization
    through the SPIRE liquidation process.

The contractual/economic sequence should be:

\[ B\_{`\tau`{=tex}^-}^{post} `\rightarrow`{=tex}
`\text{Original Collateral Default/restructuring}`{=tex}
`\rightarrow`{=tex} B\_`\tau`{=tex}\^{def/restr} `\rightarrow`{=tex}
`\text{liquidation}`{=tex} `\rightarrow`{=tex} B\_`\tau`{=tex}\^{liq}.
\]

The Series has a 30-Reference-Business-Day liquidation cut-off, so do
not implicitly assume instantaneous liquidation unless explicitly
adopted as an implementation approximation.

The CVA-relevant object is not an abstract sovereign "recovery rate." It
is the **market value/realization of the assets or restructuring
consideration actually available against Dealer's claim**.

Preserve the reduced-form first implementation if useful:

\[ B\_`\tau`{=tex}\^{uw} `\approx`{=tex}
R\_{`\rm eff`{=tex}},IR\_`\tau`{=tex},N\_{`\tau`{=tex}^-}^{post}, \]

but define precisely what (B\_`\tau`{=tex}\^{uw}) is intended to proxy.
Prefer explicit (B\_`\tau`{=tex}\^{liq}) or
(B\_`\tau`{=tex}\^{def/restr}) notation where it improves clarity.
Characterize the formula as:

\[
`\boxed{\text{a reduced-form approximation to event-state market/liquidation value}}`{=tex}
\]

rather than a universal law of sovereign recovery.

(R\_{`\rm eff`{=tex}}) should represent the effective market value per
unit of index-adjusted old principal of the recovery/restructuring
consideration relevant to the unwind.

For an inflation-linked bond, carefully retain the distinction between
**base notional** and **index-adjusted redemption claim**. Do not call
the latter "notional." If:

\[ N\_{`\tau`{=tex}^-}^{post} \]

is carried in base-notional units, the index ratio must be applied
explicitly when the event-state claim is index-adjusted.

If (R\_{`\rm eff`{=tex}}) is calibrated from observed
restructuring-package values, CDS auction prices, defaulted-security
prices or distressed transaction prices, do not automatically apply an
additional disposal/liquidity haircut if those market conditions are
already embedded in the calibration. A separate adjustment should be
introduced only for an incremental effect not already represented.

------------------------------------------------------------------------

## 8. Reposition Laurent rather than deleting it

Preserve the useful literature discussion of RMV, RT and RFV, but
clarify exactly what Laurent does and does not establish.

Laurent primarily concerns **pre-default relative-value pricing of live
defaultable bonds** under different recovery conventions. It does not
directly prescribe the contractual SPIRE event-state liquidation value.

Use Laurent for:

-   explaining why recovery convention affects pre-default risky-bond
    valuation;
-   distinguishing RMV, RT and RFV;
-   explaining why RFV can imply different treatment of principal and
    coupons;
-   showing that a single hazard process can, under particular
    assumptions, generate different effective coupon/principal
    discounting.

Do **not** use Laurent as authority that RFV is the legally or
empirically correct SPIRE post-default liquidation convention.

Explicitly distinguish:

\[ `\text{pre-default risky-bond pricing}`{=tex} \]

from

\[ `\text{event-state collateral valuation/liquidation}`{=tex}. \]

Greek restructuring evidence can motivate sensitivities or a
principal/face-oriented reduced form, but should not be presented as
establishing a universal sovereign recovery convention.

Preserve the important empirical distinctions already made in the draft:
distressed pre-restructuring price, nominal face reduction, economic/PV
haircut, market value of restructuring consideration and CDS auction
final price are different quantities and must not be used
interchangeably.

The approximately 21--23 per 100 Greek restructuring-package value and
the 21.5% CDS auction result can support an illustrative event-state
package-value scenario, but neither should be described as a universal
sovereign recovery rate or as model-free calibration for this
index-linked bond.

------------------------------------------------------------------------

## 9. Add a dedicated Available Proceeds / waterfall section

This is missing from the current draft and is essential.

Do not jump directly from

\[ (V\_`\tau`{=tex}^{uw}-B\_`\tau`{=tex}^{uw})\^+ \]

to a generic SPV LGD without explaining the legal recovery mechanism.

The transaction is limited recourse to the Series' Mortgaged Property /
Available Proceeds. **Mortgaged Property is broader than the Original
Collateral bond** and includes relevant transaction rights/assets,
including Issuer rights under the Swap Agreement. Do not use "Mortgaged
Property" as a synonym for bond value.

Explain schematically:

\[ `\text{Collateral liquidation proceeds}`{=tex} +
`\text{other relevant Available Proceeds}`{=tex} `\rightarrow`{=tex}
`\text{Condition 15 waterfall}`{=tex} `\rightarrow`{=tex}
`\text{Dealer recovery}`{=tex}. \]

The contractual waterfall includes higher-priority items and then
relevant Swap Counterparty claims ahead of Noteholder redemption claims,
subject to the exact Series terms and amounts already dealt with through
CSA/close-out mechanics.

Define the economically correct Dealer loss first:

\[ `\boxed{
L_\tau^{Dealer}
=
\left[
ETA_\tau^+
-
Recovery_\tau^{Dealer}
\right]^+.
}`{=tex} \]

Only after deriving this should the paper map the loss into the existing
CVA engine's representation such as:

\[ (V-C)\^+`\times `{=tex}LGD. \]

Make clear that the investor-funded note structure can economically
provide a cushion below Dealer in the waterfall, but do not equate note
principal mechanically with Dealer recovery resources without
documentary support. An approximation such as
(N_t^{post}`\approx `{=tex}N^{pool}) in a cap-bound state is a modeling
approximation, not a legal identity.

------------------------------------------------------------------------

## 10. Resolve the CSA/ETA double-counting issue explicitly

This must be treated as an implementation-critical issue.

The contractual ETA calculation takes CSA collateral into account. The
production CVA engine, meanwhile, appears to consume separate
exposure-MtM and collateral-driving inputs and then calculate pathwise:

\[ (V-C)\^+. \]

Therefore the revised document must ask:

> If (V\_`\tau`{=tex}\^{uw}) is intended to represent contractual ETA,
> how exactly should CSA collateral be represented so that posted
> collateral is not counted once inside ETA and again through the
> engine's collateral subtraction?

Do not paper over this.

Derive the contractual economics first and then specify an equivalent
engine mapping.

If the production model instead supplies a **gross pre-collateral
close-out value** as (V), say so and demonstrate how the engine
collateral leg reproduces the contractual net economics.

Similarly, do not add posted Original Collateral again to an ETA that
has already credited the same CSA collateral.

Leave the exact mapping as **\[TO CONFIRM\]** until implementation
semantics are established.

------------------------------------------------------------------------

## 11. Rewrite the wrong-way-risk section using the actual transaction

Explain that the sovereign bond plays three linked roles:

\[ `\boxed{
\text{Original Collateral}
=
\text{bond-side swap economic asset}
+
\text{eligible CSA asset}
+
\text{Condition 8(c) trigger asset}.
}`{=tex} \]

Therefore sovereign deterioration can simultaneously:

\[ `\text{credit deterioration}`{=tex}
`\rightarrow `{=tex}B_t`\downarrow`{=tex}, \]

\[ B_t`\downarrow`{=tex}
`\rightarrow `{=tex}V_t\^{Dealer}`\uparrow`{=tex} \]

through deterioration of the bond economics Dealer receives, all else
equal, while:

\[ B_t`\downarrow`{=tex}
`\rightarrow `{=tex}`\text{CSA collateral capacity}`{=tex}`\downarrow`{=tex}.
\]

Eventually:

\[ `\text{Original Collateral Default}`{=tex} `\rightarrow`{=tex}
`\text{Dealer close-out claim + impairment/liquidation of supporting asset}`{=tex}.
\]

This is the structural wrong-way-risk mechanism. It does not require
imposing an arbitrary correlation between an unrelated exposure and
collateral process, although stochastic joint dynamics are still
required to model (B_t,V_t,N_t\^{post}), cap exhaustion and event
timing.

Do not overstate monotonicity: rates, inflation, other swap-leg effects
and contractual close-out mechanics can also affect (V_t). The
directional statement above is an economic credit-channel statement, all
else equal.

------------------------------------------------------------------------

## 12. Prevent sovereign-credit double counting

Add an explicit subsection on this.

Sovereign credit enters the model through more than one channel, but
these are **not separate additive CVA charges**.

The sovereign credit state can affect:

1.  future pre-default bond value;
2.  therefore the live swap MTM (V_t);
3.  CSA posting/collateral capacity;
4.  probability/timing of Original Collateral Default;
5.  event-state restructuring/default value and eventual liquidation
    realization.

These must form **one internally coherent stochastic loss calculation**.

Do not calculate:

\[ `\text{risky }`{=tex}V_t + `\text{sovereign expected loss}`{=tex} +
`\text{CVA}`{=tex} \]

as independent adjustments.

Instead:

\[ X_t=(r_t,`\pi`{=tex}\_t,y_t,`\lambda`{=tex}\_t,`\ldots`{=tex})
`\rightarrow`{=tex} {V_t,B_t,N_t\^{post}}, \]

followed at the modeled contractual event by:

\[ `\tau`{=tex} `\rightarrow`{=tex}
{ETA\_`\tau`{=tex},B\_`\tau`{=tex}^{def/restr},B\_`\tau`{=tex}^{liq},`\text{waterfall}`{=tex}}
`\rightarrow`{=tex} L\_`\tau`{=tex}\^{Dealer}. \]

The same sovereign process may influence multiple channels. That is
dependence/structural wrong-way risk, not a reason to charge the same
expected sovereign loss multiple times.

------------------------------------------------------------------------

## 13. Revisit MPOR and distinguish the relevant clocks

Do not equate engine MPOR automatically with SPIRE's contractual
liquidation period.

Distinguish:

1.  last collateral valuation/call;
2.  quantity of collateral posted immediately before the event;
3.  Original Collateral Default at (`\tau`{=tex});
4.  swap close-out/ETA valuation;
5.  collateral default/restructuring valuation;
6.  collateral liquidation timing;
7.  contractual 30-Reference-Business-Day liquidation cut-off;
8.  production-engine MPOR convention.

The usual security-collateral principle remains useful:

> over MPOR, posted **quantity** may freeze while its **market value**
> continues to move.

Thus a representation such as

\[ B\_{`\tau`{=tex}+`\delta`{=tex}}\^{post} =
N\_{`\tau`{=tex}^-}^{post}P\_{`\tau`{=tex}+`\delta`{=tex}} \]

may be economically preferable to freezing the collateral market value
as though it were cash.

But establish whether the production engine actually supports this
behavior for the collateral-driving portfolio. If it does not, retain it
as a required extension/refinement rather than pretending it already
exists.

Also distinguish the event-state jump/restructuring transition from
ordinary short-horizon bond diffusion during MPOR. The former may
dominate for this trade; joint short-horizon diffusion can remain a
later refinement if immaterial.

------------------------------------------------------------------------

## 14. Revisit (LGD\_{SPV})

Preserve the distinction between:

\[ R\_{`\rm eff`{=tex}} \]

= sovereign collateral event-state value/recovery convention,

and

\[ LGD\_{SPV} \]

= loss severity on any residual Dealer claim against the
limited-recourse SPIRE compartment after relevant resources have been
credited.

Never apply (R\_{`\rm eff`{=tex}}) again to residual exposure; that
would double count sovereign recovery.

However, do not simply assert (LGD\_{SPV}`\approx1`{=tex}). Derive the
available-resource/waterfall economics first. If the actual
limited-recourse structure implies essentially no further recovery after
the modeled collateral/resources are exhausted, then
(LGD\_{SPV}`\simeq1`{=tex}) may be adopted as a **\[MODEL CHOICE\] /
engine approximation**.

Any conclusion must remain conditional on the exact waterfall, ranking,
guarantee and recourse provisions.

------------------------------------------------------------------------

## 15. Preserve and improve the implementation mapping

The current production-engine observations in Appendix D are valuable.
Preserve:

-   pathwise exposure;
-   marginal-default-probability weighting;
-   separate collateral-driving input;
-   pathwise netting before the positive-part operation;
-   single recovery applied after collateral;
-   absence of native collateral-asset representation.

But rewrite the implementation section around:

\[
`\boxed{\text{economic/legal model first}\rightarrow\text{engine representation second}.}`{=tex}
\]

Determine how the engine should receive or reproduce:

-   performing (V_t);
-   future pre-default (B_t);
-   CSA-driven posted quantity (N_t\^{post});
-   event-state (B\_`\tau`{=tex}^{def/restr}/B\_`\tau`{=tex}^{liq});
-   unwind-state (V\_`\tau`{=tex}\^{uw}/ETA\_`\tau`{=tex});
-   Delivery Cap;
-   MPOR;
-   residual LGD.

The historical scalar (`\kappa`{=tex}) treatment should remain only as
an implementation diagnostic / legacy approximation. Make explicit that:

\[ `\kappa`{=tex}`\times`{=tex}`\text{performing bond MV}`{=tex} \]

captures neither the endogenous posted quantity nor the transition from
performing-state bond value to event-state restructuring/liquidation
value.

Do not infer economic meaning from the historical
(`\kappa=0.7`{=tex}/0.9) parameterizations unless the
source/implementation semantics are established.

Preserve the unresolved reported transformation noted in the current
draft; do not rely on either historical reported figure until its
semantics are confirmed.

------------------------------------------------------------------------

## 16. Update the core CVA equation only after deriving the event loss

The final formulation should be built around the actual Dealer loss:

\[ CVA_0 = E\^Q\[D(0,`\tau`{=tex})L\_`\tau`{=tex}\^{Dealer}\], \]

or its time-grid equivalent.

If a survival-curve representation is used:

\[ CVA_0 = `\int`{=tex}\_0\^T DF(0,t)
E\^Q\[L_t`\mid`{=tex}`\tau`{=tex}=t\] (-dS(t)). \]

The expectation must remain **inside the pathwise positive-part/loss
calculation** where appropriate:

\[ E\[(V-B)\^+\]`\neq`{=tex}(E\[V\]-E\[B\])\^+. \]

Make clear that the sovereign curve used for (S(t)) represents the
modeled **loss-relevant contractual event**, through an explicitly
stated mapping from issuer default/restructuring to Original Collateral
Default. It is not SPIRE's own default curve and is not the Dealer's own
default curve.

Do not conflate the occurrence of sovereign Original Collateral Default
with CVA loss itself. Sovereign default triggers the contractual unwind;
Dealer suffers CVA loss only to the extent the resulting positive
contractual claim is not satisfied through the available
limited-recourse resources.

------------------------------------------------------------------------

## 17. Add one central state-transition diagram

Include near the beginning of the methodology:

\[ `\boxed{
\begin{array}{c}
\text{Performing state}\\
(r_t,\pi_t,y_t,\lambda_t,\ldots)
\\[1mm]
\downarrow\\
B_t,\quad V_t,\quad N_t^{post}
\\[2mm]
\text{Original Collateral Default at }\tau
\\[1mm]
\downarrow\\
\begin{array}{cc}
\text{Swap side} & \text{Collateral side}\\
V_{\tau^-}\rightarrow ETA_\tau &
B_{\tau^-}^{post}\rightarrow
B_\tau^{def/restr}\rightarrow B_\tau^{liq}
\end{array}
\\[3mm]
\downarrow\\
\text{Available Proceeds / Condition 15 waterfall}
\\[1mm]
\downarrow\\
Recovery_\tau^{Dealer}
\\[1mm]
\downarrow\\
L_\tau^{Dealer}
\\[1mm]
\downarrow\\
CVA
\end{array}}`{=tex} \]

Use this as the organizing logic for the entire paper.

------------------------------------------------------------------------

## 18. Suggested revised structure

Reorganize the main body approximately as follows.

### 1. Scope, Transaction Architecture and Loss-Relevant Event

Establish SPIRE, Dealer, Note, Original Collateral, swap, CSA and
contractual trigger.

### 2. Performing-State Economics and State Variables

Actual swap cashflows; (V_t); (B_t); sovereign credit/yield dynamics.

### 3. CSA Collateral Before the Event

85% valuation percentage, MTA, Delivery Cap, finite pool, (N_t\^{post}),
cap regimes.

### 4. Contractual State Transition at Original Collateral Default

Condition 8(c), Early Redemption Trigger, swap termination, liquidation,
timing.

### 5. Swap Close-Out at the Event

(V\_{`\tau`{=tex}^-}`\rightarrow `{=tex}ETA\_`\tau`{=tex}=V\_`\tau`{=tex}^{uw});
normal versus unwind valuation basis; investigate ESTR+212.

### 6. Collateral Value and Liquidation at the Event

(B\_{`\tau`{=tex}^-}^{post}`\rightarrow `{=tex}B\_`\tau`{=tex}^{def/restr}`\rightarrow `{=tex}B\_`\tau`{=tex}^{liq});
reduced-form (R\_{`\rm eff`{=tex}}) baseline; liquidation period.

### 7. Available Proceeds, Waterfall and Dealer Loss

Derive actual Dealer recovery and residual loss.

### 8. CVA Integration

Pathwise loss, survival/default weighting, no double counting.

### 9. Wrong-Way Risk and MPOR

Explain structural dependence and timing.

### 10. Implementation Mapping, Assumptions, Sensitivities and Open Items

Map the economic model into the production engine.

Keep Laurent/recovery literature, Greek restructuring evidence, detailed
contractual support, regulatory context and production-code details in
appendices where they support rather than interrupt the core derivation.

The exact numbering can change if a cleaner structure emerges, but
preserve this economic ordering.

------------------------------------------------------------------------

## 19. Style and terminology requirements

Keep the note technical, concise and first-principles. Do not inflate it
with generic CVA textbook material.

Every equation should correspond to a clearly identified economic or
contractual object.

Avoid using "recovery," "collateral," "default value," "unwind value,"
"notional" or "market value" loosely.

In particular, never conflate:

\[ B\_{`\tau`{=tex}\^-}, `\quad`{=tex} B\_`\tau`{=tex}\^{def/restr},
`\quad`{=tex} B\_`\tau`{=tex}\^{liq}, `\quad`{=tex}
`\text{Available Proceeds}`{=tex}, \]

or:

\[ V_t, `\quad`{=tex} V\_{`\tau`{=tex}\^-}, `\quad`{=tex}
ETA\_`\tau`{=tex}. \]

Do not use "Mortgaged Property" as a synonym for the Original Collateral
bond.

Do not call (A_T) "market value at maturity." It is the scheduled
Original Collateral redemption amount.

Do not call the inflation-adjusted redemption amount "notional."
Throughout, (N\^{pool}) and (N\^{post}) should mean **base notional**,
with the index ratio applied separately where required.

Do not claim that Dealer legally owns the entire Original Collateral
merely because it receives the bond economics under the swap or because
Original Collateral is eligible VM.

Do not assume the whole bond pool is posted under CSA unless Delivery
Cap/path dynamics imply this.

Do not assume liquidation is instantaneous.

Do not call the difference between Original Collateral amount and Note
principal a "loan" or "financing" unless explicitly framed only as an
economic analogy and necessary.

Do not invent contractual provisions or production implementation
behavior. Use **\[TO CONFIRM\]** where evidence is genuinely missing.

Do not preserve old text merely for continuity if our improved
understanding has made it misleading.

------------------------------------------------------------------------

## 20. Inputs, examples and provenance

Update example inputs to the actual Series terms where established. In
particular, do not retain the old illustrative 90% CSA valuation
percentage as though it were contractual; the actual Series term is 85%.

Preserve useful worked-example diagnostics such as:

-   whether Delivery Cap binds;
-   current posted quantity versus total pool;
-   sensitivity to event-state (R\_{`\rm eff`{=tex}});
-   break-even event-state value/recovery;
-   comparison of cap-bound versus capacity-unused states.

But recompute them if contractual inputs have changed. Do not carry old
numerical outputs forward mechanically.

The break-even recovery remains a useful diagnostic, but explicitly
state that because (N\_{`\tau`{=tex}^-}^{post}),
(V\_`\tau`{=tex}\^{uw}), index ratio and other state variables are
stochastic, the break-even recovery is path/state dependent rather than
a single immutable transaction number.

Keep provenance transparent through the input table/footnotes rather
than extra inline labels.

------------------------------------------------------------------------

## 21. Regulatory material

Keep regulatory context subordinate to the economic methodology.

Separate:

1.  whether VM must be exchanged;
2.  how much collateral is contractually exchanged under the CSA;
3.  how collateral is recognized for CCR/capital purposes;
4.  what the collateral is economically worth in the contractual
    unwind/liquidation state.

Do not import regulatory supervisory haircuts into economic event-state
collateral valuation unless there is a specific economic reason.

Do not infer a regulatory VM exemption merely from the vehicle
structure. Keep any unresolved exemption question **\[TO CONFIRM\]**.

------------------------------------------------------------------------

## 22. Final open-items table

At the end, include a compact table titled **"Open Items Before
Production Implementation"** containing only genuinely unresolved
matters, particularly:

1.  exact production mapping from contractual Original Collateral
    Default to modeled sovereign (`\tau`{=tex});
2.  production model used to generate future pre-default (B_t);
3.  joint treatment of bond yield/spread and sovereign intensity if
    modeled separately;
4.  exact calculation/approximation of (ETA\_`\tau`{=tex});
5.  exact meaning and use of **ESTR + 212 bp** in the unwind/default
    state;
6.  how CSA collateral already reflected in contractual ETA is
    reconciled with the engine's separate collateral subtraction;
7.  exact event-state restructuring/liquidation valuation convention for
    (B\_`\tau`{=tex}\^{def/restr}) and (B\_`\tau`{=tex}\^{liq});
8.  treatment of the 30-business-day liquidation period versus engine
    MPOR;
9.  whether posted quantity or collateral value freezes during MPOR in
    production;
10. justification/calibration of (R\_{`\rm eff`{=tex}});
11. justification of residual (LGD\_{SPV});
12. any transaction-specific waterfall details required to map Available
    Proceeds into Dealer recovery;
13. exact role of any default-state curve such as ESTR + 212 and whether
    different unwind cashflow legs require different valuation
    treatment;
14. whether the collateral-driving input is generated independently from
    the exposure input and, if so, its exact production semantics.

Do not keep items in this table once they have been established by
documentation or code inspection.

------------------------------------------------------------------------

## 23. Deliverable and acceptance test

Produce a **complete revised draft**, not merely comments on the old
draft.

Preserve useful existing material where it remains correct, but freely
rewrite Sections 1--10 where necessary.

The revised paper should allow a quant reviewer to answer, without
guessing:

\[ `\boxed{
\begin{array}{l}
\text{What event are we modeling?}\\
\text{Why does it contractually unwind this SPIRE Series?}\\
\text{What determines }V_t\text{ before the event?}\\
\text{What determines future }B_t\text{ and how is sovereign credit represented?}\\
\text{How much bond collateral is actually posted?}\\
\text{What exactly is }V_\tau^{uw}/ETA_\tau?\\
\text{What exactly is the posted bond worth after the event?}\\
\text{How does restructuring/default value become liquidation realization?}\\
\text{How are those resources distributed through the SPIRE waterfall?}\\
\text{What amount can Dealer actually lose?}\\
\text{How is that loss mapped into the existing CVA engine without double counting?}
\end{array}}`{=tex} \]

If any one of those questions remains answered only by an unstated
assumption, the revision is not complete.

The conceptual center of the revised note should be:

\[ `\boxed{
(B_t,V_t,N_t^{post})
\rightarrow
(ETA_\tau,B_\tau^{def/restr},B_\tau^{liq})
\rightarrow
\text{Available Proceeds / waterfall}
\rightarrow
L_\tau^{Dealer}
\rightarrow
CVA.
}`{=tex} \]

Do **not** organize the methodology primarily around "what haircut
should be applied to the bond." Haircuts, recovery conventions,
ESTR+212, Delivery Cap, MPOR and residual LGD are components of the
state-transition/loss model, not substitutes for it.
