---
name: Technical Design API Routing
description: Decision framework for routing frontend API calls — FE → Supabase PostgREST vs FE → NestJS vs FE → Supabase Realtime — for the Finance Education platform
---

# Technical Design API Routing

This project uses **both** Supabase (PostgREST + Realtime + Storage) and NestJS as backend targets.
Every API operation from the frontend must be **explicitly routed** to one of them in Section 4.0 of the TD.

---

## Decision Rules

Apply per operation. First matching rule wins.

### Purpose Constraint (Mandatory)

- One endpoint = one business purpose.
- Do not combine unrelated purposes in one endpoint (for example: session synchronization + invite acceptance).
- If a user flow needs multiple purposes, split into multiple endpoints and orchestrate in FE/BE workflow.

| Condition | Route to |
|---|---|
| Realtime subscription (push from backend to FE) | **Supabase Realtime** |
| File upload / download | **Supabase Storage** — NestJS issues signed URL, FE uploads directly |
| Operation has complex business logic or state transition | **NestJS** |
| Operation requires AI processing (Gemini) | **NestJS** (via LangChain/Gemini SDK) |
| Operation involves OCR / Data extraction from images | **NestJS** (server-side processing) |
| Read Joins multiple tables or requires server-side aggregation | **NestJS** |
| Simple single-table read/write, RLS covers the policy, no side effects | **Supabase PostgREST** (session JWT, RLS enforced) |

---

## Decision Checklist — apply per operation

1. **Is this a push subscription?** → Yes = Supabase Realtime
2. **Does this have business logic or state transition?** → Yes = NestJS
3. **Does this use AI (Gemini) or OCR?** → Yes = NestJS
4. **Is this a file upload/download?** → Yes = Supabase Storage
5. **Is it a simple read/write where RLS is enough?** → Yes = Supabase PostgREST

---

## Section 4.0 API Routing Table

Every TD **must** include this table before Section 4.1 Endpoints.

```markdown
### 4.0 API Routing Table

| Operation | Method | Path / Channel | Routes to | Reason |
|---|---|---|---|---|
| List Daily Tips | GET | `/rest/v1/daily_tips` | Supabase PostgREST | Simple read, RLS by user_id |
| Summarize Article | POST | `/api/v1/mentor/summarize` | NestJS | AI processing (Gemini) |
| Scan Bill | POST | `/api/v1/tracking/scan` | NestJS | OCR processing |
| Subscribe to Goals | — | `user_goals:user_id=eq.[id]` | Supabase Realtime | Push — no business logic |
| Upload Receipt | PUT | Supabase Storage signed URL | Supabase Storage | File upload, URL issued by NestJS |
```

---

## Routing Summary Card

```
FE needs data (read, no logic)     → Supabase PostgREST  (session JWT + RLS)
FE triggers action (write, logic)  → NestJS              (Bearer JWT + RBAC guard)
FE subscribes to live updates      → Supabase Realtime   (API SDK channel)
FE uploads / downloads file        → Supabase Storage    (signed URL from NestJS)
```

---

## Pitfalls


- **Never** overload one endpoint with multiple business purposes - split by responsibility and compose the flow
- **Never** poll NestJS for status that Supabase Realtime can push
- **Never** expose `service_role` key to the frontend — NestJS uses it server-side only
- **Never** leave Section 4.0 API Routing Table empty or with `[TBD]` entries


