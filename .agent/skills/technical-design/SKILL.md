---
name: Technical Design
description: Entry point for writing Technical Design documents on the Finance Education platform. Orchestrates the full workflow — pre-flight, writing, routing decisions, patterns, and post-write registry update.
---

# Technical Design Skill Group

This folder contains all skills needed to write a complete Technical Design document for the Finance Education platform.

**When given a task to write a Technical Design document, follow the steps below in order.**
Each step references a dedicated skill file — read it when you reach that step.

---

## Workflow

### Step 1 — Pre-flight
Read context and check for conflicts before writing anything.
→ Follow **`technical-design-documentation/SKILL.md`** (Steps 1–2)

### Step 2 — Decide API Routing
For every API operation in the feature, decide: FE → Supabase PostgREST / NestJS / Supabase Realtime.
→ Follow **`technical-design-api-routing/SKILL.md`**

### API Boundary Rule (Mandatory)
- Each API endpoint must serve one and only one business purpose.
- Do not combine independent purposes in one endpoint (for example: identity sync + workspace join).
- Multi-step business flows must be orchestrated using multiple endpoints.

### Step 3 — Write the TD
Use the standard 15-section template. Apply routing decisions in Section 4.0.
→ Follow **`technical-design-document-structure/SKILL.md`**

### Step 4 — Apply Patterns & Avoid Pitfalls
While writing, reference patterns and rules for FE, BE, on-chain, events, and diagrams.
→ Follow **`technical-design-patterns/SKILL.md`**

### Step 5 — Definition of Done & Post-write
Verify the TD meets all Done conditions, then update `docs/features/base-design.md`.
→ Follow **`technical-design-documentation/SKILL.md`** (Steps 4–5)

---

## ⚠️ Destructive Operations

> Use the skill below **only** when explicitly asked to delete, reset, or significantly rewrite an existing TD.
> Never invoke it as part of a normal write workflow.

### Safe Delete or Rewrite a TD
Remove or recreate a TD while keeping `docs/features/base-design.md` fully consistent — no orphan registry entries, no broken ERD nodes.
→ Follow **`safe-technical-design-maintenance/SKILL.md`**

---

## Skill Map

```
technical-design/
├── SKILL.md                                   ← this file — entry point & workflow
├── technical-design-documentation/
│   └── SKILL.md                               Pre-flight, file location, DoD, post-write, checklist
├── technical-design-api-routing/
│   └── SKILL.md                               FE → Supabase vs NestJS vs Realtime decision rules
├── technical-design-document-structure/
│   └── SKILL.md                               Full 15-section TD template
├── technical-design-patterns/
│   └── SKILL.md                               Best practices, diagram patterns, common pitfalls
└── safe-technical-design-maintenance/         ⚠️ DESTRUCTIVE — use only when deleting or rewriting
    └── SKILL.md                               Safe delete / rewrite + base-design.md reconciliation
```

---

## Quick Reference

| Question | Go to |
|---|---|
| Where do I save the file? | `technical-design-documentation` → Step 2 |
| What sections does the TD need? | `technical-design-document-structure` |
| Does this call go to Supabase or NestJS? | `technical-design-api-routing` |
| How do I draw this diagram correctly? | `technical-design-patterns` |
| What are the on-chain design rules? | `technical-design-patterns` |
| When is the TD considered done? | `technical-design-documentation` → Step 4 |
| What do I update in base-design.md after? | `technical-design-documentation` → Step 5 |
| ⚠️ Delete or rewrite an existing TD safely | `safe-technical-design-maintenance` |



