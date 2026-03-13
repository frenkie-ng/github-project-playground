---
name: Technical Design Document Structure
description: Standard 15-section document template for writing a Technical Design document for the Finance Education platform — covers end-to-end FE + BE design
---

# Technical Design Document Structure

Use this template when writing a Technical Design document. Every TD must follow this structure.
Each section is **required** unless marked `(if applicable)`.

> Before writing, always run the `technical-design-documentation` skill for pre-flight context reading and file location rules.

---

## Template

```markdown
# Technical Design: [Feature Name] — [US-ID]

> **Scope**: [Domain service(s) from diagram 1.2]
> **Requirements**: [US-xxx links]
> **Related TDs**: [Links to related TD files]
> **Depends on (Open Items)**: [Section 6 items from base-design.md that must be resolved first]

## 1. Overview

- Purpose and scope
- Which diagram 1.2 service group(s) this covers
- In scope / out of scope

## 2. Architecture Slice

> Zoom into the relevant service(s) from diagram 1.2 only — do not redraw the full system.

\`\`\`mermaid
flowchart TB
    subgraph APIB["API Boundary"]
        API["[API group name]"]
    end
    subgraph SVC["Domain Service"]
        Svc["[serviceName]"]
    end
    subgraph INFRA["Infrastructure"]
        DB["Supabase Postgres"]
        Pipeline["Event Streaming & Data Pipeline"]
    end
    API --> Svc
    Svc --> DB
    Svc -.->|"publish event"| Pipeline
\`\`\`

### Component Responsibilities

[One paragraph per component — what it owns, what it does NOT own, what it delegates]

## 3. Data Design

### 3.1 Data Model

\`\`\`mermaid
erDiagram
    TABLE_A {
        uuid id PK
        uuid workspace_id FK
        text status
        timestamptz created_at
    }
    TABLE_B {
        uuid id PK
        uuid table_a_id FK
    }
    TABLE_A ||--o{ TABLE_B : "has"
\`\`\`

### 3.2 Schema Definitions

> For each table: columns, types, constraints, indexes, RLS policy.

**table_name**

| Column | Type | Constraints | Notes |
|---|---|---|---|
| id | uuid | PK, default gen_random_uuid() | |
| workspace_id | uuid | FK → workspaces.id, NOT NULL | RLS: filter by workspace |
| status | text | NOT NULL, CHECK (status IN (...)) | |
| created_at | timestamptz | NOT NULL, default now() | |
| updated_at | timestamptz | NOT NULL, default now() | |
| deleted_at | timestamptz | | Soft delete |

**Indexes:**
- `idx_[table]_workspace_id` on `(workspace_id)`

**RLS Policy:**
- Policy name: `[table]_select_[scope]`
- `SELECT` (`USING`): [exact SQL expression with `auth.uid()` and workspace-membership join]
- `INSERT` (`WITH CHECK`): [exact SQL expression or `not allowed for authenticated`]
- `UPDATE` (`USING` + `WITH CHECK`): [exact SQL expression or `not allowed for authenticated`]
- `DELETE` (`USING`): [exact SQL expression or `not allowed for authenticated`]
- Service writes: enforced via NestJS service role path

**RLS Policy Matrix (mandatory):**

| Table | DB role | SELECT USING | INSERT WITH CHECK | UPDATE USING / WITH CHECK | DELETE USING | Notes |
|---|---|---|---|---|---|---|
| `table_name` | `authenticated` | `[sql expression]` | `false` / `[expression]` | `false` / `[expression]` | `false` / `[expression]` | [policy name references] |
| `table_name` | `service_role` | bypass RLS | bypass RLS | bypass RLS | bypass RLS | Backend only (NestJS) |

### 3.3 Supabase Access Pattern

| Operation | Access path | Notes |
|---|---|---|
| Frontend read (non-sensitive) | PostgREST + session JWT | RLS enforced |
| Frontend read (sensitive / cross-join) | NestJS → Postgres | Service role, NestJS enforces RBAC |
| Backend write | NestJS → Postgres | Always service role key |
| Realtime subscription | Supabase Realtime | FE subscribes via API SDK |

## 4. API Design

### 4.0 API Routing Table

> Required. See `technical-design-api-routing` skill for decision rules.
> Rule: one endpoint must serve one business purpose only. Split multi-purpose workflows into multiple endpoints.

| Operation | Method | Path / Channel | Routes to | Reason |
|---|---|---|---|---|
| [operation] | GET/POST/… | `/rest/v1/[table]` or `/api/v1/[path]` or `[table]:[filter]` | Supabase PostgREST / NestJS / Supabase Realtime | [reason] |

### 4.1 Endpoints

> Only for NestJS endpoints. Supabase PostgREST operations do not need endpoint docs here.

#### [METHOD] /api/v1/[resource]

> **Auth**: Bearer JWT (Supabase session, validated by NestJS Passport strategy)
> **RBAC**: `[workspace:role]`

**Request:**
\`\`\`json
{ "field": "value" }
\`\`\`

**Response (2xx):**
\`\`\`json
{ "id": "uuid", "field": "value" }
\`\`\`

**Errors:**

| Code | Reason |
|---|---|
| 400 | Validation failed |
| 401 | Missing / invalid session |
| 403 | Insufficient role |
| 404 | Resource not found |
| 409 | State conflict |
| 500 | Internal error |

### 4.2 API Sequence Diagrams

\`\`\`mermaid
sequenceDiagram
    actor User
    participant FE as Frontend
    participant API as NestJS API
    participant DB as Supabase Postgres
    participant K as Event Pipeline

    User->>FE: Action
    FE->>API: POST /api/v1/resource (Bearer JWT)
    API->>API: Validate JWT + RBAC
    API->>DB: Read / write
    API->>K: publish event
    API-->>FE: 201 / 200
\`\`\`

## 5. Frontend Design

> Covers everything a frontend engineer needs — no separate conversation with BE required.
> Follow diagram 1.1 layering: Route/Screen → Component → Hook → API SDK → Backend.

### 5.1 Screens & User Flows

| Screen | Route | Description | Primary actions |
|---|---|---|---|
| [Screen name] | `/[route]` | [What user sees] | [Actions] |

**User flow (happy path):**

\`\`\`mermaid
flowchart TD
    A["User lands on [screen]"] --> B["[Action]"]
    B --> C{"[Decision]"}
    C -->|success| D["[Next state]"]
    C -->|error| E["[Error state]"]
\`\`\`

### 5.2 Component Contract

> Smart Components only (data-aware). Presentational components omitted.

| Component | Reads from hook | Calls API hook | Emits / dispatches |
|---|---|---|---|
| `[ComponentName]` | `use[X]` — `[field]` | `use[X]Query` → `[endpoint]` | `[callback]` |

### 5.3 Hook & State Specification

**`use[Feature]Query`** *(read)*
- **Calls**: `apiSdk.[method]` → `[endpoint]`
- **Returns**: `{ data, isLoading, error }`
- **Caching**: [stale time / invalidation trigger]

**`use[Feature]Mutation`** *(write)*
- **Calls**: `apiSdk.[method]` → `[endpoint]`
- **On success**: [cache invalidation / navigation]
- **On error**: [error display]

**State shape** (if new slice needed):
\`\`\`typescript
interface [Feature]State {
  [field]: [type]; // [description]
}
\`\`\`

### 5.4 Realtime Subscription (if applicable)

| Channel | Trigger | FE reaction |
|---|---|---|
| `[table]:[filter]` | [Backend action] | [UI update behaviour] |

### 5.5 Frontend Error Handling

| Scenario | UI behaviour |
|---|---|
| API 4xx | Toast / inline error |
| API 5xx | Retry prompt / fallback |
| Network offline | Offline indicator |
| On-chain tx rejected | Cancel state / back to form |
| Realtime disconnected | Reconnect indicator |

## 6. On-chain Flow (if applicable)

> Include only if the feature involves Gnosis Safe transactions.

### 6.1 Transaction Lifecycle

\`\`\`mermaid
sequenceDiagram
    actor User
    participant FE as Frontend (Viem)
    participant API as NestJS API
    participant Safe as Gnosis Safe
    participant AW as Async Worker (reconciler)
    participant WI as Wallet Indexer

    User->>FE: Trigger on-chain action
    FE->>API: POST /api/v1/[resource]/prepare-tx
    API->>Safe: proposeTransaction (safeTxHash)
    API->>DB: Persist tx intent (status: pending_signature)
    API-->>FE: safeTxHash

    User->>FE: Sign tx (Viem signTypedData)
    FE->>Safe: confirmTransaction (signature)
    FE->>API: POST /api/v1/[resource]/submit-tx { safeTxHash }
    API->>DB: Update status → submitted
    API->>K: publish [domain].tx.submitted

    Safe-->>WI: On-chain event emitted
    WI->>K: publish onchain topics
    K->>AW: consume + reconcile
    AW->>DB: Update status (confirmed / failed)
    AW-->>FE: Realtime push via Supabase Realtime
\`\`\`

### 6.2 Safe Module Config (if applicable)

| Module | Purpose | When configured |
|---|---|---|
| Zodiac Roles Module | Restrict contract method calls by role | On workspace setup / role change |
| Allowance Module | Enforce spending limits per delegate | On spending policy assignment |

### 6.3 On-chain Error Cases

| Scenario | Detection | Handling |
|---|---|---|
| Tx reverted | Wallet Indexer detects failed tx | reconciler marks `execution_failed`, notify |
| Insufficient signatures | Safe rejects execution | Keep `pending_execution` until threshold met |
| Safe locked | `multisigService` pre-check | Block submission, surface warning to FE |

## 7. Event Design (if applicable)

> Include only if the feature produces or consumes Kafka events.

### 7.1 Produced Events

| Event name | Topic group | Producer | Trigger | Key fields |
|---|---|---|---|---|
| `[event.name]` | [topic group] | `[service]` | [trigger] | `{ field, ... }` |

### 7.2 Consumed Events

| Event name | Topic group | Consumer (Async Worker) | Action |
|---|---|---|---|
| `[event.name]` | [topic group] | `[consumer]` | [action] |

### 7.3 Event Schema

\`\`\`json
{
  "eventId": "uuid",
  "eventType": "[event.name]",
  "timestamp": "ISO-8601",
  "workspaceId": "uuid",
  "payload": {}
}
\`\`\`

> BullMQ (not Kafka) is used for export job queuing. Only `export.ready` goes back into Kafka.

## 8. Security

- **Auth**: All NestJS endpoints require Bearer JWT from Supabase Auth (Passport JWT strategy).
- **RBAC**: NestJS guard evaluates role from workspace membership — do not rely on RLS alone for writes.
- **RLS**: Every table has `workspace_id` RLS policy as primary isolation boundary.
- **On-chain**: Client signs with Viem `signTypedData` (EIP-712) — backend never holds private keys.
- **Service key**: NestJS uses `service_role` server-side only — never exposed to frontend.
- [Feature-specific notes]

## 9. Error Handling

**Standard error response (NestJS):**
\`\`\`json
{
  "statusCode": 400,
  "error": "ERROR_CODE",
  "message": "Descriptive message"
}
\`\`\`

| Scenario | HTTP | Error code |
|---|---|---|
| [scenario] | 4xx | [CODE] |

**Async error handling:**
- Kafka: retry × 3 with backoff → dead-letter topic
- BullMQ: built-in retry → failed queue

## 10. Scalability

- **NestJS API**: Stateless, horizontal scale. Target p95 < 300ms.
- **Async Worker**: Independently scalable, consumer count per partition.
- **Supabase Postgres**: PgBouncer connection pooling.
- **ClickHouse**: Append-only, read scale independent.
- [Feature-specific notes]

## 11. Monitoring & Observability

- Kafka consumer lag alert
- BullMQ queue depth alert
- On-chain reconciliation timeout alert
- API p95 latency + 5xx rate
- [Feature-specific metrics]

## 12. Dependencies

| Dependency | Type | Fallback |
|---|---|---|
| Supabase Auth | Platform | Re-auth prompt |
| Supabase Postgres | Platform | No fallback — per US-000.002 |
| Gnosis Safe | On-chain | Retry by user |
| Kafka / Event Pipeline | Infrastructure | Dead-letter + retry |
| Novu | External | Best-effort |

## 13. Testing Strategy

- **Unit BE**: Service logic — mock DB and Kafka
- **Unit FE**: Hook logic — mock API SDK responses
- **Component**: Smart components — mock hooks, verify render + dispatch
- **Integration**: API + DB (Supabase test project)
- **E2E on-chain**: Testnet Safe deployment
- **Event flow**: Mock Kafka events → verify Async Worker DB transitions
- **Realtime**: Mock Supabase Realtime push → verify UI state via hook

## 14. Open Questions

- [ ] **[Topic]** — [What is unclear]. **Impact**: [Blocked services/flows]. **Ref**: [base-design.md Section 6 or system-architecture-overview.md Section 8].

## 15. Appendix

- Glossary
- Requirement references
- Related TD links
```

