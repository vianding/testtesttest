# First-Principles FVA / ColVA Framework for Non-Cash VM

## 1. Setup

Consider a derivative with positive bank exposure (V_t\>0). The
counterparty posts a bond as variation margin (VM).

Define:

-   (V_t): derivative exposure / cash-equivalent collateral requirement.
-   (h\_{`\mathrm{CSA}`{=tex}}): contractual CSA haircut.
-   (h\_{`\mathrm{reg}`{=tex}}): applicable regulatory minimum haircut.
-   (h\_{`\mathrm{repo}`{=tex}}): haircut at which Treasury can monetize
    the bond in repo.
-   (q_i=1-h_i): corresponding valuation percentage.
-   (B_t): actual market value of bond received, after applying any
    contractual collateral cap.
-   (r\_{`\mathrm{repo}`{=tex},t}): repo funding rate for the bond.
-   (r\_{`\mathrm{CoF}`{=tex},t}): marginal unsecured / unsecuritized
    funding rate.
-   (r\_{`\mathrm{base}`{=tex},t}): cash/CSA discounting or funding rate
    already embedded in the base valuation.
-   (DF_t): base-valuation discount factor.

Assume the VM is legally rehypothecatable, regulation permits reuse, and
Treasury can operationally monetize it.

------------------------------------------------------------------------

## 2. How Much Bond Is Posted?

Absent a cap, the CSA requires

\[ B_t\^{`\mathrm{CSA}`{=tex}}=`\frac{V_t}{q_{\mathrm{CSA}}}`{=tex}. \]

For example, a 10% CSA haircut gives

\[ B_t\^{`\mathrm{CSA}`{=tex}}=`\frac{V_t}{0.90}`{=tex}. \]

If a contractual cap limits delivery, (B_t) is the **actual** bond
market value received and may be below (B_t\^{`\mathrm{CSA}`{=tex}}).

The CSA haircut determines how much collateral the client is
contractually asked to post. It does **not** by itself determine the
bond's actual funding value.

------------------------------------------------------------------------

## 3. Two Constraints on Usable Collateral

The received bond must pass two distinct tests.

### Regulatory recognition

The regulator recognizes

\[ C\_{`\mathrm{reg}`{=tex},t}=q\_{`\mathrm{reg}`{=tex}}B_t. \]

For a 4% regulatory haircut,

\[ C\_{`\mathrm{reg}`{=tex},t}=0.96B_t. \]

### Actual monetization

If Treasury repos the bond,

\[ C\_{`\mathrm{repo}`{=tex},t}=q\_{`\mathrm{repo}`{=tex}}B_t. \]

For a 2% repo haircut,

\[ C\_{`\mathrm{repo}`{=tex},t}=0.98B_t. \]

Therefore the amount that can support the exposure without requiring
unsecuritized funding is

\[ C\_{`\mathrm{usable}`{=tex},t} =
`\min`{=tex}`\left`{=tex}(V_t,;q\_{`\mathrm{reg}`{=tex}}B_t,;q\_{`\mathrm{repo}`{=tex}}B_t`\right`{=tex}).
\]

The unsecuritized funding requirement is

\[ `\boxed{
U_t=
\left[
V_t-\min(q_{\mathrm{reg}}B_t,q_{\mathrm{repo}}B_t)
\right]^+
}`{=tex} \]

and the break-even bond amount is

\[ `\boxed{
B_t^*
=
\frac{V_t}{\min(q_{\mathrm{reg}},q_{\mathrm{repo}})}
}`{=tex}. \]

Example: if (q\_{`\mathrm{reg}`{=tex}}=0.96) and
(q\_{`\mathrm{repo}`{=tex}}=0.98), regulation binds and

\[ B_t\^\*=`\frac{V_t}{0.96}`{=tex}. \]

Below this amount, unsecuritized funding is required.

------------------------------------------------------------------------

## 4. Contractual Excess vs Funding Shortfall

Suppose

\[ q\_{`\mathrm{CSA}`{=tex}}=0.90,`\qquad`{=tex}
q\_{`\mathrm{reg}`{=tex}}=0.96,`\qquad`{=tex}
q\_{`\mathrm{repo}`{=tex}}=0.98. \]

The normal CSA call is

\[ `\frac{V_t}{0.90}`{=tex}, \]

while the funding break-even is

\[ `\frac{V_t}{0.96}`{=tex}. \]

Thus the contractual CSA creates a buffer

\[ `\boxed{
B_{\mathrm{buffer},t}
=
\frac{V_t}{0.90}-\frac{V_t}{0.96}
}`{=tex}. \]

A cap may consume this buffer without creating an unsecuritized funding
requirement.

For (V_t=100):

-   Full 10% CSA amount: (111.11)
-   Break-even amount: (104.17)
-   Buffer: (6.94)

Hence:

  -----------------------------------------------------------------------
  Actual bond (B_t)                   Interpretation
  ----------------------------------- -----------------------------------
  (B_t`\ge111.11`{=tex})              Full contractual CSA amount
                                      received

  (104.17`\le `{=tex}B_t\<111.11)     CSA buffer partly consumed; no
                                      unsecuritized funding needed

  (B_t\<104.17)                       Funding shortfall;
                                      (U_t=100-0.96B_t) in this example
  -----------------------------------------------------------------------

The labels "base collateral" and "free hold" are useful for describing
the contractual buffer, but if both portions are equally reusable and
monetizable, they are not fundamentally different assets from an FVA
perspective.

------------------------------------------------------------------------

## 5. First-Principles ColVA / FVA

The base valuation already embeds a funding/discounting assumption
(r\_{`\mathrm{base}`{=tex},t}). Therefore FVA/ColVA should measure the
**incremental difference between actual funding economics and the
economics already reflected in base PV**.

### Collateral-supported portion

For the amount (C\_{`\mathrm{usable}`{=tex},t}), funding is available
through repo at (r\_{`\mathrm{repo}`{=tex},t}).

Its adjustment is therefore driven by

\[ r\_{`\mathrm{repo}`{=tex},t}-r\_{`\mathrm{base}`{=tex},t}. \]

### Funding shortfall

For (U_t), the dealer must use marginal unsecuritized funding at
(r\_{`\mathrm{CoF}`{=tex},t}).

Its adjustment is driven by

\[ r\_{`\mathrm{CoF}`{=tex},t}-r\_{`\mathrm{base}`{=tex},t}. \]

A compact first-order representation is

\[ `\boxed{
\mathrm{FVA/ColVA}
=
\int_0^T
E_0\left[
\frac{
C_{\mathrm{usable},t}
(r_{\mathrm{repo},t}-r_{\mathrm{base},t})
+
U_t
(r_{\mathrm{CoF},t}-r_{\mathrm{base},t})
}{DF_t}
\right]dt
+
\mathrm{ExcessValue}
}`{=tex} \]

subject to the desk's sign convention.

This avoids double counting: the adjustment is a **spread to the base
valuation**, not the absolute repo or funding cost.

------------------------------------------------------------------------

## 6. Genuine Excess Collateral

If

\[ q\_{`\mathrm{reg}`{=tex}}B_t\>V_t
`\quad`{=tex}`\text{and}`{=tex}`\quad`{=tex}
q\_{`\mathrm{repo}`{=tex}}B_t\>V_t, \]

the bank has received more monetizable collateral than is required to
support this derivative.

Define the economically monetizable excess, for example, as

\[ E_t= `\left[
\min(q_{\mathrm{reg}}B_t,q_{\mathrm{repo}}B_t)-V_t
\right]`{=tex}\^+. \]

If this excess can be deployed elsewhere, it may have additional funding
value. The appropriate rate depends on the bank's marginal use of that
liquidity and should be modeled explicitly rather than automatically
treating all excess as unsecured-rate benefit.

------------------------------------------------------------------------

## 7. Interpretation of the Base Rate

The key modeling question is what (r\_{`\mathrm{base}`{=tex}})
represents.

If the base trade is discounted/funded under a CSA convention such as

\[ r\_{`\mathrm{base}`{=tex}}=`\mathrm{€STR}`{=tex}+5`\text{ bp}`{=tex}
\]

(or €STR + 10 bp), then:

-   repo-supported collateral contributes a spread approximately
    (r\_{`\mathrm{repo}`{=tex}}-r\_{`\mathrm{base}`{=tex}});
-   an unsecuritized shortfall contributes approximately
    (r\_{`\mathrm{CoF}`{=tex}}-r\_{`\mathrm{base}`{=tex}}).

Before implementing ColVA, confirm whether the €STR + 5/10 bp term is
already embedded in the base PV or is itself a separate valuation
adjustment. It must not be counted twice.

------------------------------------------------------------------------

## 8. Core Takeaway

Keep the problem in four steps:

1.  **CSA:** How much bond is actually posted?\
    \[ B_t `\leftarrow `{=tex}h\_{`\mathrm{CSA}`{=tex}}
    `\text{ and collateral cap}`{=tex} \]

2.  **Regulation:** How much of that bond counts as collateral?\
    \[ q\_{`\mathrm{reg}`{=tex}}B_t \]

3.  **Treasury/repo:** How much cash can the bond actually raise?\
    \[ q\_{`\mathrm{repo}`{=tex}}B_t \]

4.  **FVA/ColVA:** What is the incremental funding cost relative to base
    valuation?\
    \[ r\_{`\mathrm{repo}`{=tex}}-r\_{`\mathrm{base}`{=tex}}
    `\quad`{=tex}`\text{or}`{=tex}`\quad`{=tex}
    r\_{`\mathrm{CoF}`{=tex}}-r\_{`\mathrm{base}`{=tex}}. \]

The binding constraint between regulatory recognition and repo
monetization determines when the position moves from
collateral-supported funding into unsecuritized funding.
