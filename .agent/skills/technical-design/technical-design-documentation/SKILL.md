---
name: Technical Design Documentation
description: Orchestrates end-to-end Technical Design document creation for the Finance Education platform using the Integrated Product Development model (with API Mock/Stub for parallel testing).
---

# Technical Design Documentation

Each TD covers a **complete end-to-end feature** — Frontend + Backend.
Sufficient for a BE engineer to implement the service/API/data/events and a FE engineer to implement screens/hooks/state — without additional back-and-forth.

## Output

- `docs/features/[EPIC]/technical-design/[US-ID].md`
- **API Mock/Stub**: `docs/features/[EPIC]/mocks/[US-ID]-mock.ts`

> **Related skills:**
> - `technical-design-document-structure` — the 15-section document template
> - `technical-design-api-routing` — FE → Supabase vs FE → NestJS decision framework
> - `technical-design-patterns` — best practices, diagram patterns, common pitfalls

---

## Step 1 — Pre-flight: Read Context

Read these files **in order** before writing:

1. **`docs/features/base-design.md`**
   - **Sections 1–5** (Confirmed Registry): check for existing service names, table names, API routes, Kafka events, on-chain tx types — avoid conflicts.
   - **Section 9** (Data Model ERD): understand existing entities and relationships — reuse, do not duplicate.
   - **Section 8** (Architecture Assumptions): suggested names from architecture diagrams — starting points only, not constraints.
   - **Section 6** (Open Items): if any open item blocks this feature, call it out in Section 14 (Open Questions) of the TD.

2. **`docs/architecture/system-architecture-overview.md`** — diagrams 1, 1.1, 1.2, 1.3.

3. **`docs/techinical-base/README.md`** — stack: Supabase, NestJS, Kafka, Gnosis Safe, Squid, Viem, Novu, BullMQ, ClickHouse, Next.js.

4. **`docs/requirement/[EPIC]/[US].md`** — the requirement(s) being designed.

5. **`docs/requirement/EPIC-000 - Non-Functional Requirements/US-000.001 - Security & Access Constraints.md`**
   - Use as global baseline for authentication, authorization, and row-level isolation rules.

6. **Check UI/UX Design status**
   - If UI/UX Design (Step 5b) is in progress, coordinate on API contracts
   - Ensure data shapes in TD Section 6 match UI data bindings

**Map before writing:**
- Which **service(s)** from diagram 1.2? (`userService`, `workspaceGovernanceService`, `multisigService`, `treasuryService`, `paymentWorkflowService`, `auditLogService`)
- Which **API group**? (`User & Wallet APIs`, `Workspace Governance APIs`, `Treasury APIs`, `Payment & Approval APIs`, `Audit & Report APIs`)
- Which **screens / user flows**? (diagram 1.1)
- Per operation: **FE → Supabase PostgREST or FE → NestJS or FE → Supabase Realtime?** → use `technical-design-api-routing` skill
- Does it involve **on-chain txs** (Safe lifecycle)?
- Does it need **Realtime push** to UI?
- Which tables in this US require RLS policies, and what are exact `USING` / `WITH CHECK` expressions for `authenticated`?

---

## Step 2 — File Location

```
docs/features/
├── base-design.md                              ← living registry, always at root
├── EPIC-001 - Authentication & Access Control/
│   └── technical-design/
│       └── US-001.001.md
├── EPIC-002 - Workspace Management/
│   └── technical-design/
│       ├── US-002.001.md
│       └── US-002.006.md
└── ...
```

**Rules:**
- Path: `docs/features/[EPIC folder]/technical-design/[US-ID].md`
- EPIC folder mirrors `docs/requirement/` exactly
- File name: US-ID only — e.g., `US-004.002.md`
- `base-design.md` always at root of `docs/features/`

---

## Step 3 — Write the TD

Use the `technical-design-document-structure` skill for the full 15-section template.
Use the `technical-design-api-routing` skill for Section 4.0 routing decisions.
Use the `technical-design-patterns` skill for diagram patterns, best practices, and pitfalls.

### Additional Output: API Mock/Stub

After completing the TD document, create **API Mock/Stub**:

```
docs/features/[EPIC]/
└── mocks/
    └── [US-ID]-mock.ts    ← API mock handlers
```

**Mock API requirements:**
- Implement all endpoints from TD Section 4
- Return mock data matching TD Section 6 schemas
- Include realistic delays (100-500ms simulation)
- Support all HTTP methods (GET, POST, PUT, DELETE)
- Include error scenarios (400, 401, 403, 404, 500)

---

## Step 4 — Definition of Done

TD is **✅ Done** only when all of the following are true:

| # | Condition |
|---|---|
| 1 | All 15 sections written — no `[TBD]` or empty tables |
| 2 | Every `TASK-xxx` from linked US file(s) traceable to at least one section |
| 3 | All Mermaid diagrams render without error |
| 4 | All tables have full schema (columns, types, constraints, indexes, RLS) with explicit policy expressions |
| 5 | All NestJS endpoints have request/response examples + error table |
| 6 | **Section 4.0 API Routing Table** filled — every operation explicitly routed |
| 7 | **Section 5 (Frontend Design)** complete — screens, component contract, all hooks with endpoint mapping |
| 8 | Section 5.4 Realtime filled if feature receives push updates |
| 9 | Section 6 (On-chain) complete if feature touches Gnosis Safe |
| 10 | Section 7 (Events) complete if feature produces/consumes Kafka events |
| 11 | Open Questions lists only genuinely unresolved items |
| 12 | `docs/features/base-design.md` updated (see Step 5) |
| 13 | **API Mock/Stub implemented** for parallel testing |
| 14 | **Mock endpoints match TD Section 4** |
| 15 | **Mock data matches TD Section 6** schemas |

> A TD with open questions blocked by `base-design.md` Section 6 open items can still be ✅ Done — the TD is done, the blocker is tracked separately.

---

## Step 5 — Post-write: Update base-design.md

After completing the TD, update `docs/features/base-design.md`:

| Step | Update |
|---|---|
| 1 | Registry Status → ✅ Done |
| 2 | Section 1: add confirmed service + API group |
| 3 | Section 2.1: add confirmed Postgres tables |
| 4 | Section 2.2: add confirmed ClickHouse tables |
| 5 | Section 3: add confirmed API routes |
| 6 | Section 4: add confirmed Kafka events + BullMQ jobs |
| 7 | Section 5: add confirmed on-chain tx types |
| 8 | Section 6: update resolved/new open items |
| 9 | Section 9: update master ERD — add new entities, update changed relationships |
| 10 | Section 8: remove confirmed names from Architecture Assumptions |

> **Conflict rule**: If a name already exists in Sections 1–5 under a different service, raise a conflict — do not overwrite.
> **Rename rule**: If your TD uses a different name than Section 8 suggests, that is fine — register the confirmed name and remove the suggestion.

---

## Quick Checklist

- [ ] `base-design.md` Sections 1–5 read — no conflicts
- [ ] `base-design.md` Section 9 ERD read — no duplicate entities planned
- [ ] `base-design.md` Section 8 consulted — naming decisions made
- [ ] `system-architecture-overview.md` diagrams 1.1 + 1.2 read
- [ ] File created at correct path
- [ ] Section 4.0 API Routing Table complete
- [ ] Section 3.2 includes explicit RLS policy matrix per table
- [ ] Section 5 Frontend Design complete
- [ ] Section 5.4 Realtime documented (if applicable)
- [ ] Section 6 On-chain complete (if applicable)
- [ ] Section 7 Events complete (if applicable)
- [ ] `base-design.md` updated after completion
- [ ] API Mock/Stub created at `docs/features/[EPIC]/mocks/[US-ID]-mock.ts`
- [ ] Mock endpoints cover all TD Section 4 APIs
- [ ] Mock data matches TD Section 6 schemas
- [ ] Error scenarios implemented (400, 401, 403, 404, 500)
- [ ] Mock API testable via Postman/cURL or automated tests
