# dbt Stored Procedure Migration Constitution

## Project Vision

This constitution governs the enterprise-scale migration of stored procedures to dbt, transforming legacy procedural transformation logic into modular, testable, and maintainable dbt models. The goal is to move from source-conformed data shaped by external systems to business-conformed data shaped by organizational needs and definitions.

---

## Core Principles

### I. Modularity-First (NON-NEGOTIABLE)

**Every stored procedure must be decomposed into atomic, reusable dbt models.**

- Each business object is defined in a separate model (e.g., `orders`, `customers`, `payments`)
- Models are grouped into layers reflecting transformation progression: **staging → intermediate → marts**
- No monolithic models replicating stored procedure complexity
- Each transformation applies in exactly one place — no duplicate logic
- Models must be independently testable and documentable
- Direct copy-paste of stored procedure code is **PROHIBITED**; logic must be refactored

### II. Layered Architecture (NON-NEGOTIABLE)

**Enforce strict folder structure and model layering.**

```
models/
├── staging/           # 1:1 with source tables, light transformations only
│   ├── {source_name}/
│   │   ├── __{source_name}__sources.yml
│   │   ├── __{source_name}__models.yml
│   │   └── stg_{source_name}__{entity}.sql
├── intermediate/      # Business logic, joins, aggregations
│   ├── {domain}/
│   │   ├── __int_{domain}__models.yml
│   │   └── int_{entity}_{transformation}.sql
├── marts/             # Business-ready, consumption-layer models
│   ├── {domain}/
│   │   ├── __{domain}__models.yml
│   │   └── {entity}.sql
└── utilities/         # Date spines, helper models
```

**Layer Rules:**
- **Staging**: Only `source()` references allowed; materialized as views; rename, cast, basic cleanup only
- **Intermediate**: Only `ref()` to staging or other intermediate; encapsulate complex business logic
- **Marts**: Only `ref()` to intermediate or staging; wide, denormalized, business-conformed entities

### III. Naming Conventions (NON-NEGOTIABLE)

| Component | Convention | Example |
|-----------|-----------|---------|
| Staging models | `stg_{source}__{entity}` | `stg_stripe__payments` |
| Base models | `base_{source}__{entity}` | `base_jaffle_shop__deleted_customers` |
| Intermediate models | `int_{entity}_{transformation}` | `int_payments_pivoted_to_orders` |
| Mart models | `{entity}` (simple, business-facing) | `orders`, `customers` |
| Sources YAML | `__{source}__sources.yml` | `__stripe__sources.yml` |
| Models YAML | `__{domain}__models.yml` | `__finance__models.yml` |
| Macros | `{verb}_{noun}` | `cents_to_dollars`, `get_fiscal_year` |
| Tests | `assert_{condition}` | `assert_positive_total_amount` |

**SQL/Column Naming:**
- Use `snake_case` for all identifiers
- Boolean columns: prefix with `is_`, `has_`, `was_`
- Timestamp columns: suffix with `_at` (e.g., `created_at`)
- Date columns: suffix with `_date` (e.g., `order_date`)
- ID columns: suffix with `_id` (e.g., `customer_id`)
- Price/revenue columns: suffix with currency/unit (e.g., `price_usd`, `amount_cents`)

### IV. Testing Strategy (NON-NEGOTIABLE)

| Layer | Minimum Required Tests |
|-------|----------------------|
| Staging | `unique`, `not_null` on primary keys; `accepted_values` where applicable |
| Intermediate | Referential integrity; business rule validations |
| Marts | Schema tests; data quality checks; row count validations |

**Migration Validation (CRITICAL):**
- Use `audit_helper.compare_relations` for row-by-row validation
- Validate row counts match within acceptable threshold
- Document any intentional deviations from legacy output

### V. Version Control & CI/CD (NON-NEGOTIABLE)

- All changes go through pull requests with mandatory code review
- Branch naming: `feature/{ticket-id}-{description}`, `fix/{ticket-id}-{description}`
- Commit messages: `[{ticket-id}] {type}: {description}`
- No direct commits to `main` or `production` branches
- CI pipeline: `dbt build --select state:modified+` on every PR
- All tests must pass before merge; sqlfluff must pass with zero violations

### VI. Documentation & Transparency (NON-NEGOTIABLE)

- Model description explaining business purpose
- Column descriptions for all columns
- Source freshness definitions where applicable
- Lineage must be traceable from source to mart
- Document which stored procedure(s) each model replaces

### VII. Performance & Materialization Strategy

| Materialization | Use Case | Default Layer |
|----------------|----------|---------------|
| `view` | Small data, always-fresh needed | Staging |
| `table` | Large data, complex transforms | Marts |
| `incremental` | Very large data, append-mostly | High-volume marts |
| `ephemeral` | Helper CTEs, reduce warehouse cost | Intermediate (selective) |

### VIII. Migration Methodology

**Phase 1: Discovery & Assessment** — Catalog SPs, score complexity, map dependencies

**Phase 2: Prioritization** — Start with high-impact, low-complexity (quick wins)

**Phase 3: Design & Planning** — Map SP logic to dbt model structure before code

**Phase 4: Implementation** — Build staging → intermediate → mart models

**Phase 5: Validation & Cutover** — Parallel run, audit comparison, stakeholder sign-off

---

## Procedural-to-Declarative Translation Rules

| SP Construct | dbt Equivalent |
|---|---|
| `CREATE TABLE` + `INSERT` | `SELECT` statement (model file) |
| `UPDATE SET col = expr` | `CASE WHEN` in SELECT |
| `DELETE WHERE condition` | `WHERE NOT condition` filter |
| `TRUNCATE + INSERT` (full reload) | `materialized='table'` |
| Parametric `DELETE + INSERT` | `materialized='incremental'` + `delete+insert` |
| `MERGE` | `materialized='incremental'` + `merge` |
| Procedure parameters | `{{ var('param_name') }}` |
| Hard-coded table names | `{{ source() }}` and `{{ ref() }}` |
| Temp tables / CTEs | CTEs within model or `ephemeral` intermediate |
| Cursors / WHILE loops | Set-based window functions, recursive CTEs |

---

## Required Packages

```yaml
packages:
  - package: dbt-labs/dbt_utils
    version: [">=1.0.0", "<2.0.0"]
  - package: dbt-labs/audit_helper
    version: [">=0.9.0", "<1.0.0"]
  - package: dbt-labs/codegen
    version: [">=0.12.0", "<1.0.0"]
  - package: calogica/dbt_expectations
    version: [">=0.10.0", "<1.0.0"]
```

---

## Governance

- This constitution supersedes all legacy stored procedure practices for migrated workloads
- Amendments require: documentation of change, team review, migration plan for existing models
- All PRs must include constitution compliance verification
- Exceptions require explicit documentation and approval

---

**Version**: 1.0.0 | **Ratified**: 2026-04-24
