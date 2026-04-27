# SP-to-dbt Migration Approach

## Summary

We migrate Snowflake Stored Procedures into dbt models using an **AI-assisted, specification-driven approach** — powered entirely within the Snowflake ecosystem.

---

## Spec-Driven Methodology

Nothing gets converted without a specification first. The SDD artifacts govern every conversion:

| Artifact | Purpose |
|---|---|
| **Constitution** | Non-negotiable rules for all migrations |
| **Spec** | What "done" looks like — scope, architecture, acceptance criteria |
| **Plan** | 5-phase approach: Discovery → Prioritize → Design → Build → Validate |
| **Tasks** | Granular task breakdown with priorities and status tracking |
| **Clarify** | All questions resolved before work begins |

---

## How It Works

```
┌──────────────┐     ┌──────────────────┐     ┌───────────────────┐
│  Input SPs   │────>│  Snowflake       │────>│  dbt Project      │
│  (SQL logic) │     │  Cortex AI       │     │  (models, tests,  │
│              │     │  (guided by SDD) │     │   sources, docs)  │
└──────────────┘     └──────────────────┘     └───────────────────┘
```

- **All conversions run inside Snowflake** — no external AI, no data leaves the account
- **Spec-driven** — constitution + contracts embedded in the AI prompt
- **Auditable** — every SP step maps to a dbt artifact with full traceability

---

## What Gets Generated

| Artifact | Purpose |
|---|---|
| Source declarations | Where the data comes from |
| Staging models | Light transformations of source tables |
| Intermediate models | Business logic — joins, aggregations, overlays |
| Mart models | Final reporting tables (replacing SP targets) |
| Schema files | Column docs and data quality tests |
| Step map | Traceability from every SP step to its dbt model |

---

## Key Safeguards

| Safeguard | Purpose |
|---|---|
| Completeness Contract | Every SP step covered — no silent logic drops |
| Column Preservation | All columns carried through — no silent drops |
| Round-Trip Validation | dbt output compared against original SP target |
| Automated Post-Processing | Catches common AI errors deterministically |
| Idempotent Models | Re-running produces identical results |
