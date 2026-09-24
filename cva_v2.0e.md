Please perform a focused consistency revision of Section 6 and all later references to recovery/liquidation haircuts.

The current draft inconsistently uses the same R_{\rm eff} in M0 and M1 despite different recovery bases, then switches to R_{\rm liq} in M2, while later sections separately discuss a disposal haircut. Please remove this ambiguity.

Establish the conceptual decomposition

> \text{recovery base}
> \xrightarrow{R^{rec}}
> B_\tau^{def}
> \xrightarrow{1-h_\tau^{disp}}
> B_\tau^{liq}.
>

Here R^{rec} represents sovereign default/restructuring recovery under the relevant convention (RFV, RT or RMV), while h^{disp} represents only an incremental SPIRE-specific liquidation/disposal effect.

Accordingly:

> \begin{aligned}
> \text{M0:}\quad&
> B_\tau^{def}=R_{\rm FV}IR_\tau N,\\
> \text{M1:}\quad&
> B_\tau^{def}=R_{\rm MV}B_\tau^{proxy},
> \quad
> B_\tau^{proxy}=PV_\tau^{IL}(RFR+s^*),\\
> \text{M2:}\quad&
> B_\tau^{def}=R_{\rm MV}B_{\tau^-}^{risky}.
> \end{aligned}
>

In each case,

> B_\tau^{liq}=(1-h_\tau^{disp})B_\tau^{def}.
>

However, where calibration directly targets ultimate realized proceeds, allow the two stages to be collapsed into an all-in effective realization factor. If doing so, do not use the same undifferentiated R_{\rm eff} across recovery bases. Make the denominator explicit, e.g.

> R_{\rm eff}^{FV}=\frac{B_\tau^{liq}}{IR_\tau N},
> \qquad
> R_{\rm eff}^{MV}=\frac{B_\tau^{liq}}{B_\tau^{proxy}\text{ or }B_{\tau^-}^{risky}}.
>

Do not apply an additional h^{disp} where distressed transaction/restructuring-package/defaulted-security data used to calibrate the effective recovery already embed the relevant disposal conditions. A separate haircut is justified only for an incremental SPIRE-specific forced-sale effect.

Then review all subsequent sections/open items and eliminate duplicate or inconsistent recovery/liquidation multipliers. Keep the treatment concise; one clear decomposition plus one anti-double-counting statement is preferable to repeated caveats.
