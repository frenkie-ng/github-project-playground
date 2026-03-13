---
name: Create GitHub Issues
description: Parse requirement specs and create structured GitHub Issues for implementation using the Integrated Product Development model (placeholder early, update later).
---

# Create GitHub Issues Skill

## When to Use

This skill operates in **two phases** as part of the Integrated Product Development workflow:

**Phase 1 — Step 2 (Placeholder Issues)**:
- After Requirement (Step 1) is complete
- Create placeholder issues for tracking
- Format: `[US-ID] - [Feature Name] (Placeholder)`
- Purpose: Track progress, assign resources early

**Phase 2 — Step 8 (Update with Details)**:
- After Steps 5a, 5b, 6, 7 complete
- Update issues with full implementation details
- Link to code, PRs, and complete traceability

**Do not create issues from incomplete specs.**

---

## Step 1 — Pre-flight: Read the Full Spec

### For Phase 1 (Placeholder)
Read:
1. `docs/requirement/[EPIC]/[US-ID] - *.md` — Requirement document
2. (Optional) `docs/architecture/` — if architecture was updated

### For Phase 2 (Update)
Read in order:
1. `docs/features/[EPIC]/technical-design/[US-ID].md` — full Technical Design
2. `docs/features/[EPIC]/[US-ID]-ui.md` — UI/UX Design
3. `docs/features/[EPIC]/[US-ID]-test.md` — Test Cases
4. Existing placeholder issue — to update

---

## Step 2 — Issue Decomposition

### Phase 1 (Placeholder)

Create **one placeholder issue per User Story**:

**Format**:
```markdown
## [US-ID] [Feature Name] — Placeholder

**Epic**: [EPIC-ID] — [Epic name]
**Status**: Pending specification

## Description

This issue tracks the implementation of **[US-ID]**.

Detailed specifications will be added after:
- Technical Design (Step 5a)
- UI/UX Design (Step 5b)
- Test Cases (Step 6)
- Integration & Testing (Step 7)

## Timeline

- Spec complete: Steps 5a/5b/6
- Integration: Step 7
- Target release: [Sprint/Release]

## Labels

`epic:[EPIC-ID]` `us:[US-ID]` `status:spec-in-progress`
```

### Phase 2 (Full Decomposition)

Each User Story decomposes into issues across 3 categories:

**Category A: Backend Issues (repo: `Daily Tips-Backend`)**
One issue per **service/domain concern**. Typical breakdown:
- Database schema migration (tables, RLS policies, indexes)
- NestJS service + DTOs implementation
- API endpoints (controller + route registration)
- Kafka producer/consumer (if the feature publishes/consumes events)
- BullMQ job (if async export/processing is involved)

**Category B: Frontend Issues (repo: `Daily-Tips-Frontend`)**
One issue per **screen or major interaction**. Typical breakdown:
- Page/route scaffold (App Router layout + page)
- Hook API implementation (`hooks/use-api/`)
- Hook State implementation (`hooks/use-state/`) — if new Redux slice needed
- Components (per distinct UI section in the UI/UX Design)
- Supabase Realtime subscription (if real-time push required)

**Category C: Infrastructure / Cross-cutting (repo: `Daily Tips-Backend` or `Daily Tips-Docs`)**
- Kafka topic creation (if new topics defined in TD)
- ClickHouse schema migration (if new tables in TD)
- `base-design.md` update (always — after TD is confirmed)

---

## Step 3 — Issue Template

### Phase 1 (Placeholder)

Use the simplified placeholder template shown in Step 2.

### Phase 2 (Full Issues)

For each issue, use this format:

```markdown
## Context

**User Story**: [US-ID] — [US title]
**Epic**: [EPIC-ID] — [Epic name]
**Spec**: [link to Technical Design] | [link to UI/UX Design] | [link to Test Case]

## What needs to be done

[1-3 sentence description of this specific implementation task]

## Acceptance Criteria

- [ ] [Criterion 1 — derived from Technical Design or Test Case]
- [ ] [Criterion 2]
- [ ] [Criterion 3]

## Implementation Notes

[Key constraints, patterns, or references the implementer must know — e.g. "Use `workspaceGovernanceService` — do not create a new service", "Route this via NestJS, not PostgREST (see API routing decision in TD Section 4.0)"]

## Dependencies

- Depends on: [#issue-number or "none"]
- Blocks: [#issue-number or "none"]
```

---

## Step 4 — Labels and Metadata

Apply these labels consistently:

| Label | When to apply |
|---|---|
| `epic:[EPIC-ID]` | All issues for this Epic |
| `us:[US-ID]` | All issues for this User Story |
| `be` | Backend repo issues |
| `fe` | Frontend repo issues |
| `db` | Issues involving schema changes |
| `kafka` | Issues involving Kafka topics/consumers/producers |
| `realtime` | Issues involving Supabase Realtime |
| `infra` | Infrastructure / Docker / deployment issues |
| `status:placeholder` | Phase 1 (Placeholder) |
| `status:ready-for-dev` | After spec complete, ready for implementation |
| `status:in-progress` | Currently being implemented |
| `status:review` | Code review / QC testing |

Milestone: Set to the Sprint or Release this US belongs to (confirm with user).

---

## Step 5 — Dependency Ordering

### Phase 1 (Placeholder)

Create one placeholder issue per User Story. No dependency ordering needed at this stage.

### Phase 2 (Full Issues)

Present issues in dependency order before creating them. Standard order:

1. Database schema migration (BE)
2. NestJS service + DTOs (BE)
3. API endpoints (BE)
4. Kafka producer/consumer (BE) — if applicable
5. Hook API (FE) — depends on API endpoints being defined
6. Redux slice (FE) — if new state needed
7. Components (FE) — depends on hooks
8. Page/route (FE) — depends on components
9. `base-design.md` update (Docs) — last, after all implementation is confirmed

**Always show the user the full issue list with dependencies before creating any issues.** Confirm order and content, then create.

---

## Step 6 — Output Summary

### Phase 1 (Placeholder)

Output a simple tracking table:

```markdown
## Placeholder Issues Created

| US-ID | Feature Name | Issue # | Epic | Status |
|-------|--------------|---------|------|--------|
| US-001.001 | [Feature Name] | #42 | EPIC-001 | spec-in-progress |
| US-001.002 | [Feature Name] | #43 | EPIC-001 | spec-in-progress |

**Next Steps**:
- Steps 5a/5b: Technical Design + UI/UX Design
- Step 6: Test Cases
- Step 7: Integration
- Step 8: Update issues with implementation details
```

### Phase 2 (Full Issues)

After creating all issues, output a traceability table:

```markdown
## Issues Created for [US-ID] — [US title]

| # | Title | Repo | Labels | Depends On |
|---|---|---|---|---|
| #42 | [BE] Add users + auth_login_events schema | Backend | be, db, us:001.001 | — |
| #43 | [BE] userService — session sync endpoint | Backend | be, us:001.001 | #42 |
| #44 | [FE] Hook API — useUserApi (session sync) | Frontend | fe, us:001.001 | #43 |
| #45 | [FE] Login screen — wallet connect + SIWE | Frontend | fe, us:001.001 | #44 |
| #46 | [Docs] Update base-design.md registry | Docs | us:001.001 | #43 |
```

---

## Step 7 — Update Issues (Phase 2 Only)

After integration complete (Step 7), update each placeholder issue:

1. **Add implementation details**:
   - Link to Technical Design, UI/UX Design, Test Cases
   - Link to PRs and commits
   - Update acceptance criteria

2. **Update labels**:
   - Remove `status:placeholder`
   - Add `status:ready-for-dev` or `status:complete`

3. **Add traceability**:
   ```markdown
   ## Traceability

   | Document | Link |
   |---|---|
   | Requirement | [link] |
   | Technical Design | [link] |
   | UI/UX Design | [link] |
   | Test Cases | [link] |
   | PR | [link] |
   | Release | [link] |
   ```
