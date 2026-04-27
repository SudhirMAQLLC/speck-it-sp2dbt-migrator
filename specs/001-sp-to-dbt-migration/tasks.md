# TASKS — Stored Procedure to dbt Migration

> **Spec-Driven Development Task Breakdown**
> Derived from: [constitution.md](../../.specify/memory/constitution.md)
> Implements: [plan.md](plan.md) · Validated by: [spec.md](spec.md)
> Version: 1.0.0 | Date: 2026-04-24

---

## Task Legend

| Status | Meaning |
|--------|---------|
| `[ ]` | Not started |
| `[~]` | In progress |
| `[x]` | Complete |
| `[!]` | Blocked — see notes |

**Priority**: P0 (critical path) · P1 (high) · P2 (medium) · P3 (low)

---

## PHASE 1: Discovery & Assessment

### T1.1 — Stored Procedure Inventory

| # | Task | Priority | Owner | Status | Notes |
|---|------|----------|-------|--------|-------|
| T1.1.1 | Extract complete list of stored procedures from source database(s) | P0 | | [ ] | Query `INFORMATION_SCHEMA` |
| T1.1.2 | Record metadata: name, database, schema, last modified, execution frequency | P0 | | [ ] | |
| T1.1.3 | Identify and flag obsolete/unused procedures | P1 | | [ ] | Requires stakeholder confirmation |
| T1.1.4 | Get stakeholder approval on elimination list | P1 | | [ ] | Blocked on T1.1.3 |

### T1.2 — Procedure Analysis

| # | Task | Priority | Owner | Status | Notes |
|---|------|----------|-------|--------|-------|
| T1.2.1 | Document inputs (source tables, parameters) per procedure | P0 | | [ ] | |
| T1.2.2 | Document outputs (target tables, result sets) per procedure | P0 | | [ ] | |
| T1.2.3 | Map internal dependencies (procedure-to-procedure calls, temp tables) | P0 | | [ ] | |
| T1.2.4 | Create data flow diagrams per procedure | P1 | | [ ] | |

### T1.3 — Complexity Scoring

| # | Task | Priority | Owner | Status | Notes |
|---|------|----------|-------|--------|-------|
| T1.3.1 | Define scoring rubric: Low / Medium / High | P0 | | [ ] | Per constitution Phase 1 |
| T1.3.2 | Score each procedure | P0 | | [ ] | Blocked on T1.2 |
| T1.3.3 | Identify patterns needing special translation (cursors, dynamic SQL) | P1 | | [ ] | |

---

## PHASE 2: Prioritization

### T2.1 — Prioritization Matrix

| # | Task | Priority | Owner | Status | Notes |
|---|------|----------|-------|--------|-------|
| T2.1.1 | Plot all procedures on Impact × Complexity matrix | P0 | | [ ] | Blocked on T1.3 |
| T2.1.2 | Identify Quick Wins (high impact, low complexity) | P0 | | [ ] | |
| T2.1.3 | Group procedures sharing source tables | P1 | | [ ] | |
| T2.1.4 | Map dependency chains | P1 | | [ ] | |
| T2.1.5 | Create ordered migration backlog | P0 | | [ ] | |

---

## PHASE 3: Design & Planning (Per Procedure)

### T3.1 — Source-to-Staging Mapping

| # | Task | Priority | Owner | Status | Notes |
|---|------|----------|-------|--------|-------|
| T3.1.1 | List all source tables referenced by the procedure | P0 | | [ ] | |
| T3.1.2 | Design staging model for each source | P0 | | [ ] | 1:1 with source tables |
| T3.1.3 | Define source YAML with freshness | P0 | | [ ] | |
| T3.1.4 | Check if staging model already exists (reuse) | P1 | | [ ] | |

### T3.2 — Intermediate Model Design

| # | Task | Priority | Owner | Status | Notes |
|---|------|----------|-------|--------|-------|
| T3.2.1 | Decompose procedure logic into discrete transformation steps | P0 | | [ ] | NO copy-paste |
| T3.2.2 | Name each intermediate model | P0 | | [ ] | |
| T3.2.3 | Decide materialization per model | P1 | | [ ] | |

### T3.3 — Mart Model Design

| # | Task | Priority | Owner | Status | Notes |
|---|------|----------|-------|--------|-------|
| T3.3.1 | Define mart entity (business-facing name) | P0 | | [ ] | |
| T3.3.2 | Design denormalized output schema | P0 | | [ ] | |
| T3.3.3 | Decide materialization | P1 | | [ ] | |

### T3.4 — Test Plan

| # | Task | Priority | Owner | Status | Notes |
|---|------|----------|-------|--------|-------|
| T3.4.1 | Define staging tests | P0 | | [ ] | |
| T3.4.2 | Define intermediate tests | P0 | | [ ] | |
| T3.4.3 | Define mart tests | P0 | | [ ] | |
| T3.4.4 | Design `audit_helper` parity test | P0 | | [ ] | CRITICAL |

---

## PHASE 4: Implementation (Per Procedure)

### T4.0 — Project Scaffolding (ONE-TIME)

| # | Task | Priority | Owner | Status | Notes |
|---|------|----------|-------|--------|-------|
| T4.0.1 | Initialize dbt project | P0 | | [ ] | |
| T4.0.2 | Configure `dbt_project.yml` per constitution | P0 | | [ ] | |
| T4.0.3 | Create `packages.yml` | P0 | | [ ] | |
| T4.0.4 | Run `dbt deps` | P0 | | [ ] | |
| T4.0.5 | Configure `.sqlfluff` | P0 | | [ ] | |
| T4.0.6 | Set up Git repository with branch protection | P0 | | [ ] | |
| T4.0.7 | Configure CI pipeline | P0 | | [ ] | |

### T4.1 — Build Staging Models

| # | Task | Priority | Owner | Status | Notes |
|---|------|----------|-------|--------|-------|
| T4.1.1 | Create source YAML | P0 | | [ ] | |
| T4.1.2 | Create staging model SQL | P0 | | [ ] | |
| T4.1.3 | Add tests: `unique`, `not_null` on PKs | P0 | | [ ] | |
| T4.1.4 | Run `dbt run --select staging` | P0 | | [ ] | |
| T4.1.5 | Run `sqlfluff lint models/staging/` | P0 | | [ ] | |

### T4.2 — Build Intermediate Models

| # | Task | Priority | Owner | Status | Notes |
|---|------|----------|-------|--------|-------|
| T4.2.1 | Translate procedure logic: `INSERT` → SELECT | P0 | | [ ] | |
| T4.2.2 | Translate: `UPDATE` → `CASE WHEN` | P0 | | [ ] | |
| T4.2.3 | Translate: `DELETE` → `WHERE` filter | P0 | | [ ] | |
| T4.2.4 | Replace hard-coded tables with `ref()` | P0 | | [ ] | |
| T4.2.5 | Create model YAML with descriptions | P0 | | [ ] | |
| T4.2.6 | Add tests | P0 | | [ ] | |

### T4.3 — Build Mart Models

| # | Task | Priority | Owner | Status | Notes |
|---|------|----------|-------|--------|-------|
| T4.3.1 | Create mart model SQL | P0 | | [ ] | |
| T4.3.2 | Configure materialization | P0 | | [ ] | |
| T4.3.3 | Create model YAML | P0 | | [ ] | |
| T4.3.4 | Add schema tests and data quality checks | P0 | | [ ] | |

### T4.4 — Audit & Validation

| # | Task | Priority | Owner | Status | Notes |
|---|------|----------|-------|--------|-------|
| T4.4.1 | Create `audit_helper.compare_relations` test | P0 | | [ ] | CRITICAL |
| T4.4.2 | Validate row counts | P0 | | [ ] | |
| T4.4.3 | Validate column-level parity | P0 | | [ ] | |
| T4.4.4 | Document intentional deviations | P0 | | [ ] | |

### T4.5 — PR & Review

| # | Task | Priority | Owner | Status | Notes |
|---|------|----------|-------|--------|-------|
| T4.5.1 | Create feature branch | P0 | | [ ] | |
| T4.5.2 | Open PR with compliance checklist | P0 | | [ ] | |
| T4.5.3 | CI pipeline passes | P0 | | [ ] | |
| T4.5.4 | Peer review approved | P0 | | [ ] | |
| T4.5.5 | Merge to `main` | P0 | | [ ] | |

---

## PHASE 5: Validation & Cutover (Per Procedure)

### T5.1 — Parallel Execution

| # | Task | Priority | Owner | Status | Notes |
|---|------|----------|-------|--------|-------|
| T5.1.1 | Schedule dbt model to run in parallel with legacy SP | P0 | | [ ] | |
| T5.1.2 | Run audit comparison on each parallel run | P0 | | [ ] | |

### T5.2 — Sign-off & Cutover

| # | Task | Priority | Owner | Status | Notes |
|---|------|----------|-------|--------|-------|
| T5.2.1 | Present validation report to business owner | P0 | | [ ] | |
| T5.2.2 | Get formal approval for cutover | P0 | | [ ] | |
| T5.2.3 | Document rollback plan | P0 | | [ ] | |
| T5.2.4 | Redirect consumers to dbt output | P0 | | [ ] | |
| T5.2.5 | Monitor during burn-in | P0 | | [ ] | |
| T5.2.6 | Decommission legacy SP | P1 | | [ ] | After burn-in |

---

## Constitution Compliance Checklist (Per PR)

- [ ] **Modularity**: No monolithic models
- [ ] **No Copy-Paste**: Logic refactored, not copied
- [ ] **Layered Architecture**: Models in correct layer
- [ ] **Layer References**: Staging = `source()` only; others = `ref()` only
- [ ] **Naming**: All conventions followed
- [ ] **Tests**: Required tests per layer present and passing
- [ ] **Audit**: `audit_helper` configured for parity
- [ ] **Documentation**: Model + column descriptions present
- [ ] **Linting**: `sqlfluff lint` zero violations
- [ ] **CI**: `dbt build --select state:modified+` passes

---

**Governing Document**: [constitution.md](../../.specify/memory/constitution.md)
