# CLARIFY — Stored Procedure to dbt Migration

> **Spec-Driven Development Clarification Checklist**
> Derived from: [constitution.md](../../.specify/memory/constitution.md)
> Version: 1.0.0 | Date: 2026-04-24

---

## Purpose

This document captures all questions, ambiguities, assumptions, and decisions that must be resolved **before** implementation begins. Each item maps directly to a constitution principle. No work proceeds on an item until its clarifications are resolved.

---

## 1. Discovery & Inventory

### 1.1 Stored Procedure Catalog

- [ ] **Q**: What is the complete list of stored procedures in scope for migration?
- [ ] **Q**: Which database platform(s) host the current stored procedures?
- [ ] **Q**: Has each procedure been scored for complexity (low / medium / high)?
- [ ] **Q**: Which procedures are obsolete and can be eliminated from scope?
- [ ] **Q**: Are there stored procedures with cross-database or cross-server dependencies?

### 1.2 Source Systems

- [ ] **Q**: What are all source systems feeding into the stored procedures?
- [ ] **Q**: Is there a 1:1 mapping of source tables already documented?
- [ ] **Q**: Are source tables accessible from the dbt target warehouse?
- [ ] **Q**: Are there any CDC patterns in use that affect staging design?

---

## 2. Architecture & Platform

### 2.1 Target Warehouse

- [ ] **Q**: What is the target data warehouse? (Snowflake, BigQuery, Redshift, etc.)
- [ ] **Q**: What dbt adapter and version will be used?
- [ ] **Q**: Are there warehouse-specific features to leverage? (clustering, partitioning)
- [ ] **Q**: What are the warehouse compute/cost constraints?

### 2.2 Environment Setup

- [ ] **Q**: Are three environments (dev / staging / prod) provisioned?
- [ ] **Q**: What is the schema naming strategy per environment?
- [ ] **Q**: Will developers use individual schemas or shared development schemas?

---

## 3. Decomposition Decisions

### 3.1 Model Boundaries

- [ ] **Q**: For procedures with multiple business objects, how are model boundaries determined?
- [ ] **Q**: Are there shared transformation patterns that should become macros?
- [ ] **Q**: Which intermediate models are `ephemeral` vs. materialized?
- [ ] **Q**: Are there utility models needed? (date spines, fiscal calendars)

### 3.2 Procedural Pattern Translation

- [ ] **Q**: Do any procedures use cursors or row-by-row processing?
- [ ] **Q**: Are there dynamic SQL patterns that need macro-based solutions?
- [ ] **Q**: Do any procedures use temp tables spanning multiple steps?
- [ ] **Q**: Are there error-handling / TRY-CATCH patterns needing alternatives?
- [ ] **Q**: Do any procedures have side effects (logging, notifications)?

---

## 4. Data Quality & Testing

### 4.1 Validation Thresholds

- [ ] **Q**: What is the acceptable row count deviation threshold? (0%? 0.1%?)
- [ ] **Q**: Are there known data quality issues that should NOT be replicated?
- [ ] **Q**: What precision tolerance applies for numeric comparisons?
- [ ] **Q**: How long should parallel validation run before cutover?

### 4.2 Source Freshness

- [ ] **Q**: What are the freshness SLAs for each source table?
- [ ] **Q**: Are there sources that update intra-day vs. daily?
- [ ] **Q**: Should freshness checks block downstream model runs?

---

## 5. CI/CD & Tooling

### 5.1 Version Control

- [ ] **Q**: What Git platform is in use? (GitHub, Azure DevOps, GitLab)
- [ ] **Q**: Is there an existing CI/CD pipeline?
- [ ] **Q**: What is the PR review process? (minimum reviewers, required checks)

### 5.2 Linting

- [ ] **Q**: Which SQL dialect for sqlfluff?
- [ ] **Q**: Any style overrides beyond the constitution's formatting guide?
- [ ] **Q**: Leading commas confirmed as standard?

---

## 6. Security & Governance

- [ ] **Q**: What roles/grants are needed for each schema?
- [ ] **Q**: Are there row-level security requirements?
- [ ] **Q**: How are secrets/credentials managed?
- [ ] **Q**: Are there compliance requirements (SOX, HIPAA, GDPR)?

---

## 7. Stakeholders & Ownership

- [ ] **Q**: Who are the SP subject matter experts?
- [ ] **Q**: Who owns validation sign-off for each migrated procedure?
- [ ] **Q**: Who approves intentional deviations from legacy output?
- [ ] **Q**: What is the escalation path for blocked migrations?
- [ ] **Q**: What is the rollback plan if a cutover fails?

---

## 8. Prioritization

- [ ] **Q**: Has the prioritization matrix been completed?
- [ ] **Q**: Which procedures are quick wins?
- [ ] **Q**: Are there hard deadlines driving specific migrations?
- [ ] **Q**: Are there dependency chains (Procedure A must migrate before B)?

---

## Assumptions Log

| # | Assumption | Status | Resolution |
|---|-----------|--------|------------|
| A1 | Source tables are accessible from target warehouse | Unverified | |
| A2 | Three environments (dev/staging/prod) are available | Unverified | |
| A3 | Git repository exists with branch protection | Unverified | |
| A4 | Team has dbt Core/Cloud licenses | Unverified | |
| A5 | sqlfluff is the agreed linter | Assumed per constitution | |

---

## Decision Log

| # | Decision | Date | Decided By | Rationale |
|---|----------|------|-----------|-----------|
| D1 | | | | |

---

**Status**: All items must be resolved before proceeding to [plan.md](plan.md)
**Governing Document**: [constitution.md](../../.specify/memory/constitution.md)
