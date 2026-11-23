Task: MC Bond Valuation & Physical Collateral — Numerical Trace and Validation

Please prepare a concise, code-grounded technical note tracing how the current FFF Monte Carlo XVA implementation values a bond and applies its value to collateralized exposure.

Objective: Establish precisely what financial quantities the current implementation calculates, how they flow through the engine, and whether the numerical results are internally consistent.

This is an implementation validation task, not a Repack methodology discussion or architecture redesign.

1. Scope

Use the current Repack development branch, focusing on the implemented nominal bond valuation and physical collateral functionality.

Select a simple existing test case involving one nominal fixed-rate bond, one CSA and a portfolio exposure.

A. Bond pathwise valuation

For one observation date and one representative MC path:

* Identify the bond’s remaining cashflows and the discount factors used.
* Show how the run’s Funding Index and bond spread (s^*) enter valuation.
* Calculate the bond’s pathwise market value (MV).
* Calculate its face-value (FV) basis, including the outstanding principal.
* Identify applicable FX conversion, accrued-interest treatment, and numeraire conventions.

Provide the actual numbers and reconcile them to the relevant C++ valuation outputs.

B. Physical collateral recognition

Trace the same bond value through:

* CollateralAssetNettingSets::valuePerCSA
* TradeIndependentAmount::collateralAssetAmount
* aggregatePortfolioValuesAndCollateralCapping

Show explicitly:

* The bond value before collateral adjustment.
* How the collateral asset is excluded from ordinary portfolio exposure.
* How (R_{\mathrm{eff}}) is applied.
* How the adjusted collateral amount affects secured EPE/ENE.
* The exact sign, currency, and numeraire conventions.
* The effect of choosing MarketValue versus FaceValue.

Where possible, reproduce the final exposure calculation independently from the recorded numerical inputs.

C. Focused consistency checks

Verify the following using the existing implementation:

1. At (R_{\mathrm{eff}}=0), the recognized collateral contribution is zero.
2. MarketValue and FaceValue produce the expected difference when the bond trades away from par.
3. The collateral bond is not double-counted as an ordinary portfolio asset.
4. The bond’s value changes consistently with the relevant simulated interest-rate state.
5. The collateral value and exposure use consistent currency and numeraire normalization.
6. The resulting secured exposure reconciles to the engine’s aggregation calculation.

For each check, report Pass / Fail / Not Tested, with supporting numerical evidence.

2. Execution and evidence

Prefer a small, reproducible test or diagnostic harness using existing unit-test infrastructure.

* Reuse existing test data and configurations wherever possible.
* Identify the precise branch/commit, source files and key functions.
* Run the relevant tests if feasible, and cite actual numerical output.
* Clearly distinguish executed tests from static code inspection.
* If execution is unavailable, derive a small numerical example directly from the code, label it illustrative, and identify what remains unverified.
* Avoid modifying production code solely to produce this document. Temporary diagnostics or a standalone test are acceptable.

3. Deliverable

Target 2–3 pages with:

1. A compact table of test inputs and numerical results.
2. A short pathwise bond-valuation calculation.
3. A short collateral/exposure calculation.
4. A Pass / Fail / Not Tested validation summary.
5. Any discrepancies, assumptions or implementation limitations revealed.

Include source references for important calculations, but avoid reproducing large portions of the code.

Out of scope: Repack methodology, multi-bond default modeling, proposed placement of (s^*), calibration Jacobian redesign, broad XVA architecture, and general financial theory.

Success criterion: Another quant developer should be able to follow one bond from simulated risk factors to bond MtM, collateral recognition and final exposure, and independently reproduce the numerical result.
