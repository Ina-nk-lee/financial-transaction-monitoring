# Project 2 Design Brief — Financial Transaction Monitoring

1. Scenario Overview & Scope

This project designs an overnight batch data pipeline to detect anomalous payment activity across Canada, India, and the UK.
Each night, ~2 TB of transaction logs arrive from regional payment processors.
The pipeline aggregates and scores transactions to flag possible anti-money-laundering (AML) events while meeting privacy, regulatory, and cost constraints.

Batch window: 22:00 → 06:00 local time
SLA: p95 completion ≤ 6 hours per run
Cost ceiling: ≤ $500 / month (@ AWS educational tier)
Scope: Design-only — no code execution required; prototypes optional.
