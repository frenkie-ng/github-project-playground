---
name: Test Case Documentation
description: Create test case documentation for a specific User Story on the Finance Education platform. Scoped to feature-level test scenarios — written after Technical Design and UI/UX Design are synced.
---

# Test Case Documentation Skill

## Prerequisite

**Do not start this skill until both are complete and synced:**
- Technical Design: `docs/features/[EPIC]/technical-design/[US-ID].md` — Status: Approved
- UI/UX Design: `docs/features/[EPIC]/[US-ID]-ui.md` — Status: Approved

If either is missing or in Draft, stop and inform the user.

---

## Step 1 — Pre-flight: Read Context

Read in order before writing:

1. **`docs/requirement/[EPIC]/[US-ID] - *.md`** — the acceptance criteria and tasks.
2. **`docs/features/[EPIC]/technical-design/[US-ID].md`** — API contracts, data models, business rules, error cases, RLS policies.
3. **`docs/features/[EPIC]/[US-ID]-ui.md`** — screen states, validation rules, access control per element.
4. **`docs/requirement/EPIC-000 - Non-Functional Requirements/`** — security and compliance constraints that apply globally.

Map before writing:
- What are the **acceptance criteria** in the requirement? Each criterion → at least one test case.
- What are the **error cases** defined in the TD? Each error path → one negative test case.
- What **permission/role scenarios** exist? Each role boundary → one access control test case.
- What **Privacy/Data rules** exist? Each boundary → one user data privacy test case.

---

## Step 2 — File Location

```
docs/features/
└── [EPIC folder]/
    └── [US-ID]-test.md     ← output file
```

**Rules:**
- Path: `docs/features/[EPIC folder]/[US-ID]-test.md`
- EPIC folder mirrors `docs/requirement/` exactly.
- File name: `[US-ID]-test.md` — e.g., `US-003.001-test.md`.

---

## Step 3 — Test Case ID Convention

| Format | Example |
|---|---|
| `TC-[US-ID]-[3-digit sequence]` | `TC-003.001-001` |

Group by category using prefixes in the title:
- `[Happy Path]` — normal successful flow
- `[Validation]` — input validation, format checks
- `[Auth]` — authentication and authorization
- `[Permission]` — RBAC / role-based access
- `[Privacy]` — User data privacy and boundary
- `[Error]` — server errors, edge cases
- `[AI]` — AI response behavior (summarization, mentor responses)
- `[Gamification]` — XP/Badge award logic

---

## Step 4 — Document Template

```markdown
# Test Cases: [US Title] — [US-ID]

> **Requirements**: [link to requirement file]
> **Technical Design**: [link to TD file]
> **UI/UX Design**: [link to UI file]

---

## 1. Scope

**What is tested:**
- [Feature/flow 1]
- [Feature/flow 2]

**Out of scope:**
- [Related feature not covered here]

---

## 2. Test Cases

### TC-[US-ID]-001 [Happy Path] [Short description]

**Priority**: Critical | High | Medium | Low
**Requirement ref**: TASK-[ID]
**Preconditions**:
- [State of system before test]
- [User role / permissions]

```gherkin
Given [initial state]
And [additional precondition]
When [user action or API call]
Then [expected result]
And [additional expected result]
```

**Test data**:
- [field]: [value]

**API call** (if applicable): `[METHOD] /api/v1/[route]` → HTTP [status]

---

### TC-[US-ID]-002 [Validation] [Short description]

**Priority**: High
**Requirement ref**: TASK-[ID]
**Preconditions**: [state]

```gherkin
Given [state]
When [invalid input submitted]
Then [validation error is shown]
And [specific error message "[text]" is displayed]
And [the request is not sent to the backend]
```

---

### TC-[US-ID]-003 [Permission] [Short description]

**Priority**: High
**Requirement ref**: TASK-[ID]
**Preconditions**: User has role [X] (not [Y])

```gherkin
Given the user is authenticated with role "[role]"
And the user is in workspace "[workspace]"
When the user attempts to [action]
Then the response is HTTP 403
And the error message is "[expected message]"
```

---

### TC-[US-ID]-004 [Privacy] [Short description]

**Priority**: Critical
**Requirement ref**: TASK-[ID]
**Preconditions**: Two separate users (User-A and User-B) exist

```gherkin
Given user A has created [resource]
And user B is authenticated
When user B attempts to access user A's [resource]
Then the response is HTTP 403
And the data is not returned
```

---

## 3. Traceability Matrix

| Test Case ID | Category | Requirement ref | Priority | Covers |
|---|---|---|---|---|
| TC-[US-ID]-001 | Happy Path | TASK-[ID] | Critical | [what it tests] |
| TC-[US-ID]-002 | Validation | TASK-[ID] | High | [what it tests] |
| TC-[US-ID]-003 | Permission | TASK-[ID] | High | [what it tests] |
| TC-[US-ID]-004 | Isolation | TASK-[ID] | Critical | [what it tests] |

---

## 4. Coverage Check

| Acceptance Criterion (from requirement) | Covered by |
|---|---|
| [criterion text] | TC-[US-ID]-001 |
| [criterion text] | TC-[US-ID]-002 |

| Error Case (from Technical Design) | Covered by |
|---|---|
| [error case] | TC-[US-ID]-00X |

| RLS Policy (from Technical Design) | Covered by |
|---|---|
| [policy description] | TC-[US-ID]-004 |

---

## 5. Open Questions

- [ ] [Ambiguity or missing spec that needs clarification before test can be written]
```

---

## Step 5 — Test Case Writing Guidelines

### Gherkin rules
- **Given**: system state (not user action).
- **When**: single user action or single API call.
- **Then**: observable outcome — HTTP status, UI change, DB state.
- Be **specific**: use actual values, not placeholders. E.g., `HTTP 403`, not `an error`.
- One scenario per test case — do not combine two behaviors.

### Coverage minimums per US
- At least **1 happy path** test per acceptance criterion.
- At least **1 validation** test per user input field with constraints.
- At least **1 permission test** per role-gated action.
- At least **1 isolation test** per workspace-scoped resource.
- At least **1 error test** per server-side error path documented in the TD.

---

## Step 6 — Definition of Done

- [ ] Every acceptance criterion in the requirement has at least one test case.
- [ ] Every error case from the TD has a corresponding negative test.
- [ ] Every user data boundary has a privacy test.
- [ ] Every gamification event has an award logic test.
- [ ] Traceability matrix is complete.
- [ ] Coverage check shows no uncovered acceptance criteria.
- [ ] File saved to correct path: `docs/features/[EPIC]/[US-ID]-test.md`.
- [ ] Backlinks to requirement, TD, and UI doc are at the top.
