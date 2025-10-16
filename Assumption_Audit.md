# Assumption Audit

---

## Key Assumptions & Tests

| Assumption | Why It Might Fail | Test You Ran | Result | Impact on Conclusions |
|-------------|------------------|---------------|--------|-----------------------|
| Glue throughput ≥ 200 MB/s | AWS benchmarks may differ by region | Back-of-envelope throughput calc (2 TB ÷ 6 h) | Feasible under 6 h | Retained |
| Athena cost scales linearly | Query volume may grow unpredictably | Synthetic probe plan (+25 %) | +$17 / mo increase | Revised cost buffer + 30 % |
| Consent registry always reachable | Network/API outage possible | Step Function fail-state simulation | Adds 5 min retry delay | Added kill-switch Lambda |
| Lifecycle deletion triggers on time | CloudWatch rule drift | Policy trace mock | 100 % TTL hits ≤ 90 days | Retained |
| Hash rotation every 90 days sufficient | Key reuse vulnerability | Policy trace (salt rotation log) | Meets UK DPA guidance | Retained |

---

## Sensitivity / Spec Grid
| Parameter | Base | Variant | Δ Result |
|------------|------|---------|----------|
| Storage size | 3 TB | 3.75 TB (+25 %) | +$17 mo |
| Runtime | 6 h | 7 h | +$3 compute cost |
| Retry count | 1 | 2 | +1 h delay, <$15/day ceiling |

---

## Actions Taken
- Retained throughput and retention assumptions.  
- Updated cost envelope to include 30 % margin.  
- Added orchestration retries (2× limit ≤ 8 h).  
- Confirmed privacy guardrails (k ≥ 10, TTL ≤ 90 d).  
