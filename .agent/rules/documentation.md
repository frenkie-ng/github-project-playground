---
trigger: always_on
---

# github-project-playground — AI Agent Context

## Project Purpose

This repo is the **source of truth** for the Finance Education & Toolkit platform for Gen Z.
All backend and frontend implementation derives from documents in `./docs/`.

Cross-repo structure:
```
daily-tips-Root/
├── github-project-playground/  ← YOU ARE HERE — specs drive everything
├── Daily-Tips-Backend/
└── Daily-Tips-Frontend/
```

---

## Directory Structure

```
docs/
├── requirement/            # Normalized requirements — Epic > User Story > Task
├── techinical-base/        # Core technology documentation
├── architecture/           # System architecture + feature-level architecture
├── features/               # 1-1 with requirement — TD + UI/UX + Test Case
│   ├── base-design.md      # ← LIVING REGISTRY: services, tables, routes, events
│   └── EPIC-XXX - .../
│       └── technical-design/
│           └── US-XXX.YYY.md
└── technical-design/       # (legacy, use features/ going forward)
```

**`docs/features/base-design.md` is the most critical file.** Always read Sections 1–5 before writing any Technical Design.

---

## Naming Conventions

| Artifact | Format | Example |
|---|---|---|
| Epic folder | `EPIC-{3d} - {Name}` | `EPIC-001 - Academy` |
| User Story file | `US-{EPIC}.{3d} - {Name}.md` | `US-001.001 - Affiliate Marketing Roadmap.md` |
| Technical Design file | `US-{EPIC}.{3d}.md` | `US-001.001.md` (in `features/[EPIC]/technical-design/`) |
| UI/UX Design file | `US-{EPIC}.{3d}-ui.md` | `US-001.001-ui.md` (in `features/[EPIC]/`) |
| Test Case file | `US-{EPIC}.{3d}-test.md` | `US-001.001-test.md` (in `features/[EPIC]/`) |
| Task ID | `TASK-{EPIC}.{US}.{3d}` | `TASK-001.001.001` |
| Tech base folder | `docs/techinical-base/[tech-name]/README.md` | `docs/techinical-base/supabase/README.md` |

---

## Documentation Flow

```
Requirement (Step 1)
  └─► Technical Base (Step 2) ─► Architecture (Step 3)
        └─► Technical Design (Step 4a) ─┐
        └─► UI/UX Design (Step 4b) ─────┼─► Sync ─► Test Case (Step 5)
                                         └───────────► GitHub Issues (Step 6)
```

Each step has a dedicated skill. **Never skip steps.**

---

## Available Skills

Skills live in `.agent/skills/`. Invoke by reading the corresponding `SKILL.md`.

| Step | Skill path | Trigger |
|---|---|---|
| 1 | `.agent/skills/analysis-requirement-documentation/SKILL.md` | New/updated requirements from Notion or user input |
| 2 | `.agent/skills/research-technology-documentation/SKILL.md` | New technology added to the stack |
| 3 | `.agent/skills/architecture-documentation/SKILL.md` | New Epic or major architecture change |
| 4a | `.agent/skills/technical-design/SKILL.md` | Writing a Technical Design document |
| 4b | `.agent/skills/ui-design-documentation/SKILL.md` | Writing UI/UX Design documentation |
| 5 | `.agent/skills/test-case-documentation/SKILL.md` | Writing Test Cases (only after 4a+4b are synced) |
| 6 | `.agent/skills/create-github-issues/SKILL.md` | Creating GitHub Issues from a completed feature spec |
| Maintenance | `.agent/skills/technical-design/safe-technical-design-maintenance/SKILL.md` | Deleting or rewriting a TD safely |

### Full Workflow

To run the full end-to-end feature flow, read `.agent/workflows/new-feature.md`.

---

## Key Constraints

1. **Read `docs/features/base-design.md` Sections 1–5 before any Technical Design work** — check for existing service names, table names, API routes, and event types to avoid conflicts.
2. **Every feature file under `docs/features/` must backlink to its requirement.** Add `> **Requirements**: [US-XXX.YYY](../../../requirement/...)` at the top.
3. **All diagrams use Mermaid.js** — no static images. Wrap in fenced `mermaid` blocks. Add `accTitle:` for accessibility.
4. **Language**: Content in Vietnamese (for team reading) + English (for AI tooling and technical precision). Technical terms, IDs, and code stay in English.
5. **Do not invent requirements** — only normalize/structure what the user provides.
6. **Technical Design and UI/UX Design must be synced before writing Test Cases.** Check both documents are consistent before proceeding to Step 5.
7. **`base-design.md` must be updated** after every new Technical Design is finalized — append new services, tables, routes, and events to the registry sections.

---

## Writing Standards

- Headings: `#` → `##` → `###` (hierarchical, no skipping levels)
- File names: kebab-case for all documentation files
- Images: `./docs/assets/` with relative links
- Mermaid labels with `(`, `)`, `@`, `<`, `>` must be quoted: `NodeA["Label (detail)"]`
- Add `%% accTitle: [description]` as first line of every Mermaid block
- Primary key convention: `id uuid DEFAULT gen_random_uuid() PRIMARY KEY`
- User-scoped tables include `user_id uuid NOT NULL REFERENCES auth.users(id)`
- Timestamps: `created_at`, `updated_at`; add `deleted_at` for soft-delete entities

## Mermaid Diagram Types

| Type | When to use |
|---|---|
| `flowchart TD` | Business logic, user journeys, decision trees (top-down) |
| `flowchart LR` | Simple linear processes (left-to-right) |
| `sequenceDiagram` | API interactions, auth flows, microservice communication |
| `erDiagram` | Database schema, data relationships |

---

## Current Status (as of 2026-03)

| Epic | Requirements | Technical Design | UI/UX | Test Case |
|---|---|---|---|---|
| EPIC-001 Academy | ✅ | ⬜ | ⬜ | ⬜ |
| EPIC-002 Smart Tools | ✅ | ⬜ | ⬜ | ⬜ |
| EPIC-003 AI Mentor | ✅ | ⬜ | ⬜ | ⬜ |
| EPIC-004 Smart Tracking | ✅ | ⬜ | ⬜ | ⬜ |
| EPIC-005 Gamification | ✅ | ⬜ | ⬜ | ⬜ |
| EPIC-006 Financial Roadmap | ✅ | ⬜ | ⬜ | ⬜ |
