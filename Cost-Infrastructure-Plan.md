## 5. Cost & Infrastructure Plan

### 💰 Daily / Monthly Cost Model

| Service                            | Metric / Assumption          |  Cost / Unit |        Daily |         Monthly | Notes                                     |
| :--------------------------------- | :--------------------------- | -----------: | -----------: | --------------: | :---------------------------------------- |
| **AWS Glue**                       | 2 ETL runs × 1.5 h @ $0.44/h |        $0.66 |        $0.66 |             $20 | Batch compute for nightly transformations |
| **Athena**                         | 4 TB scan @ $5/TB            |       $20.00 |        $0.67 |             $20 | Query engine for joins and aggregation    |
| **S3 (raw + aggregate)**           | 3 TB stored                  | $0.023/GB-mo |            — |             $69 | 90 d raw, 180 d agg lifecycle             |
| **CloudWatch Logs**                | 20 GB ingestion              |     $0.60/GB |            — |             $12 | Metric + log storage                      |
| **Lambda Notifications**           | 0.5 M invocations            |      $0.20/M |            — |              $1 | SLA + budget alerts                       |
| **Step Functions (orchestration)** | 60 runs @ $0.025/run         |            — |        $0.05 |           $1.50 | Lightweight orchestration                 |
| **Total (approx.)**                | —                            |            — | **$2.0/day** | **≈ $123 / mo** | 75 % below $500 ceiling                   |

**Sensitivity:** +25 % data growth → +$17 / mo increase (≈ $140 / mo).  
Still within $500/month educational credit ceiling.

---

### ⚙️ Kill-Switch Playbook

| Trigger                     | Detection                   | Automated Response                                | Owner         |
| :-------------------------- | :-------------------------- | :------------------------------------------------ | :------------ |
| **Batch runtime > 8 h**     | CloudWatch timer metric     | Lambda terminates Glue job, sends Slack/SNS alert | Pipeline Ops  |
| **Cost > $15/day**          | AWS Budgets threshold alarm | Step Function halts further ETL runs              | Data Steward  |
| **S3 growth > 5 TB**        | Storage metrics anomaly     | Archive older aggregates to Glacier               | Platform Team |
| **Athena error rate > 3 %** | Error logs / CloudWatch     | Retry once, escalate to on-call engineer          | On-Call       |

---

### 🧩 Contingency Plan

**If Costs Spike:**

- Suspend non-critical transformations (feature-rich joins, full re-scans).
- Auto-archive historical aggregates to **S3 Glacier Deep Archive**.
- Notify regulator contact if daily report is delayed beyond window.

**If SLA Slips (p95 > 6 h):**

- Prioritize largest region (Canada) first, then India, UK.
- Switch temporary compute tier → **AWS Batch Spot Instances** (2× capacity).
- Run downstream aggregations on partial data; flag as “deferred run” in ledger.

**If Infrastructure Fails:**

- Retry up to 2× within 8 h window via Step Functions.
- Rollback metadata updates in Glue Catalog (idempotent job design).
- Resume next batch using checkpointed S3 prefixes.

---

**Summary:**  
The pipeline operates at ≈ $2/day and scales linearly with data size.  
Automated kill-switches prevent cost overruns or missed regulator deadlines,  
and contingency procedures guarantee recovery within the same nightly window.
