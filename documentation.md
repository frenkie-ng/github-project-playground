# github-project-playground — AI Agent Context

## Project Purpose

This project is a playground for learning finance and investment, specifically tailored for Gen Z.
It follows the **Lean Startup** model and focuses on **Micro-learning** and **Gamification**.
All backend and frontend implementation derives from documents in `./docs/`.

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
| Epic folder | `EPIC-{3d} - {Name}` | `EPIC-003 - Treasury Management` |
| User Story file | `US-{EPIC}.{3d} - {Name}.md` | `US-003.001 - Wallet Management.md` |
| Technical Design file | `US-{EPIC}.{3d}.md` | `US-003.001.md` (in `features/[EPIC]/technical-design/`) |
| UI/UX Design file | `US-{EPIC}.{3d}-ui.md` | `US-003.001-ui.md` (in `features/[EPIC]/`) |
| Test Case file | `US-{EPIC}.{3d}-test.md` | `US-003.001-test.md` (in `features/[EPIC]/`) |
| Task ID | `TASK-{EPIC}.{US}.{3d}` | `TASK-003.001.001` |
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

## Writing Standards

- Headings: `#` → `##` → `###` (hierarchical, no skipping levels)
- File names: kebab-case for all documentation files
- Images: `./docs/assets/` with relative links
- Mermaid labels with `(`, `)`, `@`, `<`, `>` must be quoted: `NodeA["Label (detail)"]`
- Add `%% accTitle: [description]` as first line of every Mermaid block
- Primary key convention: `id uuid DEFAULT gen_random_uuid() PRIMARY KEY`
- Workspace-scoped tables include `workspace_id uuid NOT NULL REFERENCES workspaces(id)`
- Timestamps: `created_at`, `updated_at`; add `deleted_at` for soft-delete entities

---

## Current Status (init)

| Epic | Requirements | Technical Design | UI/UX | Test Case |
|---|---|---|---|---|
| EPIC-001 Academy | ⬜ | ⬜ | ⬜ | ⬜ |
| EPIC-002 Smart Tools | ⬜ | ⬜ | ⬜ | ⬜ |
| EPIC-003 AI Mentor | ⬜ | ⬜ | ⬜ | ⬜ |
| EPIC-004 Smart Tracking | ⬜ | ⬜ | ⬜ | ⬜ |
| EPIC-005 Gamification | ⬜ | ⬜ | ⬜ | ⬜ |
| EPIC-006 Financial Roadmap | ⬜ | ⬜ | ⬜ | ⬜ |
