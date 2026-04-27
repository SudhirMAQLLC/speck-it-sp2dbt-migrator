# PLAN — Stored Procedure to dbt Migration

> **Spec-Driven Development Implementation Plan**
> Derived from: [constitution.md](../../.specify/memory/constitution.md)
> Pre-requisite: All [clarify.md](clarify.md) items resolved
> Version: 1.0.0 | Date: 2026-04-24

---

## Execution Overview

The migration follows the constitution's **5-phase methodology**, with each stored procedure progressing through all phases independently.

```
Phase 1          Phase 2           Phase 3          Phase 4            Phase 5
Discovery   →   Prioritization  →  Design     →    Implementation  →  Validation
& Assessment                       & Planning                         & Cutover
```

---

## Phase 1: Discovery & Assessment

### Objective
Catalog all stored procedures, score complexity, map dependencies, identify eliminations.

### Steps

1. **Inventory all stored procedures**
   - Extract procedure list from source database catalog
   - Record: name, database, schema, last modified date, execution frequency

2. **Analyze each procedure**
   - Document inputs (source tables, parameters)
   - Document outputs (target tables, result sets)
   - Map data flow: source → transformations → target

3. **Score complexity**
   - Low: Single source, simple transforms, <100 lines
   - Medium: Multiple sources, joins/aggregations, 100–500 lines
   - High: Dynamic SQL, cursors, cross-database, >500 lines

4. **Identify eliminations**
   - Flag procedures no longer called or scheduled
   - Get stakeholder confirmation before removing from scope

5. **Map stakeholder ownership**
   - Assign business owner and technical SME per procedure

### Deliverables
- [ ] Stored procedure inventory
- [ ] Complexity scoring matrix
- [ ] Data flow diagrams per procedure
- [ ] Stakeholder ownership map
- [ ] Elimination list (approved)

---

## Phase 2: Prioritization

### Objective
Order the migration backlog for maximum early value and team learning.

### Prioritization Matrix

```
                    HIGH IMPACT
                        │
         Quick Wins     │    Strategic
         (DO FIRST)     │    (DO SECOND)
                        │
  LOW COMPLEXITY ───────┼─────── HIGH COMPLEXITY
                        │
         Low Priority   │    Complex/Risky
         (DO LAST)      │    (DO THIRD)
                        │
                    LOW IMPACT
```

### Deliverables
- [ ] Prioritized migration backlog
- [ ] Dependency graph
- [ ] Sprint/iteration assignment per procedure

---

## Phase 3: Design & Planning (Per Procedure)

### Objective
Map stored procedure logic to dbt model structure before writing any code.

### Steps

1. Map source tables to staging models
2. Design intermediate models (break complex logic into discrete steps)
3. Design mart models (wide, denormalized, consumption-ready)
4. Identify reusable macros
5. Design test suite (staging / intermediate / mart / audit)
6. Document the design (DAG fragment, column mapping, translation decisions)

### Deliverables (per procedure)
- [ ] Model decomposition diagram
- [ ] Source → staging → intermediate → mart mapping
- [ ] Column mapping document
- [ ] Test plan
- [ ] Macro identification list

---

## Phase 4: Implementation (Per Procedure)

### Step 4.1: Project Scaffolding (once)

```
dbt_project/
├── dbt_project.yml
├── packages.yml
├── .sqlfluff
├── models/
│   ├── staging/
│   ├── intermediate/
│   └── marts/
├── macros/
├── tests/
├── seeds/
└── docs/
```

### Step 4.2: Build Staging Models
- Create source YAML with freshness definitions
- Create `stg_{source}__{entity}.sql` per source table
- Add `unique`, `not_null` tests on PKs

### Step 4.3: Build Intermediate Models
- Translate procedural logic to declarative SELECT
- Replace parameters with `{{ var() }}`
- Replace hard-coded tables with `{{ ref() }}` / `{{ source() }}`

### Step 4.4: Build Mart Models
- Wide, denormalized output matching business needs
- Configure materialization: `table` or `incremental`

### Step 4.5: Write Tests
- Schema tests, data quality checks, row count validations
- `audit_helper.compare_relations` for migration parity

### Step 4.6: Lint & Document
- `sqlfluff lint` — zero violations
- `dbt docs generate` — review lineage

### Deliverables (per procedure)
- [ ] All model SQL files passing
- [ ] All YAML files with descriptions and tests
- [ ] All tests passing
- [ ] sqlfluff passing
- [ ] Documentation generated
- [ ] Audit tests configured

---

## Phase 5: Validation & Cutover (Per Procedure)

### Steps

1. **Parallel execution** — run legacy SP and dbt model side-by-side
2. **Validate results** — row count, column-level comparison, edge cases
3. **Document deviations** — any intentional differences with rationale
4. **Stakeholder sign-off** — formal approval to cutover
5. **Cutover execution** — redirect consumers, keep rollback available
6. **Decommission legacy** — after burn-in, archive legacy SP

### Deliverables (per procedure)
- [ ] Audit comparison report
- [ ] Deviation documentation (if any)
- [ ] Stakeholder sign-off record
- [ ] Cutover executed
- [ ] Legacy procedure decommissioned

---

## CI/CD Pipeline

```
PR Created
    ├── dbt deps
    ├── sqlfluff lint
    ├── dbt build --select state:modified+
    ├── dbt docs generate
    ▼
Review & Merge to main
    ├── dbt build (staging env)
    ▼
Deploy to production
    ├── dbt build (production env)
    ├── Source freshness checks
    ├── Monitoring & alerts
```

---

## Risk Mitigation

| Risk | Mitigation |
|------|-----------|
| Logic drift during migration | `audit_helper` parity validation |
| Monolithic model creation | Code review enforcing decomposition |
| Naming inconsistency | sqlfluff + PR review checklist |
| Production data corruption | Environment separation, parallel runs |
| Missing test coverage | CI gate: all tests must pass |

---

**Next Step**: Execute tasks per [tasks.md](tasks.md)
**Governing Document**: [constitution.md](../../.specify/memory/constitution.md)
