
# Clause → Control → Test — Financial Transaction Monitoring

This document formalizes compliance guardrails for three jurisdictions — **Canada, India, and the UK** — following the official exemplar format.

---

## 1. Canada — PIPEDA (Purpose Limitation & Retention)

**Clause (source)**

> “Organizations shall retain personal information only as long as necessary for the fulfillment of those purposes.”  
> *(PIPEDA Schedule 1, Principle 5 — Retention, Office of the Privacy Commissioner of Canada)*

**Control Design**

| Control | Enforcement Point | Implementation Notes |
|:--|:--|:--|
| Enforce per-region S3 lifecycle rules to delete raw telemetry ≤ 90 days; aggregates ≤ 180 days. | Data storage (S3) | Terraform lifecycle JSON + region-scoped KMS key; replication off. |
| Audit monthly object TTL compliance. | Observability | CloudWatch metric filter scanning object timestamps; SNS alert if >90 days. |

**Failing Test (Red Bar)**

```python
def test_retention_ttl():
    """Canada (PIPEDA) — Retention limit for raw telemetry."""
    objs = list_objects_older_than(90)
    assert len(objs) == 0, f"Harm: over-retention → example={objs[0]}"
```

Expected: 🔴 Fails initially → proves no lifecycle rule yet.  
**Make it Green:** Apply lifecycle rule; rerun CI; expect 🟢 pass.  
**Ledger Entry:** “Retention ≤ 90 days enforced — protects all Canadian users.”

---

## 2. India — DPDP Act (Consent & Purpose Limitation)

**Clause (source)**

> “Personal data shall be processed only for a lawful purpose for which the data principal has given consent.”  
> *(Digital Personal Data Protection Act, 2023 — § 7(1))*

**Control Design**

| Control | Enforcement Point | Implementation Notes |
|:--|:--|:--|
| Validate opt-in token before including record in analytics; skip if consent withdrawn. | Compute (Glue / Athena) | Join with consent registry table pre-aggregation. |
| Block pipeline if consent registry unreachable. | Orchestration (Step Functions) | Add fail state + alert. |

**Failing Test (Red Bar)**

```python
def test_opt_in_enforced():
    processed = {"U100", "U123"}  # includes withdrawn user
    opt_in = {"U100"}
    diff = processed - opt_in
    assert len(diff) == 0, f"Harm: unlawful processing → {diff}"
```

Expected: 🔴 Fails → user U123 processed w/o consent.  
**Make it Green:** Add pre-join filter; re-run CI → 🟢 all pass.  
**Ledger Entry:** “Opt-in enforcement protects Indian users’ consent rights.”

---

## 3. United Kingdom — DPA / UK GDPR (Anonymization before Export)

**Clause (source)**

> “Before disclosure, personal data shall be anonymized or de-identified to prevent re-identification.”  
> *(UK GDPR Recital 26 & ICO Anonymisation Guidance, 2022)*

**Control Design**

| Control | Enforcement Point | Implementation Notes |
|:--|:--|:--|
| Hash payer_id with per-region salted SHA-256; rotate salts every 90 days. | Transform (Glue job / Athena UDF) | Secrets Manager stores active salts; rotation logged. |
| Validate re-id risk < 1 % on sampled export. | Observability | Daily Athena sampling query with CI threshold test. |

**Failing Test (Red Bar)**

```python
def test_reid_rate_threshold():
    risk = estimate_reid_rate(sample)
    assert risk < 0.01, f"Harm: re-identification risk ≥ 1 % (est={risk:.2%})"
```

Expected: 🔴 Fails first → re-id risk high.  
**Make it Green:** Implement salt rotation; run sampling query → 🟢 pass.  
**Ledger Entry:** “Hash rotation protects UK merchants and payers.”

---

## 4. Evidence / Storage Map

| Step | Artifact | Location |
|:--|:--|:--|
| Clause | Citation + screenshot | `/docs/clauses_controls_tests.md` |
| Control | Architecture diagram annotation | `/docs/diagram_mermaid.mmd` |
| Test | Pytest file + CI log | `/tests/ethics/` |
| Ledger | Entry + owner | `/docs/ethics_debt_ledger.md` |
