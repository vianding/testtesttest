Please revise Sections 1.1 and 1.2 of the CVA white paper. The objective is to make the opening CVA framework substantially cleaner and more intuitive, while preserving the mathematical correctness needed for the later sovereign-collateral analysis.

Merge 1.1 and 1.2 into a single compact section if that produces the cleanest exposition. We do not need two subsections merely to move from the primitive CVA expectation to its factorised/discrete form.

Structure the revised discussion as follows:

1. Start from the primitive definition
    > CVA_0=E^Q[D(0,\tau)L_\tau 1_{\{\tau\le T\}}],
    >
    where \tau is the loss-relevant event and L_\tau is the Dealer’s event-state loss.
2. Move quickly to the conditional/factorised representation
    > CVA_0=\int DF(0,t)E^Q[L_t\mid\tau=t](-dS(t)),
    >
    and then its discrete exposure-grid equivalent. Do not over-explain the transition; this is standard CVA machinery.
3. Explicitly connect this to the familiar practitioner shorthand
    > CVA\sim \sum DF\times PD\times EPE\times LGD.
    >
    Use this as an intuition/organising bridge, not as the fundamental formula for this transaction. Explain briefly that EPE\times LGD is a convenient specialisation of the more general conditional expected-loss term.
4. Use that bridge to motivate the three transaction-specific questions already identified in the paper:
    * Q1 / PD: What event drives the loss, and whose credit/default process generates its timing?
    * Q2 / Exposure or claim: What is the Dealer actually owed when that event occurs?
    * Q3 / Recovery/LGD: What resources are available to meet that claim, and what residual amount becomes Dealer loss?
5. Preserve the important transaction-specific conclusions:
    * the loss-relevant event is default/restructuring of the third-party sovereign collateral issuer, not automatically counterparty default;
    * the Dealer’s event-state claim follows the contractual close-out mechanics and should not simply be identified with the impaired collateral value;
    * the collateral is a security, not cash, and its event-state realisation plus the contractual waterfall determines what is available to satisfy the claim;
    * therefore the usual independent-looking EPE\times LGD decomposition is potentially misleading here.
6. Retain and sharpen the wrong-way-risk point:
    > E^Q[L_t\mid\tau=t]\neq E^Q[L_t]
    >
    in general. The same sovereign event that generates PD also impairs the collateral supporting the Dealer’s claim. This is structural wrong-way risk, not something that must be manufactured by imposing an arbitrary correlation between unrelated exposure and collateral processes.
7. Be careful not to overstate what this means for modelling. Under our baseline recovery architecture, the sovereign event can directly switch collateral realisation to the specified event-state value (e.g. R_{\rm eff}IR_tN); a separate dynamic pre-default bond-price process is therefore not required merely to create the default/collateral dependence. Other simulated market factors may nevertheless affect the close-out exposure, so conditional versus unconditional expected loss remains an implementation issue.
8. Move Felix/production-specific implementation detail out of this introductory section. The current discussion of pathwise exposure simulation, conditional-default/common-factor hazard adjustments, production weighting conventions, etc. belongs in the later implementation / wrong-way-risk discussion (currently around §§8.2/8.4). Section 1 should establish the economics and mathematical framework, not resolve engine implementation.
9. Keep the opening concise. The desired conceptual progression is:
    > \boxed{\text{General CVA}\rightarrow PD\times EPE\times LGD\text{ intuition}
    > \rightarrow Q1/Q2/Q3
    > \rightarrow\text{why this transaction is structurally different}.}
    >
10. Review the remainder of the document after making this change and update cross-references, notation, and later statements where necessary so that the revised Section 1 is consistent with the subsequent definitions of \tau, V_t^{co}, L_t, B_t^{liq}, R_{\rm eff}, and the later wrong-way-risk/production sections. Do not change the substantive modelling choices elsewhere merely for stylistic consistency.

The final result should read like a quantitative white paper for practitioners: mathematically precise but economical. Avoid spending a page proving or explaining standard CVA factorisation. The intellectual weight should fall on what PD, exposure/claim, and recovery actually mean for this unusual sovereign-bond-collateral structure.
