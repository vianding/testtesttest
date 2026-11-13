Task: Architecture Review of Physical Bond Collateral Integration in Felix XVA

1. Objective

Conduct an independent, code-grounded architecture review of the existing D3/D4 physical bond collateral implementation in Felix’s Monte Carlo XVA engine.

The implementation is already underway. This is a review and validation task, not a request to rewrite, redesign, or extend the code.

Our objectives are to:

1. Understand how the existing D3/D4 changes integrate with the standard XVA calculation architecture.
2. Validate whether responsibilities are assigned to appropriate architectural layers.
3. Identify potential financial-modeling, numerical, and software-design inconsistencies.
4. Determine whether the implementation is correct and sufficiently robust for the intended REPACK use case.
5. Recommend targeted changes where necessary, distinguishing correctness issues from optional architectural improvements.

Do not modify source code during this review.

2. Reference Material

Use the attached XVA_CALL_STACK_AND_LINE_TABLES.pdf as the initial navigation guide.

The document describes:

* Table 1: Standard Monte Carlo CVA call stack, steps 1–19.
* Table 2: Existing D3/D4 bond valuation and collateral integration.
* Detailed function-level traces for steps 6–19.

The document was prepared from a static code review and may not reflect the latest working tree. Verify all relevant claims against the current source code.

Pay particular attention to the D3/D4-marked changes.

Do not limit the review to those changes: examine surrounding standard XVA functionality whenever necessary to establish correctness.

3. Architectural Principles to Validate

Treat the following as review hypotheses, not predetermined design requirements.

A. Separation of product valuation and collateral treatment

The bond valuation function should ideally compute the bond’s value under the simulated market state independently of whether the bond is used as:

* An ordinary traded bond;
* A physical collateral asset;
* A component of a REPACK transaction.

The collateral layer should determine how the bond’s value is recognized in exposure calculations.

Investigate whether this separation exists in the current implementation.

In particular:

* Does bond pricing contain collateral-specific assumptions?
* Are RFV/RMV recovery conventions correctly separated from ordinary bond valuation?
* Can the same pricing infrastructure support both a bond as a product and a bond as collateral?
* Is there duplicated valuation logic?
* Are responsibilities distributed consistently across product factories, value functions, CSA machinery, and exposure aggregation?

Distinguish deliberate product-specific behavior from unnecessary coupling.

B. Separation of collateral valuation, balance, and recognition

These are conceptually different quantities:

Collateral asset valuation

The market or modeled value of the collateral asset at a simulated future date.

Collateral balance

The quantity/value of collateral actually held under the contractual collateral arrangement.

Recognized collateral

The amount credited against counterparty exposure after applicable recovery, haircut, timing, and closeout assumptions.

Review whether the current implementation distinguishes these concepts appropriately for the intended D3/D4 methodology.

Investigate:

* How the collateral asset pool is valued;
* How R_eff is applied;
* How TradeIndependentAmount is used;
* Whether the recognized collateral is always derived from current bond value;
* Whether margin frequency, settlement lag, MTA, thresholds, or MPOR are relevant to the intended REPACK contract;
* Whether the implementation makes implicit assumptions about collateral delivery or balance evolution.

Do not require full collateral-account simulation if the contractual structure or approved methodology does not need it.

C. Correct placement within the XVA calculation sequence

Review the integration points across the full call stack, particularly:

Step 7 — Portfolio construction

* resolveCollateralAssets
* CVAPortfolioComposition::build

Validate collateral identification, portfolio mapping, CSA association, and asset relabeling.

Steps 8–10 — Product conversion and valuation setup

* ValuationEngineExposure::initialise
* PortfolioValueFunction::newPortfolioValueFunction
* Bond factory and cash-flow expansion
* calculateExposure

Validate product dispatch, risk-factor registration, value-function construction, and MPOR handling.

Steps 11–13 — Simulation and CSA preparation

* resolveCSADetails
* prepareIndependentAmounts
* buildSimpleMonteCarloSimulation
* registerAllIndices
* prepareOptionValuationStructures

Validate CSA configuration, model reduction, bond funding setup, spread calibration, and independent-amount initialization.

Steps 14–15 — Monte Carlo path valuation

* runASimulation
* calculateForwardValuesOfGivenPaths

Validate that collateral bonds are valued consistently with the derivative portfolio under the same simulated market states.

Step 16 — Exposure aggregation

* aggregatePathValue
* aggregatePortfolioValuesAndCollateralCapping

This is the highest-priority review area.

Validate the ordering and interaction of:

1. Portfolio MtM;
2. Collateral asset masking;
3. CSA collateral capping;
4. Physical collateral pool valuation;
5. TradeIndependentAmount subtraction;
6. Contractual independent amounts and initial margin;
7. Secured EPE/ENE;
8. CVA accumulation.

Check for double counting, incorrect netting, sign errors, inconsistent normalization, and unexpected interactions with existing CSA logic.

Steps 17–19 — Post-processing and reporting

Validate:

* Monte Carlo normalization;
* Collateral asset NPV reporting;
* Exclusion of synthetic collateral assets from ordinary trade NPV;
* CVA and exposure consistency;
* Subsequent XVA requests and risk calculations.

4. Review of D3/D4 Financial Methodology

Review the implementation against its intended methodology, without silently changing the methodology.

D3 — Recovery of Face Value (RFV)

Validate:

* Identification of the principal-carrying bond leg;
* Face-value construction;
* Inflation-linked principal and index fixing treatment;
* Recovery application;
* Timing and discounting conventions;
* Behavior under default-related scenarios.

D4 — Recovery of Market Value (RMV)

Validate:

* Bond funding curve selection;
* Spread (s^*) calibration;
* Whether calibration occurs once at setup as intended;
* How the calibrated spread is applied along future paths;
* Whether bond market-value dynamics are represented consistently;
* Application of R_eff;
* Any interaction between bond recovery and counterparty recovery.

Pay particular attention to the distinction between:

* A fixed calibrated spread (s^*);
* A stochastic issuer spread process;
* Rate/inflation-driven changes in bond PV.

Do not assume the current hybrid model contains a stochastic issuer-specific bond spread process.

Identify what risks the implementation captures and what risks it approximates or excludes.

Shared stochastic dynamics

Assess whether derivative and collateral valuations are generated using a consistent simulation environment.

Verify:

* Relevant interest-rate, inflation, credit, and FX factors;
* Joint risk-factor dependencies;
* Correlation treatment;
* Model-reduction behavior;
* Any deterministic approximations.

Identify missing dynamics only where they matter to the intended methodology.

5. Specific Architectural Questions

Provide explicit answers to the following.

Q1. Is TradeIndependentAmount an appropriate abstraction for physical collateral in the current REPACK implementation?

Assess correctness, limitations, and potential unintended interactions with contractual IA and IM.

Q2. Is Step 16 the correct place to apply physical collateral to secured exposure?

If yes, explain why. If not, identify the more appropriate location.

Q3. Is the current ordering of CSA capping and physical collateral subtraction financially correct?

Consider cash VM, physical collateral, contractual IA, MPOR, and exposure sign.

Q4. Is switching off cash VM for asset CSAs justified?

Determine whether this is a valid contractual assumption for the targeted REPACK structure or an overly broad implementation rule.

Q5. Are bond valuation and collateral recognition sufficiently decoupled?

Identify any coupling that materially affects correctness, maintainability, or reuse.

Q6. Does multi-CSA composition preserve correct collateral ownership and netting boundaries?

Verify asset-to-CSA mapping, aggregation, and treatment of multiple collateral assets or CSA couples.

Q7. Does the implementation support CVA sensitivities consistently?

Review AAD propagation through bond valuation, spread calibration, collateral masking, and exposure adjustments. Identify any intentionally frozen parameters.

Q8. What are the main architectural limitations if we later generalize beyond the current REPACK bond collateral use case?

Distinguish limitations requiring redesign from features that could be added incrementally.

6. Validation Strategy

Where practical, identify or execute focused tests covering:

* No collateral: existing XVA behavior unchanged.
* Zero collateral value: physical collateral produces no exposure reduction.
* Fully covering collateral: secured positive exposure is appropriately reduced.
* Bond as an ordinary product versus bond as collateral.
* RFV versus RMV.
* Positive and negative bond values.
* Multiple bonds within one collateral pool.
* Multiple CSA couples.
* Cash VM enabled versus disabled.
* MPOR enabled versus disabled.
* CVA and first-order CVA risk.
* NPV reporting and collateral asset NPV reconciliation.

Include stressed paths where derivative exposure increases while collateral bond value declines.

Check whether any resulting behavior is intentional under the approved methodology.

For tests that cannot be executed, specify the proposed test, expected invariant, and reason execution was not possible.

7. Review Method

Use the actual repository as the source of truth.

For each significant finding:

1. Identify the relevant function and file location.
2. Explain the current implementation behavior.
3. Explain the intended financial or architectural behavior.
4. State whether they are consistent.
5. Provide concrete evidence.
6. Assess the impact.
7. Recommend the smallest justified correction, if needed.

Classify each finding as:

* Correctness defect: Financially or numerically incorrect behavior.
* Architectural risk: Currently functional but fragile or tightly coupled.
* Model limitation: Known or newly identified modeling approximation.
* Optional improvement: Potential improvement without demonstrated correctness impact.
* Validated: Implementation appears appropriate for the intended scope.

Separate confirmed defects from hypotheses requiring further testing.

Do not infer implementation behavior solely from function names or comments.

8. Deliverables

Produce a concise but technically substantive Markdown review report with the following structure.

Executive Assessment

* Overall architecture assessment;
* Whether the existing implementation is fit for the intended REPACK scope;
* Most important findings;
* Whether material refactoring is justified.

Architecture and Data Flow

Provide a compact diagram showing how the collateral asset moves through:

Portfolio construction → Bond valuation → Simulation → CSA/IA treatment → Exposure → CVA.

Identify the actual owning functions and data structures.

Findings Table

ID	Severity	Category	Function/location	Finding	Evidence	Recommendation

Prioritize by financial correctness and production risk.

D3/D4 Methodology Validation

Separate findings for RFV and RMV, including model assumptions and limitations.

Targeted Recommendations

Group recommendations into:

* P0: Must fix for financial correctness;
* P1: Important for robustness or production readiness;
* P2: Optional architectural improvements or future extensibility.

For each recommendation, estimate scope and regression risk.

Validation/Test Matrix

Summarize existing test coverage, tests executed, observed results, and outstanding tests.

Open Questions

List questions that cannot be resolved from the code alone and require confirmation from the quant/model owner or business.

9. Constraints

* Do not modify production code.
* Do not introduce new architecture without demonstrating why existing abstractions are insufficient.
* Do not treat every modeling approximation as a defect.
* Do not assume the uploaded call-stack document is fully current.
* Do not claim tests passed unless actually executed.
* Preserve the existing REPACK D3/D4 methodology unless a material inconsistency is demonstrated.
* Prioritize correctness, numerical consistency, maintainability, and minimal regression risk over theoretical architectural purity.

The desired outcome is a defensible assessment of whether the current D3/D4 integration is correctly placed within the XVA architecture, what genuinely needs correction, and what should be left alone.
