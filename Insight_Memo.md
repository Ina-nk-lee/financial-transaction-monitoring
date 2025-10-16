# Insight Memo — Project 2: Financial Transaction Monitoring

**Project:** Overnight Batch Data Pipeline
**Sprint Window:** Sept 25 – Oct 16 2025  
**Team:** Ina Lee, Han Kim

---

## Insight 1 — Serverless Batch ≠ Always Cheaper

**Evidence:**  
Cost model in `Cost-Infrastructure-Plan.md` shows AWS Glue + Athena cost ≈ **$128 / mo**, vs manual EC2 ETL ≈ **$600 / mo**.  
However, sensitivity (+25 % data) → + $17 / mo, and Athena pricing rises linearly with scan size.  

**Why It Matters:**  
It corrected my assumption that *“serverless = low cost.”*  
In a nightly 2 TB pipeline, pay-per-scan fees can escalate if queries are inefficient.  
This changed my architecture emphasis from pure cost minimization to **cost predictability** with lifecycle rules and query profiling.  

**Limits:**  
Based on AWS benchmarks and synthetic loads, not live spending data.  
Spot pricing or regional discounts might shift totals.  

**Next Question:**  
How can query-plan optimization and partition pruning reduce Athena scan cost without hurting data freshness?

---

## Insight 2 — Ethical Automation Works Only When Tests Name the Harm

**Evidence:**  
`Clause_Control_Test.md` shows pytest fail message:  
> “Harm: unlawful processing → {'U123'}”  

After adding a Glue pre-join filter, the test turned green (see `Ethical_Operations_Bonus.md`).  

**Why It Matters:**  
It proved that embedding ethical constraints as code is feasible only when each assertion links directly to a stakeholder harm.  
Without that explicit mapping, CI results become just compliance noise.  
This clarity shifted my focus from passing tests to **communicating who is protected and how**.  

**Limits:**  
Applies to synthetic consent data; no real PII used.  
Moral harms like indirect bias remain untested.  

**Next Question:**  
Could we extend this “harm-naming pytest” pattern to measure false-positive burdens on small merchants?

---

## Insight 3 — Cross-Jurisdiction Privacy Can Constrain Data Design More Than Compute Limits

**Evidence:**  
Architecture diagram enforces *“No Cross-Region Replication”* and KMS-per-region keys (see `Architecture-Diagram.md`).  
Glue must run separately in `ca-central-1`, `ap-south-1`, and `eu-west-2`, increasing job count but preserving residency.  

**Why It Matters:**  
I originally saw regulations as legal overhead, not technical constraints.  
But data residency and encryption domains actually shape the pipeline’s parallelism and cost profile.  
Privacy guardrails became **first-class design constraints**, not afterthoughts.  

**Limits:**  
Did not simulate real cross-border latency or multi-region Glue failures.  
All timing estimates use AWS documentation.  

**Next Question:**  
Can we build a federated aggregation model that keeps data local but still produces global AML insights?
