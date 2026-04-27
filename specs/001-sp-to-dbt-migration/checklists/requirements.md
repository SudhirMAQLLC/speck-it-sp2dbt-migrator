# Requirements Checklist — SP to dbt Migration

> Use this checklist on every Pull Request to verify constitution compliance.
> Derived from: [constitution.md](../../../.specify/memory/constitution.md)

---

## PR Compliance Checklist

### Architecture
- [ ] Models are in the correct layer (`staging/`, `intermediate/`, `marts/`)
- [ ] Staging models reference only `source()` — no `ref()`
- [ ] Intermediate/mart models reference only `ref()` — no `source()`
- [ ] No monolithic models — each model = one business object or transformation step
- [ ] No direct copy-paste of stored procedure code — logic is refactored

### Naming
- [ ] Staging: `stg_{source}__{entity}`
- [ ] Intermediate: `int_{entity}_{transformation}`
- [ ] Marts: `{entity}` (business-facing name)
- [ ] Columns: `snake_case`, proper prefixes/suffixes (`is_`, `_at`, `_id`, `_usd`)
- [ ] YAML files follow `__{scope}__models.yml` / `__{source}__sources.yml`

### Testing
- [ ] Staging: `unique`, `not_null` on primary keys
- [ ] Intermediate: Referential integrity, business rule tests
- [ ] Marts: Schema tests, data quality checks
- [ ] Audit: `audit_helper.compare_relations` configured for parity validation
- [ ] All tests pass: `dbt test`

### Documentation
- [ ] Model description: purpose, grain, update frequency
- [ ] Column descriptions: all columns documented
- [ ] Migration mapping: which SP(s) this model replaces
- [ ] Source freshness defined where applicable

### Code Quality
- [ ] `sqlfluff lint` — zero violations
- [ ] No `SELECT *` in final models
- [ ] Leading commas in column lists
- [ ] CTEs preferred over subqueries
- [ ] `snake_case` for all SQL identifiers

### Materialization
- [ ] Staging: `view`
- [ ] Intermediate: `ephemeral` (or `view` if reused/debuggable)
- [ ] Marts: `table` or `incremental`
- [ ] Incremental models have `unique_key` and merge strategy defined

### CI/CD
- [ ] Feature branch follows naming convention
- [ ] Commit message follows format: `[{ticket-id}] {type}: {description}`
- [ ] `dbt build --select state:modified+` passes
- [ ] `dbt docs generate` succeeds

---

**Governing Document**: [constitution.md](../../../.specify/memory/constitution.md)
