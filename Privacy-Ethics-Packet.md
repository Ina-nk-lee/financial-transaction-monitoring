## 6. Privacy & Ethics Packet

### 🧩 PIA Excerpt

**Purpose:** Detect anomalous financial transactions (AML) while preserving customer privacy and consent integrity.  
**Data Categories:**

- Transaction amount, merchant ID, timestamp, payer ID (hash)
- No names, account numbers, addresses, or device identifiers

**Minimization & Retention:**

- Raw telemetry ≤ 90 days, aggregates ≤ 180 days
- Secure deletion via S3 lifecycle rules; no cross-region replication
- Access restricted to analyst role (read-only, audited 1 year)

**Lawful Basis:**

- Canada → PIPEDA (Principle 5: Retention Limitation)
- India → DPDP Act § 7(Consent & Lawful Purpose)
- UK → DPA 2018 / UK GDPR (Recital 26: Anonymisation Requirement)

---

### 📊 Telemetry Matrix

| Signal             | Value for Analysis | Invasive? | Effort to Protect | Retain? | Guardrail Control                     |
| :----------------- | :----------------- | :-------: | :---------------: | :-----: | :------------------------------------ |
| Transaction amount | High               |    Low    |        Low        |   ✅    | KMS encryption @ rest                 |
| Merchant ID        | Medium             |    Low    |        Low        |   ✅    | Mask merchant IDs in logs             |
| Payer ID (hash)    | Low                |  Medium   |      Medium       |   ✅    | Salted SHA-256 rotation every 90 days |
| IP address         | Medium             |   High    |        Low        |   ❌    | Drop before ingest                    |
| Consent token      | High               |  Medium   |      Medium       |   ✅    | Join with opt-in registry before ETL  |

---

### 🧠 Recourse & Remedy (Example Dependency: AWS Glue)

| Dependency       | Potential Harm                             | Detection Metric                       | Remedy Path                                             | Fallback Test                                           |
| :--------------- | :----------------------------------------- | :------------------------------------- | :------------------------------------------------------ | :------------------------------------------------------ |
| **AWS Glue Job** | Over-retention of unencrypted staging data | S3 object age > 90 days or unencrypted | Lifecycle rule deletes raw data and rotates CMK monthly | `pytest tests/ethics/test_retention_ca.py --max-age 90` |

---

### 📕 Ethics Debt Ledger (Excerpt)

| Status | Risk / Promise                          | Who It Protects      | Control Owner      | Acceptance Test        | Enforcement Point     | Next Action                          | Last Reviewed |
| :----- | :-------------------------------------- | :------------------- | :----------------- | :--------------------- | :-------------------- | :----------------------------------- | :------------ |
| 🔴     | Delete raw telemetry ≤ 90 days (PIPEDA) | All Canadian users   | Data Platform      | `test_retention_ttl`   | S3 Lifecycle Policy   | Apply Terraform rule and re-run test | 2025-10-13    |
| 🔴     | Consent withdrawal exclusion (DPDP)     | Indian opt-out users | Pipeline Eng       | `test_opt_in_enforced` | Glue pre-filter       | Add consent join table to ETL        | 2025-10-13    |
| 🟡     | Merchant ID masking in logs (UK DPA)    | Small UK merchants   | Observability Team | `test_mask_logs`       | CloudWatch log filter | Enable PII masking rule              | 2025-10-13    |

---

**Summary:**  
The pipeline applies privacy-by-design through data minimization, retention controls, and jurisdiction-specific guardrails.  
Two outstanding red-bar tests (PIPEDA retention and DPDP consent) remain open and are tracked in the Ethics Debt Ledger until controls pass CI verification.
