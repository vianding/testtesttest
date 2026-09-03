# First-Principles FVA / ColVA for Non-Cash VM

## 1. Setup

Consider a derivative with positive bank exposure \(V_t>0\), collateralized by a bond posted as variation margin (VM).

Define:

- \(V_t\): derivative exposure / cash-equivalent collateral requirement.
- \(h_{\mathrm{CSA}}\): contractual CSA haircut; \(q_{\mathrm{CSA}}=1-h_{\mathrm{CSA}}\).
- \(h_{\mathrm{reg}}\): regulatory haircut; \(q_{\mathrm{reg}}=1-h_{\mathrm{reg}}\).
- \(h_{\mathrm{repo}}\): repo-market haircut; \(q_{\mathrm{repo}}=1-h_{\mathrm{repo}}\).
- \(B_t\): actual market value of bond received, after any contractual collateral cap.
- \(r_{\mathrm{repo},t}\): repo funding rate.
- \(r_{\mathrm{CoF},t}\): marginal unsecured / unsecuritized funding rate.
- \(r_{\mathrm{base},t}\): cash/CSA discounting or funding rate already embedded in base valuation.
- \(DF_t=P(0,t)\): discount factor from \(t\) to today.

Assume the VM is legally rehypothecatable, regulation permits reuse, and Treasury can operationally monetize the bond.

---

## 2. How Much Bond Is Posted?

Absent a cap, the CSA requires

\[
B_t^{\mathrm{CSA}}=\frac{V_t}{q_{\mathrm{CSA}}}.
\]

For a 10% CSA haircut,

\[
B_t^{\mathrm{CSA}}=\frac{V_t}{0.90}.
\]

If a contractual cap limits delivery, \(B_t\) is the **actual** bond market value received and may be below the full CSA amount.

The CSA haircut determines how much bond the client is contractually required to post. It does **not** determine the bond's regulatory recognition or repo funding value.

---

## 3. Regulatory Coverage Determines the True Funding Shortfall

Regulatory recognition is

\[
C_{\mathrm{reg},t}=q_{\mathrm{reg}}B_t.
\]

For a 4% regulatory haircut,

\[
C_{\mathrm{reg},t}=0.96B_t.
\]

The regulatory break-even amount is

\[
\boxed{
B_t^*=\frac{V_t}{q_{\mathrm{reg}}}
}
\]

and the true amount requiring unsecuritized funding is

\[
\boxed{
U_t=(V_t-q_{\mathrm{reg}}B_t)^+.
}
\]

Hence:

- if \(q_{\mathrm{reg}}B_t\ge V_t\), there is no true unsecuritized funding shortfall;
- if \(q_{\mathrm{reg}}B_t<V_t\), the shortfall \(U_t\) is funded at \(r_{\mathrm{CoF},t}\).

**Repo haircut does not determine whether \(U_t\) exists.**

---

## 4. Repo Monetization and Bond Funding Value

The received bond can be monetized in repo. Its repo funding capacity is

\[
\boxed{
F_t^{\mathrm{repo}}=q_{\mathrm{repo}}B_t.
}
\]

For a 2% repo haircut,

\[
F_t^{\mathrm{repo}}=0.98B_t.
\]

This is the amount of cash Treasury can raise against the bond.

The repo haircut and regulatory haircut are independent quantities. There is no universal ordering:

\[
h_{\mathrm{repo}}\lessgtr h_{\mathrm{reg}}.
\]

For liquid sovereign collateral, \(h_{\mathrm{repo}}<h_{\mathrm{reg}}\) may be common, but the framework should not assume it.

### Bond funding value

Funding capacity is not itself the economic value. The funding value comes from the spread between repo funding and the alternative/base funding assumption.

Under a benefit-positive convention,

\[
\text{Funding benefit rate}
\approx
F_t^{\mathrm{repo}}
\left(r_{\mathrm{base},t}-r_{\mathrm{repo},t}\right).
\]

Under a cost-positive ColVA convention, the same effect is written

\[
F_t^{\mathrm{repo}}
\left(r_{\mathrm{repo},t}-r_{\mathrm{base},t}\right).
\]

---

## 5. CSA Buffer and Three Economic Regimes

Suppose

\[
q_{\mathrm{CSA}}=0.90,\qquad q_{\mathrm{reg}}=0.96.
\]

The full CSA amount is

\[
\frac{V_t}{0.90},
\]

while the regulatory break-even amount is

\[
\frac{V_t}{0.96}.
\]

The contractual buffer is therefore

\[
\boxed{
B_{\mathrm{buffer},t}
=
\frac{V_t}{0.90}
-
\frac{V_t}{0.96}.
}
\]

For \(V_t=100\):

\[
B^{\mathrm{CSA}}=111.11,\qquad
B^*=104.17,\qquad
B_{\mathrm{buffer}}=6.94.
\]

### Case 1: Full CSA / genuine excess collateral

\[
B_t\ge \frac{V_t}{q_{\mathrm{CSA}}}.
\]

The full contractual amount is received. Relative to the regulatory minimum, genuine excess bond collateral is

\[
\boxed{
B_t^{\mathrm{excess}}
=
\left(
B_t-\frac{V_t}{q_{\mathrm{reg}}}
\right)^+.
}
\]

### Case 2: CSA buffer partly consumed, but still fully covered

\[
\frac{V_t}{q_{\mathrm{reg}}}
\le B_t
<
\frac{V_t}{q_{\mathrm{CSA}}}.
\]

The collateral cap has reduced the amount below the full CSA call, but regulatory coverage still satisfies the exposure:

\[
U_t=0.
\]

There is no unsecuritized funding shortfall.

### Case 3: Deficit regime

\[
B_t<\frac{V_t}{q_{\mathrm{reg}}}.
\]

Then

\[
\boxed{
U_t=V_t-q_{\mathrm{reg}}B_t>0.
}
\]

This amount must be funded at \(r_{\mathrm{CoF},t}\).

---

## 6. ColVA / FVA: First-Principles Decomposition

The base PV already embeds \(r_{\mathrm{base},t}\). Therefore ColVA/FVA should measure the **incremental funding economics relative to the base valuation**, not absolute funding costs.

### 6.1 Collateral-supported base exposure

Define the portion of the original exposure supported by regulatory collateral as

\[
\boxed{
C_t=\min(V_t,q_{\mathrm{reg}}B_t).
}
\]

This ensures

\[
\boxed{
C_t+U_t=V_t.
}
\]

The repo-supported ColVA contribution on this base exposure is

\[
\boxed{
\mathrm{ColVA}_{\mathrm{base}}
=
\int_0^T
E_0\!\left[
DF_t\,
C_t
\left(r_{\mathrm{repo},t}-r_{\mathrm{base},t}\right)
\right]dt.
}
\]

This avoids applying the base-exposure adjustment to more than the original \(V_t\).

### 6.2 Regulatory shortfall

\[
\boxed{
\mathrm{ColVA}_{\mathrm{shortfall}}
=
\int_0^T
E_0\!\left[
DF_t\,
U_t
\left(r_{\mathrm{CoF},t}-r_{\mathrm{base},t}\right)
\right]dt.
}
\]

where

\[
U_t=(V_t-q_{\mathrm{reg}}B_t)^+.
\]

### 6.3 Genuine excess collateral

The genuine excess bond amount is

\[
B_t^{\mathrm{excess}}
=
\left(
B_t-\frac{V_t}{q_{\mathrm{reg}}}
\right)^+.
\]

Its repo funding capacity is

\[
\boxed{
F_t^{\mathrm{excess}}
=
q_{\mathrm{repo}}B_t^{\mathrm{excess}}.
}
\]

If Treasury can deploy this excess repo cash to replace marginal unsecured funding elsewhere in the bank, a natural cost-positive adjustment is

\[
\boxed{
\mathrm{ColVA}_{\mathrm{excess}}
=
\int_0^T
E_0\!\left[
DF_t\,
F_t^{\mathrm{excess}}
\left(r_{\mathrm{repo},t}-r_{\mathrm{CoF},t}\right)
\right]dt.
}
\]

Since normally \(r_{\mathrm{repo}}<r_{\mathrm{CoF}}\), this term is typically negative: a funding benefit.

This term should be included only if the trade/XVA framework is intended to capture the Treasury value of excess reusable collateral. If that value is captured centrally through FTP or Treasury, including it again would double count.

### 6.4 Total

Subject to the desk's sign convention,

\[
\boxed{
\mathrm{ColVA/FVA}
\approx
\mathrm{ColVA}_{\mathrm{base}}
+
\mathrm{ColVA}_{\mathrm{shortfall}}
+
\mathrm{ColVA}_{\mathrm{excess}}.
}
\]

---

## 7. Repo Monetization Uplift Is Not Genuine Excess Collateral

If \(q_{\mathrm{repo}}>q_{\mathrm{reg}}\), then even in a deficit regime the bond may raise more repo cash than its regulatory recognized value:

\[
\boxed{
M_t
=
(q_{\mathrm{repo}}-q_{\mathrm{reg}})B_t.
}
\]

Example:

\[
B=90,\quad q_{\mathrm{reg}}=0.96,\quad q_{\mathrm{repo}}=0.98.
\]

Then

\[
q_{\mathrm{reg}}B=86.4,\qquad
q_{\mathrm{repo}}B=88.2,
\]

so

\[
M=1.8.
\]

This \(1.8\) is **repo monetization uplift**, not genuine excess collateral. The trade still has a regulatory shortfall

\[
U=100-86.4=13.6.
\]

Do not label \(M_t\) as "free hold" or excess collateral.

---

## 8. Worked Example: \(V=100\)

Use

\[
q_{\mathrm{CSA}}=0.90,\qquad
q_{\mathrm{reg}}=0.96,\qquad
q_{\mathrm{repo}}=0.98.
\]

### Case 1: Full CSA amount

\[
B=111.11.
\]

Regulatory coverage:

\[
0.96(111.11)=106.67>100.
\]

Hence

\[
C=100,\qquad U=0.
\]

Genuine excess bond:

\[
B^{\mathrm{excess}}
=
111.11-104.17
=
6.94.
\]

Excess repo funding capacity:

\[
F^{\mathrm{excess}}
=
0.98(6.94)
=
6.80.
\]

Adjustment:

\[
\boxed{
100(r_{\mathrm{repo}}-r_{\mathrm{base}})
+
6.80(r_{\mathrm{repo}}-r_{\mathrm{CoF}})
}
\]

if the value of excess reusable collateral is captured in this trade.

### Case 2: Cap consumes part of CSA buffer

Take

\[
B=107.
\]

Then

\[
0.96(107)=102.72>100.
\]

Thus

\[
C=100,\qquad U=0.
\]

Genuine excess bond relative to the regulatory minimum:

\[
B^{\mathrm{excess}}
=
107-104.17
=
2.83.
\]

Excess repo capacity:

\[
F^{\mathrm{excess}}
=
0.98(2.83)
\approx2.77.
\]

Adjustment:

\[
\boxed{
100(r_{\mathrm{repo}}-r_{\mathrm{base}})
+
2.77(r_{\mathrm{repo}}-r_{\mathrm{CoF}})
}
\]

if excess collateral value is recognized.

### Case 3: Regulatory deficit

Take

\[
B=90.
\]

Then

\[
C=0.96(90)=86.4,
\]

and

\[
U=100-86.4=13.6.
\]

There is no genuine excess collateral:

\[
B^{\mathrm{excess}}=0.
\]

Adjustment:

\[
\boxed{
86.4(r_{\mathrm{repo}}-r_{\mathrm{base}})
+
13.6(r_{\mathrm{CoF}}-r_{\mathrm{base}})
}
\]

with

\[
86.4+13.6=100.
\]

Although the bond can physically raise

\[
0.98(90)=88.2
\]

in repo, the additional

\[
88.2-86.4=1.8
\]

is repo monetization uplift relative to regulatory recognition, not a separate part of the \(100\) base funding requirement.

---

## 9. Interpretation of the Base Rate

The central modeling question is what \(r_{\mathrm{base}}\) represents.

If base valuation uses a CSA funding/discounting convention such as

\[
r_{\mathrm{base}}=\mathrm{€STR}+5\text{ bp}
\]

(or €STR + 10 bp), then:

- collateral-supported funding is adjusted by
  \[
  r_{\mathrm{repo}}-r_{\mathrm{base}};
  \]
- genuine unsecuritized shortfall is adjusted by
  \[
  r_{\mathrm{CoF}}-r_{\mathrm{base}};
  \]
- genuine excess reusable collateral, if valued here, is naturally worth approximately
  \[
  r_{\mathrm{repo}}-r_{\mathrm{CoF}}
  \]
  under a cost-positive convention.

Confirm whether the €STR + 5/10 bp term is already embedded in base PV or applied separately. It must not be counted twice.

---

## 10. Core Takeaway

Keep the following concepts separate:

1. **CSA haircut** determines how much bond the client is contractually asked to post.
2. **Regulatory haircut** determines whether there is a true collateral/funding shortfall:
   \[
   U_t=(V_t-q_{\mathrm{reg}}B_t)^+.
   \]
3. **Repo haircut** determines how much cash the received bond can raise:
   \[
   F_t^{\mathrm{repo}}=q_{\mathrm{repo}}B_t.
   \]
4. **Repo rate** determines the cost of secured monetization.
5. **Cost of funds** applies to the genuine unsecuritized shortfall.
6. **ColVA/FVA** measures each actual funding channel relative to the rate already embedded in base valuation.
7. **Genuine excess collateral** and **repo monetization uplift** are different quantities and should not be conflated.


## 11. Key References

1. **OSFI — Margin Requirements for Non-Centrally Cleared Derivatives (Guideline E-22)**  
   Regulatory framework for bilateral margin, including eligible collateral and the treatment of initial and variation margin.  
   https://www.osfi-bsif.gc.ca/en/guidance/guidance-library/margin-requirements-non-centrally-cleared-derivatives-guideline-2024

2. **OSFI — Capital Adequacy Requirements (CAR) 2026, Chapter 4: Credit Risk — Standardized Approach**  
   Source for the supervisory collateral haircut framework, including the sovereign-debt maturity buckets discussed in this note.  
   https://www.osfi-bsif.gc.ca/en/guidance/guidance-library/capital-adequacy-requirements-car-2026-chapter-4-credit-risk-standardized-approach

3. **ICMA — What is a haircut? (Repo FAQ)**  
   Reference for repo haircut mechanics and the relationship between collateral market value and repo cash proceeds.  
   https://www.icmagroup.org/market-practice-and-regulatory-policy/repo-and-collateral-markets/icma-ercc-publications/frequently-asked-questions-on-repo/21-what-is-a-haircut/

4. **ISDA — 2016 Credit Support Annex for Variation Margin (English Law)**  
   Reference for the contractual variation-margin CSA framework and collateral terms.  
   https://www.isda.org/book/2016-credit-support-annex-for-variation-margin-english-pdf/

> **Note:** The ColVA/FVA decomposition and formulas in this document are first-principles modeling derivations using these contractual, regulatory, and repo-market building blocks; they are not formulas prescribed directly by the references above.
