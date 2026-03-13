---
name: Safe Technical Design Maintenance
description: Safely delete, reset, or rewrite technical design documents while keeping docs/features/base-design.md consistent (Sections 1-9). Use when a TD must be removed, recreated, or significantly refactored without leaving orphan registry entries.
---

# Safe Technical Design Maintenance Skill

Use this skill for two operations:

1. Delete a TD safely.
2. Rewrite a TD safely (same path or replacement).

## Required Inputs

- Target TD file path (expected format: `docs/features/[EPIC-ID]/technical-design/[US-ID].md`)
- Operation mode: `delete` or `rewrite`
- Related requirement id (`US-xxx.xxx`)

## Safety Invariants

- Never leave `docs/features/base-design.md` pointing to a missing TD file.
- Never keep confirmed service/table/route/event entries that no longer have a TD source.
- Never remove entries that are still confirmed by another TD.
- Keep Section 8 assumptions and Section 9 ERD aligned with current confirmed state.

## Pre-flight Checklist

Before editing:

1. Read `docs/features/base-design.md`.
2. Read target TD (if exists).
3. Identify all impacted base-design sections:
   - `Registry Status`
   - `Section 1` to `Section 5`
   - `Section 8`
   - `Section 9`
4. Build an impact list:
   - Services to remove/update
   - Tables to remove/update
   - Routes to remove/update
   - Events/jobs/tx types to remove/update
   - ERD nodes/relationships to remove/update

## Workflow A: Safe Delete

Use when user wants to remove a TD and pause or restart later.

### A1. Remove TD file

- Delete only the target TD file.
- Do not delete unrelated TD files.

### A2. Update `base-design.md`

Apply all:

1. `Registry Status`:
   - Set target row to `PENDING`
   - Set `Last Updated` to `-`
2. `Section 1` to `Section 5`:
   - Remove rows whose `TD Document` points to the deleted TD
   - Keep rows still referenced by other TDs
3. `Section 8`:
   - Re-add assumptions that became unconfirmed after deletion
4. `Section 9` ERD:
   - Remove entities and relationships that were confirmed only by deleted TD
   - If no confirmed entity remains, set placeholder:
     - `_(empty - first TD will populate this)_`

### A3. Validate

- No remaining mention of deleted TD path in `base-design.md`
- No broken section structure
- No duplicate assumptions in Section 8

## Workflow B: Safe Rewrite

Use when user wants to regenerate TD content.

### B1. Rewrite target TD

- Keep file path stable unless user asks for path change.
- Produce full TD with required structure from `technical-design-documentation` skill.

### B2. Reconcile `base-design.md`

1. Update `Registry Status` to `DONE` with current date.
2. Replace old confirmed entries with new confirmed entries in Sections `1-5`.
3. Remove newly confirmed names from Section 8.
4. Update Section 9 ERD to reflect new confirmed entities and relations.

### B3. Validate

- Every confirmed entry in Sections `1-5` has a valid `TD Document` source.
- Section 9 ERD matches tables confirmed in Section 2.1.
- Section 8 contains assumptions only (not confirmed names).

## Conflict Rules

- If a service/table/route/event is shared by multiple TDs, do not remove it during delete.
- If ownership changes across services during rewrite, mark as conflict and resolve explicitly in base-design.
- If entity naming changes, update both registry and ERD in the same change.

## Output Requirements

After operation, always report:

1. TD files removed/rewritten
2. Base-design sections changed
3. Confirmed entries removed/added
4. Remaining open assumptions in Section 8 (if any)

## Quick Audit Checklist

- [ ] Target TD operation completed (`delete` or `rewrite`)
- [ ] `base-design.md` updated in all impacted sections
- [ ] No orphan references to missing TD files
- [ ] Section 9 ERD synchronized
- [ ] Section 8 contains assumptions only
