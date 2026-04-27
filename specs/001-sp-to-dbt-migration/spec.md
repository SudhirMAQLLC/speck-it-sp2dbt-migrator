# SPEC — Stored Procedure to dbt Migration

> **Specification-Driven Development Specification**
> Derived from: [constitution.md](../../.specify/memory/constitution.md)
> Version: 1.0.0 | Date: 2026-04-24

---

## 1. Objective

Migrate all enterprise stored procedures to dbt models, transforming legacy procedural transformation logic into modular, testable, and maintainable dbt models. The end state moves data from **source-conformed** (shaped by external systems) to **business-conformed** (shaped by organizational needs).

---

## 2. Scope

### In Scope

- All stored procedures performing data transformations (ETL/ELT)
- Decomposition of each procedure into atomic dbt models across **staging → intermediate → marts** layers
- Testing, documentation, and validation of every migrated model
- CI/CD pipeline setup for dbt project
- Parallel validation of legacy vs. dbt outputs using `audit_helper`

### Out of Scope

- Stored procedures identified as obsolete during Discovery (Phase 1)
- Application-layer stored procedures (CRUD, API-facing)
- Infrastructure/DBA maintenance procedures
- Data ingestion pipelines (upstream of dbt)

---

## 3. Architecture Specification

### 3.1 Layered Model Structure

```
models/
├── staging/           # 1:1 with source tables
│   ├── {source_name}/
│   │   ├── __{source_name}__sources.yml
│   │   ├── __{source_name}__models.yml
│   │   └── stg_{source_name}__{entity}.sql
├── intermediate/      # Business logic layer
│   ├── {domain}/
│   │   ├── __int_{domain}__models.yml
│   │   └── int_{entity}_{transformation}.sql
├── marts/             # Consumption layer
│   ├── {domain}/
│   │   ├── __{domain}__models.yml
│   │   └── {entity}.sql
└── utilities/
```

### 3.2 Layer Rules

| Layer | References Allowed | Materialization | Purpose |
|-------|-------------------|-----------------|---------|
| Staging | `source()` only | `view` | Rename, cast, basic cleanup. 1:1 with source tables |
| Intermediate | `ref()` to staging/intermediate | `ephemeral` | Complex business logic, joins, aggregations |
| Marts | `ref()` to intermediate/staging | `table` | Wide, denormalized, business-conformed entities |

### 3.3 Materialization Strategy

| Materialization | Criteria | Default Layer |
|----------------|----------|---------------|
| `view` | Small data, always-fresh needed | Staging |
| `table` | Large data, complex transforms | Marts |
| `incremental` | >1M rows, append-mostly patterns | High-volume marts |
| `ephemeral` | Helper CTEs, reduce warehouse cost | Intermediate (selective) |

---

## 4. Naming Conventions

| Component | Pattern | Example |
|-----------|---------|---------|
| Staging models | `stg_{source}__{entity}` | `stg_stripe__payments` |
| Intermediate models | `int_{entity}_{transformation}` | `int_payments_pivoted_to_orders` |
| Mart models | `{entity}` | `orders`, `customers` |
| Sources YAML | `__{source}__sources.yml` | `__stripe__sources.yml` |
| Models YAML | `__{domain}__models.yml` | `__finance__models.yml` |
| Macros | `{verb}_{noun}` | `cents_to_dollars` |

### Column Naming

- All identifiers: `snake_case`
- Booleans: `is_`, `has_`, `was_` prefix
- Timestamps: `_at` suffix
- Dates: `_date` suffix
- IDs: `_id` suffix
- Currency/units: suffix with unit (`_usd`, `_cents`)

---

## 5. Testing Requirements

### 5.1 Tests by Layer

| Layer | Required Tests |
|-------|---------------|
| Staging | `unique`, `not_null` on PKs; `accepted_values` where applicable |
| Intermediate | Referential integrity; business rule validations |
| Marts | Schema tests; data quality checks; row count validations |

### 5.2 Migration Validation

- Use `audit_helper.compare_relations` for row-by-row parity checks
- Row counts must match within documented acceptable thresholds
- All intentional deviations from legacy output must be documented and approved

---

## 6. Procedural-to-Declarative Translation Rules

| Stored Procedure Pattern | dbt Equivalent |
|--------------------------|---------------|
| `CREATE TABLE` + `INSERT` | `SELECT` statement (model file) |
| `UPDATE` statements | `CASE WHEN` logic in SELECT |
| `DELETE` statements | `WHERE` filters |
| Procedure parameters | `dbt var()` variables |
| Hard-coded table names | `source()` and `ref()` functions |
| Temp tables / CTEs | CTEs within model or `ephemeral` intermediate models |

---

## 7. Documentation Requirements

Every model must include:

- **Model description**: business purpose, grain, update frequency, business owner
- **Column descriptions**: all columns documented
- **Source freshness**: defined where applicable
- **Lineage**: traceable from source to mart
- **Migration mapping**: which stored procedure(s) each model replaces

---

## 8. CI/CD & Version Control

- All changes via pull requests with mandatory code review
- Branch naming: `feature/{ticket-id}-{description}`, `fix/{ticket-id}-{description}`
- CI runs: `dbt build --select state:modified+` on every PR
- Gates: all tests pass, sqlfluff zero violations, docs generated

---

## 9. Required Packages

| Package | Version | Purpose |
|---------|---------|---------|
| `dbt-labs/dbt_utils` | >=1.0.0, <2.0.0 | Utility macros |
| `dbt-labs/audit_helper` | >=0.9.0, <1.0.0 | Migration parity validation |
| `dbt-labs/codegen` | >=0.12.0, <1.0.0 | Code generation |
| `calogica/dbt_expectations` | >=0.10.0, <1.0.0 | Advanced testing |

---

## 10. Acceptance Criteria

- [ ] Every stored procedure is decomposed — no monolithic models
- [ ] No direct copy-paste of stored procedure code
- [ ] All models follow layered architecture (staging → intermediate → marts)
- [ ] All naming conventions enforced
- [ ] Every model has required tests per layer
- [ ] `audit_helper` parity validation passes for each migrated procedure
- [ ] Full documentation: model descriptions, column descriptions, migration mapping
- [ ] CI/CD pipeline operational with all gates passing
- [ ] sqlfluff linting passes with zero violations
- [ ] Parallel run validated before cutover
- [ ] Stakeholder approval on any intentional deviations from legacy output

---

## Migration Tracking

| Stored Procedure | Status | dbt Models | Owner | Validated | Notes |
|-----------------|--------|------------|-------|-----------|-------|
| _example_ | Not Started | TBD | TBD | No | |

---

**Governing Document**: [constitution.md](../../.specify/memory/constitution.md)
