Objective

Please investigate the existing source code and propose the cleanest implementation approach for two requirements needed for the SPIRE trade:

1. Represent the SPIRE trade payoff/value as

[
V_{\mathrm{SPIRE}} = \min\left(V(\mathrm{Product}_A),,V(\mathrm{Product}_B)\right)
]

where Product A and Product B are the two alternative collateral/product legs/configurations relevant to the SPIRE structure.

2. Calculate and expose ColVA1, ColVA2, and ColVA3 separately to the user, rather than only exposing an aggregated ColVA result.

I will provide fva_model.tex and other findings/design notes from our SPIRE/FVA/ColVA investigation as reference. Please use those documents to understand the intended financial/model behavior, but focus this task on determining how the requirements should be implemented within the existing codebase.

Part 1 — Implementing min(PV(A), PV(B))

Please inspect the source code and determine how products/trades and their valuation logic are currently represented.

Specifically investigate:

* Whether there is already a composite/product-wrapper mechanism that can combine two existing products.
* Whether there are existing examples of min, max, optionality, cheapest-to-deliver, alternative-delivery, or similar payoff structures.
* At what layer the min(PV(A), PV(B)) logic should naturally live:
    * product/payoff definition,
    * pricing/model layer,
    * trade wrapper,
    * scenario valuation,
    * or another existing abstraction.
* Whether Product A and Product B can simply reuse existing product/pricer implementations or require special handling.
* How this construction behaves under scenario simulation/risk calculations.

The last point is important: please determine whether the framework can evaluate

[
\min(V_A(\omega),V_B(\omega))
]

scenario by scenario/path by path, rather than merely calculating min(PV_A, PV_B) once at time 0. Please identify what the existing architecture naturally supports and what the SPIRE model requires based on the supplied documentation.

Trace the relevant classes/functions and provide concrete source-code references.

Part 2 — Separately exposing ColVA1 / ColVA2 / ColVA3

Please trace the complete ColVA calculation and reporting pipeline in the existing implementation.

Starting from the model/calculation layer, identify:

* where ColVA is calculated;
* whether ColVA1, ColVA2 and ColVA3 already exist internally as separate quantities;
* where/if they are aggregated;
* what result/result-container objects carry them;
* how XVA results are exposed to downstream callers;
* how the current user-facing ColVA field is populated;
* whether the reporting/display infrastructure supports adding additional result fields without changing the underlying valuation architecture.

Please explicitly map the flow, for example:

model calculation → internal ColVA components → aggregation → result object → API/reporting/display

using the actual classes/functions/files from the codebase.

Then determine the minimum clean change required to expose something conceptually like:

ColVA
ColVA1
ColVA2
ColVA3

while preserving the existing aggregate ColVA output for backward compatibility, unless the existing architecture suggests a better convention.

Part 3 — Interaction between the two requirements

Please also investigate whether the min(Product A, Product B) construction affects how ColVA1/2/3 should be calculated or reported.

In particular, determine whether the existing architecture naturally calculates the ColVA components on the selected/minimum-value branch in each scenario, or whether special logic is required to preserve the correct decomposition.

Do not assume that these are simply two independent UI changes; trace the valuation dependency and flag any modeling or implementation issue you find.

Deliverable

For now, do not make broad code changes. First return an implementation investigation containing:

1. Relevant files/classes/functions.
2. Current valuation and ColVA data flow.
3. Existing mechanisms we can reuse.
4. Recommended implementation for min(PV(A), PV(B)).
5. Recommended implementation for exposing ColVA1/2/3.
6. Any interaction between the two.
7. Minimal set of files/functions that would need modification.
8. Risks, ambiguities, or assumptions that need confirmation.
9. If useful, small pseudocode/code snippets showing the proposed changes.

Please prefer reuse of existing framework abstractions over introducing SPIRE-specific machinery. We want the smallest implementation that is consistent with the architecture and remains reusable for similar structures.

Treat fva_model.tex and the accompanying SPIRE findings as the modeling specification/reference, and flag explicitly if the current source-code behavior differs from what those documents imply.
