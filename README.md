# SP-to-dbt Migration Kit

> **Specification-Driven Development (SDD) kit for migrating Snowflake Stored Procedures to dbt models.**

Clone this repository into your Snowflake dbt project to get a complete, structured migration framework — powered by Snowflake Cortex AI.

---

## Quick Start

### 1. Clone into your dbt project

```bash
git clone https://github.com/<your-org>/sdd-dbt-migration-kit.git .specify
```

Or copy the `specs/` and `.specify/` folders into your existing dbt project root.

### 2. Project structure after setup

```
your_dbt_project/
├── dbt_project.yml
├── models/
│   ├── staging/
│   ├── intermediate/
│   └── marts/
├── .specify/
│   └── memory/
│       └── constitution.md        # Governing rules for all migrations
├── specs/
│   └── 001-sp-to-dbt-migration/
│       ├── spec.md                # What we're building
│       ├── plan.md                # How we'll build it
│       ├── tasks.md               # Step-by-step task breakdown
│       ├── clarify.md             # Questions & decisions log
│       └── checklists/
│           └── requirements.md    # PR compliance checklist
└── docs/
    └── migration-approach.md      # Client-facing overview
```

### 3. Follow the SDD workflow

```
clarify → spec → plan → tasks → implement → validate
```

| Phase | File | Purpose |
|-------|------|---------|
| 1. Clarify | `specs/.../clarify.md` | Resolve all questions before starting |
| 2. Specify | `specs/.../spec.md` | Define what "done" looks like |
| 3. Plan | `specs/.../plan.md` | Design the 5-phase migration approach |
| 4. Tasks | `specs/.../tasks.md` | Break work into trackable tasks |
| 5. Implement | `models/` | Build dbt models per spec |
| 6. Validate | `tests/audit/` | Prove parity with legacy output |

---

## What's Included

| File | Purpose |
|------|---------|
| **constitution.md** | Non-negotiable rules: modularity, layered architecture, naming, testing, CI/CD |
| **spec.md** | Full specification: scope, architecture, naming conventions, testing requirements |
| **plan.md** | 5-phase implementation plan: Discovery → Prioritization → Design → Build → Validate |
| **tasks.md** | Granular task breakdown with priorities, owners, and status tracking |
| **clarify.md** | Structured Q&A checklist covering all ambiguities before work begins |
| **requirements.md** | PR compliance checklist — use on every pull request |
| **migration-approach.md** | Client-facing summary of the approach |

---

## How It Works

```
┌──────────────┐     ┌──────────────────┐     ┌───────────────────┐
│  Input SPs   │────>│  Snowflake       │────>│  dbt Project      │
│  (SQL logic) │     │  Cortex AI       │     │  (models, tests,  │
│              │     │  (guided by SDD) │     │   sources, docs)  │
└──────────────┘     └──────────────────┘     └───────────────────┘
       ↑                     ↑                         ↓
   Spec defines          Guided by              Validated against
   what to convert     constitution +           original SP output
                       completeness contract    (round-trip check)
```

- **All conversions run inside Snowflake** — no external AI, no data leaves the account
- **Spec-driven** — constitution + contracts are embedded in the Cortex prompt
- **Auditable** — every SP step maps to a dbt artifact with full traceability

---

## Requirements

- Snowflake account with Cortex AI enabled
- dbt Core or dbt Cloud


---

## License

MIT
