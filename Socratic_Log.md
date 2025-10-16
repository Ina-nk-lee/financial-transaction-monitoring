# Socratic Log

---

### Context
Designing an overnight 2 TB batch pipeline across Canada, India, UK, balancing SLA ≤ 6 h and ≤ $500 / mo cost ceiling.

---

### Prompt A (Design Alternative)
> “Should we use EMR clusters instead of Glue for throughput control?”  
**Tested:** cost math for EC2 vs Glue; Glue chosen for auto-scaling and lower ops overhead.

---

### Prompt B (Red-Team)
> “If consent joins slow the job, is skipping them ever acceptable?”  
**Response:** No — fail fast is preferable to silent non-compliance.  
Added Step Function fail-state + SNS alert as ethical guardrail.

---

### Inflection Point
Realized privacy and cost are co-equal SLAs, not trade-offs.  
The architecture shifted from cost-driven ETL to compliance-aware design,  
where a single red-bar (test fail) blocks deployment as intentionally ethical behavior.

---

### Evidence
- `Architecture-Diagram.md` — Kill-switch Lambda and guardrail nodes.  
- `Clause_Control_Test.md` — Consent test assertion naming harm.  
- `Ethical_Operations_Bonus.md` — Green-bar CI confirmation.  

---

### Outcome
- Added automatic kill-switch Lambda for consent registry failures.  
- Re-classified privacy tests as hard gates in CI.  
- Shifted evaluation criteria from pure cost to ethical reliability.  
