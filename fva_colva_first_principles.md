# First-Principles FVA / ColVA Framework for Non-Cash VM

## 1. Setup

Consider a derivative with positive bank exposure (V_t\>0), where the
counterparty posts a bond as variation margin (VM).

Define:

-   (V_t): derivative exposure / cash-equivalent collateral requirement.
-   (h\_{`\mathrm{CSA}`{=tex}}): contractual CSA haircut;
    (q\_{`\mathrm{CSA}`{=tex}}=1-h\_{`\mathrm{CSA}`{=tex}}).
-   (h\_{`\mathrm{reg}`{=tex}}): applicable regulatory minimum haircut;
    (q\_{`\mathrm{reg}`{=tex}}=1-h\_{`\mathrm{reg}`{=tex}}).
-   (h\_{`\mathrm{repo}`{=tex}}): repo-market haircut;
    (q\_{`\mathrm{repo}`{=tex}}=1-h\_{`\mathrm{repo}`{=tex}}).
-   (B_t): actual market value of bond received, after any collateral
    cap.
-   (r\_{`\mathrm{repo}`{=tex},t}): repo funding rate for the bond.
-   (r\_{`\mathrm{CoF}`{=tex},t}): marginal unsecured / unsecuritized
    funding rate.
-   (r\_{`\mathrm{base}`{=tex},t}): cash/CSA discounting or funding rate
    already embedded in base valuation.
-   (DF_t): base-valuation discount factor.

Assume VM is legally rehypothecatable, regulatory rules permit reuse,
and Treasury can operationally monetize the bond.

------------------------------------------------------------------------

## 2. How Much Bond Is Posted?

Absent a cap, the CSA requires

\[ B_t\^{`\mathrm{CSA}`{=tex}}=`\frac{V_t}{q_{\mathrm{CSA}}}`{=tex}. \]

For a 10% CSA haircut:

\[ B_t\^{`\mathrm{CSA}`{=tex}}=`\frac{V_t}{0.90}`{=tex}. \]

If a contractual cap limits delivery, (B_t) is the **actual** bond
market value received and can be below this amount.

The CSA haircut determines how much collateral the client is
contractually required to post. It does not determine the bond's funding
value.

------------------------------------------------------------------------

## 3. Regulatory Coverage Determines the Funding Shortfall

Regulatory recognition of the received bond is

\[ C\_{`\mathrm{reg}`{=tex},t}=q\_{`\mathrm{reg}`{=tex}}B_t. \]

For a 4% regulatory haircut:

\[ C\_{`\mathrm{reg}`{=tex},t}=0.96B_t. \]

The regulatory break-even bond amount is therefore

\[ `\boxed{
B_t^*=\frac{V_t}{q_{\mathrm{reg}}}
}`{=tex} \]

and the amount requiring unsecuritized funding is

\[ `\boxed{
U_t=(V_t-q_{\mathrm{reg}}B_t)^+.
}`{=tex} \]

Thus:

-   if (q\_{`\mathrm{reg}`{=tex}}B_t`\ge `{=tex}V_t), no unsecuritized
    funding shortfall exists;
-   if (q\_{`\mathrm{reg}`{=tex}}B_t\<V_t), the deficit (U_t) is funded
    at (r\_{`\mathrm{CoF}`{=tex},t}).

**Important:** the repo haircut is not a second threshold for
determining (U_t). It affects the funding economics of the bond that has
been received, not whether the regulatory collateral requirement has
been met.

------------------------------------------------------------------------

## 4. CSA Buffer and Collateral Cap

Suppose

\[
q\_{`\mathrm{CSA}`{=tex}}=0.90,`\qquad `{=tex}q\_{`\mathrm{reg}`{=tex}}=0.96.
\]

The full contractual collateral amount is

\[ `\frac{V_t}{0.90}`{=tex}, \]

while the regulatory break-even amount is

\[ `\frac{V_t}{0.96}`{=tex}. \]

The contractual buffer is

\[ `\boxed{
B_{\mathrm{buffer},t}
=
\frac{V_t}{0.90}-\frac{V_t}{0.96}.
}`{=tex} \]

For (V_t=100):

-   Full 10% CSA amount: (111.11)
-   Regulatory break-even: (104.17)
-   Buffer: (6.94)

Hence:

  -----------------------------------------------------------------------
  Actual bond (B_t)                   Interpretation
  ----------------------------------- -----------------------------------
  (B_t`\ge111.11`{=tex})              Full contractual CSA amount
                                      received

  (104.17`\le `{=tex}B_t\<111.11)     CSA buffer consumed partly; still
                                      no unsecuritized funding shortfall

  (B_t\<104.17)                       Regulatory collateral deficit;
                                      (U_t=100-0.96B_t)
  -----------------------------------------------------------------------

The decomposition into "base collateral" and "free hold" can describe
this buffer, but both pieces are the same reusable bond inventory if
they have identical legal and operational treatment.

------------------------------------------------------------------------

## 5. Repo Monetization Is Separate

The bond can be used as a secured funding asset.

Its repo **funding capacity** is

\[ `\boxed{
F_t^{\mathrm{repo}}=q_{\mathrm{repo}}B_t.
}`{=tex} \]

For example, a 2% repo haircut gives

\[ F_t\^{`\mathrm{repo}`{=tex}}=0.98B_t. \]

This tells us how much cash Treasury can raise against the received
bond.

It does **not** redefine the regulatory shortfall:

\[ U_t`\ne`{=tex}(V_t-q\_{`\mathrm{repo}`{=tex}}B_t)\^+. \]

For example, if

\[ B_t=`\frac{100}{0.96}`{=tex}=104.17 \]

and (q\_{`\mathrm{repo}`{=tex}}=0.98), then:

\[ q\_{`\mathrm{reg}`{=tex}}B_t=100 \]

so there is no regulatory/unsecuritized funding shortfall, while

\[ q\_{`\mathrm{repo}`{=tex}}B_t=102.08 \]

is the bond's repo funding capacity.

These are different concepts.

------------------------------------------------------------------------

## 6. What Is the Bond's Funding Value?

Distinguish **funding capacity** from **funding value**.

### Funding capacity

\[ F_t\^{`\mathrm{repo}`{=tex}}=q\_{`\mathrm{repo}`{=tex}}B_t. \]

This is the cash that can be raised through repo.

### Funding value

The economic value comes from obtaining funding at the repo rate rather
than at the funding/discounting rate already embedded in base valuation.

A first-order instantaneous contribution is

\[ `\boxed{
F_t^{\mathrm{repo}}
\left(r_{\mathrm{base},t}-r_{\mathrm{repo},t}\right)
}`{=tex} \]

for a "benefit-positive" convention.

Equivalently, under a ColVA-cost convention, the adjustment is

\[ F_t\^{`\mathrm{repo}`{=tex}}
`\left`{=tex}(r\_{`\mathrm{repo}`{=tex},t}-r\_{`\mathrm{base}`{=tex},t}`\right`{=tex}).
\]

Thus the repo haircut affects **how much funding value the bond
provides**, while the repo rate determines the cost of obtaining that
funding.

------------------------------------------------------------------------

## 7. First-Principles ColVA / FVA

Base PV already embeds (r\_{`\mathrm{base}`{=tex},t}). Therefore
ColVA/FVA should measure the incremental difference between actual
funding economics and that base assumption.

### Repo-supported bond

The received reusable bond has repo funding capacity

\[ F_t\^{`\mathrm{repo}`{=tex}}=q\_{`\mathrm{repo}`{=tex}}B_t. \]

Its ColVA contribution is approximately

\[ `\boxed{
\mathrm{ColVA}_{\mathrm{repo}}
=
\int_0^T
E_0\left[
\frac{
F_t^{\mathrm{repo}}
(r_{\mathrm{repo},t}-r_{\mathrm{base},t})
}{DF_t}
\right]dt.
}`{=tex} \]

### Regulatory collateral shortfall

Define

\[ U_t=(V_t-q\_{`\mathrm{reg}`{=tex}}B_t)\^+. \]

This portion must be funded at the marginal unsecuritized funding rate:

\[ `\boxed{
\mathrm{ColVA}_{\mathrm{shortfall}}
=
\int_0^T
E_0\left[
\frac{
U_t(r_{\mathrm{CoF},t}-r_{\mathrm{base},t})
}{DF_t}
\right]dt.
}`{=tex} \]

Subject to the desk's sign convention,

\[ `\boxed{
\mathrm{ColVA/FVA}
\approx
\mathrm{ColVA}_{\mathrm{repo}}
+
\mathrm{ColVA}_{\mathrm{shortfall}}
+
\mathrm{ExcessValue}.
}`{=tex} \]

This is a spread adjustment to base valuation, not an absolute repo or
funding charge.

------------------------------------------------------------------------

## 8. Genuine Excess / Free-Hold Collateral

If the actual CSA requires more bond than the regulatory minimum,

\[ B_t\>`\frac{V_t}{q_{\mathrm{reg}}}`{=tex}, \]

the difference

\[ `\boxed{
B_t^{\mathrm{excess}}
=
\left(B_t-\frac{V_t}{q_{\mathrm{reg}}}\right)^+
}`{=tex} \]

is a useful definition of contractual excess/free hold.

If this bond is equally rehypothecatable and repoable, it is
operationally part of the same collateral inventory and also has funding
value.

Whether its economic benefit should be valued at the same marginal rate
as the collateral supporting the trade depends on how Treasury can
deploy the resulting liquidity. This should be an explicit modeling
assumption rather than inferred solely from the regulatory haircut.

------------------------------------------------------------------------

## 9. Interpretation of the Base Rate

The central modeling question is what (r\_{`\mathrm{base}`{=tex}})
represents.

If the base trade is valued using a CSA funding/discounting convention
such as

\[ r\_{`\mathrm{base}`{=tex}}=`\mathrm{€STR}`{=tex}+5`\text{ bp}`{=tex}
\]

(or €STR + 10 bp), then:

-   repo funding is valued relative to (r\_{`\mathrm{base}`{=tex}}): \[
    r\_{`\mathrm{repo}`{=tex}}-r\_{`\mathrm{base}`{=tex}}; \]
-   an unsecuritized funding deficit is valued relative to
    (r\_{`\mathrm{base}`{=tex}}): \[
    r\_{`\mathrm{CoF}`{=tex}}-r\_{`\mathrm{base}`{=tex}}. \]

Before implementation, confirm whether the €STR + 5/10 bp term is
already embedded in base PV or is a separate CSA-funding adjustment. It
must not be counted twice.

------------------------------------------------------------------------

## 10. Core Framework

Keep four questions separate:

1.  **CSA --- how much bond does the client actually post?** \[ B_t
    `\leftarrow `{=tex}h\_{`\mathrm{CSA}`{=tex}}`\text{ and collateral cap}`{=tex}.
    \]

2.  **Regulation --- is there a collateral deficit requiring
    unsecuritized funding?** \[
    U_t=(V_t-q\_{`\mathrm{reg}`{=tex}}B_t)\^+. \]

3.  **Repo/Treasury --- what funding capacity and value does the
    received bond provide?** \[
    F_t\^{`\mathrm{repo}`{=tex}}=q\_{`\mathrm{repo}`{=tex}}B_t,
    `\qquad`{=tex}
    `\text{value}`{=tex}`\sim `{=tex}F_t\^{`\mathrm{repo}`{=tex}}(r\_{`\mathrm{base}`{=tex}}-r\_{`\mathrm{repo}`{=tex}}).
    \]

4.  **FVA/ColVA --- what incremental adjustment is required relative to
    base valuation?** \[
    r\_{`\mathrm{repo}`{=tex}}-r\_{`\mathrm{base}`{=tex}}
    `\quad`{=tex}`\text{for repo-supported funding,}`{=tex} \] \[
    r\_{`\mathrm{CoF}`{=tex}}-r\_{`\mathrm{base}`{=tex}}
    `\quad`{=tex}`\text{for true funding shortfall.}`{=tex} \]

**Key distinction:** regulatory haircut determines the shortfall
threshold; repo haircut determines the funding capacity/value of the
bond. They should not be collapsed into a single threshold.
