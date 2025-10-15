# ⭐ Ethical Operations Badge

**Selected Mapping:** India — DPDP Act §7 (Consent & Lawful Purpose)  
**Stakeholder:** Unbanked / opt-out Indian customer  
**Harm:** Unlawful processing without valid consent

---

## 🔴 Before (Red-Bar Failure)

```bash
pytest tests/ethics/test_consent_in.py -q
F                                                                    [100%]
================================ FAILURES =================================
Harm: unlawful processing → {'U123'}
1 failed, 0 passed
```

---

## 🟢 After (Green-Bar Success)

Applied Glue pre-join filter using consent registry table.

```bash
pytest tests/ethics/test_consent_in.py -q
.                                                                    [100%]
1 passed in 0.23s
```

**Ledger Update:**  
Ethics Debt Ledger entry → “Consent withdrawal exclusion (DPDP)”  
**Who it protects:** Indian opt-out users  
**Status changed:** 🔴 → 🟢 _(2025-10-13)_
