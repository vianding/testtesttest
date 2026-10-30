# Repository Investigation Brief: Contractual Analytics Spike

## Repository locations &#40;fill in before running&#41;

- **Project F repository:** `<<INSERT_PROJECT_F_REPOSITORY_PATH>>`
- **Project Q repository:** `<<INSERT_PROJECT_Q_REPOSITORY_PATH>>`

## Mission

Investigate whether **Project F** should implement a native, independent, high\-performance fixed\-income contractual analytics foundation, using **Project Q’s source code as a reference** rather than calling Project Q at runtime\.

This is an **exploratory spike / go\-no\-go assessment**, not an approved rewrite or migration\. We have source access to both repositories\. Your job is to inspect the code and provide evidence\-based findings, an architecture recommendation, and a tightly scoped prototype plan\. Do **not** implement changes yet\.

### Why this is being considered

1. Eliminate a potential runtime/build dependency on Project Q and reduce key\-person/maintenance risk\.
2. Explore modern, performance\-oriented implementations of bond **price/yield &#40;P/Y&#41;** and **relative\-value &#40;RV&#41;** calculations, especially for repeated and high\-throughput workloads\.
3. Potentially establish a reusable **upstream contractual\-data layer** inside Project F for pricing, RV, derivatives and risk analytics\.
4. Determine whether the practical benefit justifies the complexity of bond conventions and the ongoing burden of owning this code\.

Do **not** presume that Project Q is slow, Project F is deficient, or a rewrite is desirable\. Verify these hypotheses\.

## Operating instructions

- Read both repositories, including build configuration, tests, documentation, public APIs, implementations and relevant call sites\. Prioritize code over high\-level descriptions\.
- **Read\-only investigation\.** Do not modify either repository, generate large code changes, or introduce dependencies\. If compilation, test execution or profiling is feasible without changing source or affecting shared environments, describe the exact proposed commands first; otherwise provide a reproducible benchmark plan\.
- Trace actual call graphs\. Cite concrete **repo\-relative file paths, symbols and line numbers** for important claims\. Differentiate observed facts, inference and unknowns\.
- Inspect selectively and efficiently; avoid trying to read every file\. Note what was and was not inspected\.
- Treat Project Q as a reference implementation/behavioral oracle, not a mandatory target design\. Clearly flag licenses or other constraints relevant to code reuse if present; do not assume copying is permitted merely because source is accessible\.
- Keep recommendations compatible with Project F’s existing engineering patterns where reasonable\. Propose modernization only where it has demonstrable benefit\.
- Use the exact names **Project F** and **Project Q** throughout\.

## Investigation workstreams

### A\. Current\-state inventory and architecture

For **each repository**, identify:

- Languages, build system, binary/runtime boundaries, target platforms, compiler settings and important third\-party dependencies\.
- Existing bond/product abstractions, lifecycle, data ownership, public APIs, and how objects are created, cached, reused and destroyed\.
- How contractual terms enter the system; where schedules, cash flows, accrued interest, ex\-dividend rules, settlement conventions, day counts, calendars and coupon rules are represented and computed\.
- Implementations for clean/dirty price, yield solving, derivatives/sensitivities and relevant RV measures &#40;e\.g\., ASW, Z\-spread&#41;, including where market curves are obtained\.
- Existing test suites, test instruments, golden results and documented known limitations\.

Produce a concise **capability matrix** with: feature, Project F implementation, Project Q implementation, notable differences, evidence, and confidence\. Mark *not found* separately from *not supported*\.

### B\. Contractual engine decomposition

Trace a representative fixed\-rate bond end\-to\-end in each repository:

`instrument terms -> normalized definition -> schedule -> projected/known cash flows -> accrual -> price from yield -> yield from price`

Identify reusable computational primitives and implicit dependencies, including conventions, date/calendar libraries, day\-count handling, global/singleton state, reference data, error handling and object lifetime\.

Pay special attention to **the boundary between contractual facts and market\-dependent analytics**\. Propose what could live in:

1. A market\-independent &#40;subject to explicit fixing/observation inputs&#41; contractual definition and cash\-flow/schedule component\.
2. A P/Y and risk calculation layer that consumes that component\.
3. An RV layer consuming the contractual outputs plus Project F’s market curves and market conventions\.

Identify any cases where this neat split breaks down &#40;e\.g\., FRNs, optionality, exercise assumptions, inflation, fixings&#41;\.

### C\. Performance investigation

Assess whether performance opportunities actually exist\. Find potential overhead from:

- Repeated instrument creation or schedule/cash\-flow regeneration\.
- Heap allocations, object graphs, virtual dispatch, copies and data translation\.
- Price/yield root finding, numerical tolerances, discount factor evaluation and derivative reuse\.
- Scalar versus vector/batch workloads, parallelism and thread safety\.
- Caching of immutable contractual information versus market\-dependent calculations\.

Separate **observed bottlenecks** &#40;measured&#41; from **suspected bottlenecks** &#40;code inspection&#41;\. Do not assert speedups without benchmarks\.

Propose fair, reproducible microbenchmarks, at minimum: one\-time instrument construction; schedule/cash\-flow generation; accrued interest; price\-from\-yield; yield\-from\-price; repeated repricing; and multi\-bond batch throughput\. Include warm/cold workloads, allocation behavior, correctness/tolerance checks, and comparable compiler/build settings\. Identify the end\-to\-end workload most relevant to an algo\-trading consumer if discoverable\.

### D\. Coverage and long\-tail complexity

Map the complexity beyond a plain fixed\-rate bullet bond, prioritizing examples actually supported by Project Q or used in Project F:

- Irregular/short/long coupons, stubs, holiday adjustments, settlement and ex\-dividend conventions\.
- FRNs, fixings, reset rules and floating coupon conventions\.
- Amortizing/sinking, step\-up, callable/putable, inflation\-linked and other special structures if present\.
- Edge cases &#40;negative yields/rates, unusual dates, near\-maturity bonds, accrued\-interest transitions, convergence or precision failures&#41;\.

Distinguish **minimum valuable coverage**, **hard\-to\-reproduce legacy complexity**, and **out\-of\-scope functionality**\. Identify coverage gaps and potential regression risk\.

### E\. Dependency, maintenance and reuse risks

- Identify dependencies that a native Project F implementation would actually remove, and dependencies it might merely replace or re\-create\.
- Evaluate key\-person risk in terms of undocumented conventions, sparse testing, difficult debugging, and operational ownership—not just linking to Project Q\.
- Evaluate maintainability, API stability, extensibility and deployment/build complexity\.
- Highlight possible legal/licensing or provenance restrictions on porting or copying source\.

## Evaluate multiple strategies

Compare at least these options without assuming a winner:

1. **Do nothing / retain Project F’s current implementation\.**
2. **Targeted improvements inside Project F** &#40;e\.g\., faster P/Y, better caching&#41;, without a new contractual engine\.
3. **Build a narrow native contractual core in Project F**, informed and regression\-tested against Project Q\.
4. **Progressive native replacement for a broader product set**, if supported by evidence\.
5. **Keep Project Q integration as a comparator only** &#40;or as a fallback option in the analysis&#41;, identifying the trade\-offs despite the independence objective\.

Assess correctness, speed potential, coverage, engineering effort, long\-term ownership, dependency reduction, and utility to downstream consumers\. Give explicit conditions under which each option makes sense\.

## Recommend one minimal exploratory spike

Give a concrete small first slice, preferably a representative **fixed\-rate bullet bond** unless code evidence suggests a better candidate\. State:

- Exact existing Project F and Project Q files/classes/functions worth starting from\.
- Proposed native Project F interfaces and ownership boundaries &#40;conceptual signatures or pseudocode only&#41;\.
- Inputs/outputs and what would be precomputed once vs recalculated per valuation\.
- Proposed validation cases and golden/reference comparisons\.
- Benchmark methodology, success criteria and performance measurement approach\.
- Remaining dependency risks, estimated development complexity and obvious blockers\.
- **Stop/go/expand gates** based on measured benefit and uncovered convention complexity\.

Avoid fabricated numerical targets\. Where target latency/throughput or tolerance is unknown, recommend a measurement baseline and which business workload should set the acceptance threshold\.

## Deliverable: concise decision memo

Write a Markdown report with these sections, in this order:

1. **Executive conclusion &#40;max \~1 page&#41;** — provisional recommendation, confidence, main evidence and main uncertainty\.
2. **Repository map** — important entry points and call paths with source references\.
3. **Capability/coverage comparison** — succinct matrix including unverified items\.
4. **Proposed native Project F architecture** — ownership boundaries and data flow; call out what should *not* be in the contractual layer\.
5. **Performance findings and benchmark plan** — facts versus hypotheses clearly labeled\.
6. **Options and trade\-offs** — including doing nothing and targeted optimization\.
7. **Recommended first spike** — exact scope, relevant files, acceptance/stop criteria, and estimated effort range with assumptions\.
8. **Open questions / decisions needed** — only questions that materially affect the decision\.
9. **Evidence appendix** — file paths, line numbers, commands/tests examined, and unverified claims\.

**Decision questions to answer explicitly:**

- Does Project F already have enough contractual infrastructure that building another layer would duplicate effort?
- Is Project Q’s value chiefly its contractual edge\-case coverage, its numerical algorithms, or something else?
- Is performance most likely limited by numerical computation or by repeated data/object preparation?
- Can the proposed contractual core be independent of Project Q **and** remain cleanly reusable upstream of P/Y, RV and risk?
- What is the smallest test that could materially change our decision?
- What finding would make you recommend **not** proceeding?

**Quality bar:** Be skeptical, specific, and implementation\-aware\. Ground claims in actual source\. If information is not verifiable in the repositories, mark it unknown rather than inventing an answer\. The report should support a practical **go / no\-go / limited\-scope** decision, not serve as advocacy for a rewrite\.
