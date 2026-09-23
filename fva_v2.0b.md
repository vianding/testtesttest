Please make one further targeted refinement to Section 6 based on an additional
transaction-specific fact.

This instruction SUPPLEMENTS the previous correction. Do NOT undo the previous
correction establishing r_base as the starting valuation benchmark and the
classic cash-posting FVA case as the conceptual anchor.

The additional fact is:

The dealer is not required to post one generic G10 bond. The collateral
agreement permits an eligible set of G10 government bonds, and the contractual
CSA haircut varies by characteristics such as rating and remaining maturity.

Therefore the liability-side non-cash treatment contains a
cheapest-to-deliver (CTD) collateral choice.

Please refine Section 6 accordingly.

============================================================
1. REPLACE THE SINGLE GENERIC G10 COLLATERAL ASSUMPTION
============================================================

Where Section 6 currently refers to one generic:

    q_CSA^G10

replace that simplification with an eligible collateral set:

    i ∈ E_t

where E_t is the set of G10 securities eligible under the collateral agreement.

For each eligible bond i, define:

    h_i^CSA = contractual CSA haircut for bond i

and

    q_i^CSA = 1 - h_i^CSA.

These haircuts are determined by the SWAP/COLLATERAL AGREEMENT and may vary,
for example, with:

- credit rating;
- remaining maturity;
- security type or other contractual eligibility buckets.

Make very clear that these are CONTRACTUAL CSA haircuts.

They are NOT regulatory haircuts and q_reg should NOT be reintroduced.

============================================================
2. REQUIRED BOND AMOUNT IS INSTRUMENT-SPECIFIC
============================================================

Continue to define dealer liability exposure as:

    L_t = (-V_t)^+.

If the dealer satisfies the collateral call entirely using eligible bond i,
the required market value of bond i is:

    B_i,t^post
        = L_t / q_i^CSA
        = L_t / (1 - h_i^CSA).

Therefore different eligible G10 bonds require different market values to
satisfy the same collateral call.

The CSA haircut is therefore economically relevant to the dealer's collateral
choice even though it is not itself a funding spread.

============================================================
3. INTRODUCE THE CTD COLLATERAL CHOICE
============================================================

The dealer should not be assumed to post an arbitrary representative G10 bond.

If multiple eligible securities can satisfy the same collateral obligation,
the economically relevant bond is determined by the dealer's marginal cost of
delivering each eligible security.

For bond i, let:

    c_i,t^post

denote the marginal economic cost per unit market value of making that bond
available for posting.

Depending on the dealer's collateral-management framework, this may reflect:

- opportunity cost of using an owned bond;
- lost repo/funding value;
- repo specialness;
- repo haircut / advance rate;
- securities borrowing or sourcing cost;
- inventory availability;
- internal Treasury collateral transfer pricing.

Do not assume all of these are required in the current base implementation.
They simply identify what may determine the economic posting cost.

Because bond i requires:

    B_i,t^post = L_t / q_i^CSA,

the economic cost of satisfying the collateral call depends jointly on:

    (a) the contractual CSA haircut; and
    (b) the marginal economic cost of the security.

Schematically, if c_i^post is expressed per unit bond market value:

    PostingCost_i,t
        = B_i,t^post c_i,t^post
        = L_t c_i,t^post / q_i^CSA.

The CTD security is therefore:

    i_t*
      = argmin_{i ∈ E_t}
          [ c_i,t^post / q_i^CSA ]

subject to eligibility, inventory and operational constraints.

Present this as a conceptual CTD condition rather than a fully specified
production collateral optimizer unless the existing methodology already
provides the required inputs.

============================================================
4. IMPORTANT: LOWEST CSA HAIRCUT IS NOT NECESSARILY CTD
============================================================

Explicitly state this point.

The bond with the smallest CSA haircut is not necessarily the economically
cheapest bond to deliver.

A smaller haircut reduces the market value of bonds required to satisfy the
collateral call:

    B_i^post = L / q_i^CSA.

But another eligible bond with a larger haircut may nevertheless be cheaper
to deliver if its opportunity/sourcing cost is sufficiently lower.

Therefore CTD depends on the combined effect of:

    contractual haircut
        ×
    collateral-specific funding / opportunity economics.

This is why the G10 liability-side problem should not be represented by one
generic q_CSA or one generic repo rate without justification.

============================================================
5. PRESERVE r_base AS THE VA BENCHMARK
============================================================

Do NOT allow the CTD refinement to obscure the correction made previously.

The liability-side VA remains conceptually:

    base valuation economics
        versus
    actual cost of satisfying the collateral-posting obligation.

The classic cash benchmark remains:

    VA_L^cash
      = ∫ E[
          DF_t L_t (r_base,t - r_CoF,t)
        ] dt.

For non-cash collateral, it is acceptable at this stage to summarize the
optimized G10 posting economics through an effective CTD posting rate/cost,
for example:

    r_post,t^CTD,

so that schematically:

    VA_L^G10
      = ∫ E[
          DF_t L_t
          (r_base,t - r_post,t^CTD)
        ] dt.

The CTD optimization determines the actual/effective posting economics;
it does NOT replace r_base as the valuation benchmark.

Do not force r_post^CTD into a specific closed-form function of r_repo and
r_CoF unless that mapping is supported by the actual Treasury/collateral
management methodology.

============================================================
6. KEEP THE CURRENT-TRADE MATERIALITY CONCLUSION
============================================================

Preserve the previous discussion that this machinery only matters when:

    L_t = (-V_t)^+ > 0.

The relevant diagnostic remains:

    ENE_t = E[(-V_t)^+].

If ENE is negligible for the current transaction, then the dealer rarely or
never needs to exercise the G10 collateral-posting/CTD choice and:

    VA_L ≈ 0.

Therefore we should recognize the CTD economics correctly in the generic
methodology without over-engineering a collateral optimization framework for
a liability-side exposure that may be immaterial in this particular trade.

============================================================
7. KEEP SECTION 6 COMPACT
============================================================

This is a refinement, not an expansion of scope.

The desired logical sequence for Section 6 is now:

    L_t = (-V_t)^+
            ↓
    classic cash-posting FVA benchmark
            ↓
    eligible G10 collateral set E_t
            ↓
    bond-specific contractual CSA haircuts h_i^CSA
            ↓
    required quantity/value B_i^post = L/q_i^CSA
            ↓
    bond-specific marginal posting economics
            ↓
    CTD selection
            ↓
    effective G10 posting cost relative to r_base
            ↓
    liability-side VA

Then close with:

    for the current transaction,
    if ENE ≈ 0,
    this liability-side/CTD optionality is economically immaterial.

Please modify only the portions of Section 6 needed to incorporate this
refinement. Do not reopen the asset-side methodology or other sections of the
paper.
