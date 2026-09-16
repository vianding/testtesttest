Use v0.7_final as the source for the top-down CVA methodology and the current draft as the source for the substantially improved bottom-up SPIRE transaction mechanics.

The current draft has overcorrected in one respect: in replacing the earlier simplified treatment with the much more rigorous contractual derivation, it has largely removed the useful top-down CVA framework that was present in v0.7.

Do not recreate that framework from scratch. Revisit v0.7 and restore its useful top-down methodology, updating it wherever the conclusions of the current draft have superseded the earlier treatment.

In particular, preserve from v0.7 the reader-oriented progression from the standard CVA problem into what is special about this transaction: the loss-relevant event, exposure conditional on that event, non-cash collateral, pathwise netting/positive-part treatment, default-probability integration, wrong-way dependence, and mapping into the production CVA machinery.

However, v0.7 is not authoritative where the current investigation has changed our understanding. Do not blindly paste old equations or conclusions. The current draft's contractual analysis governs where the two conflict—especially regarding:

Original Collateral Default under Condition 8(c) as the contractual trigger;
deemed swap Early Termination Date;
\(V_t\), \(V_{\tau^-}\), \(COA_\tau\), and \(ETA_\tau\) as distinct quantities;
CSA collateral being accounted for inside contractual close-out;
\(B_{\tau^-}\), \(B_\tau^{def/restr}\), and \(B_\tau^{liq}\);
the 30-Reference-Business-Day liquidation process;
Available Proceeds and Condition 15;
limited recourse and extinguishment of residual claims;
the possible result that the Dealer ultimately reaches the unposted remainder of the collateral pool through its senior waterfall claim, rather than recovery being determined solely by \(N_\tau^{post}\);
the consequent reassessment of the importance of Delivery Cap / pool exhaustion for terminal loss;
avoidance of CSA and sovereign-credit double counting.

Think of the revision as:

$$ \boxed{ \text{v0.7 top-down CVA architecture} + \text{current draft bottom-up contractual mechanics} = \text{next version} } $$

The resulting document should first tell the reader what CVA calculation is being performed, then use the SPIRE contractual mechanics to derive what the exposure, collateral, recovery and loss actually mean in that calculation.