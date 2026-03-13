---
name: Research Technology Documentation
description: Automated research and documentation for core technologies
---

# Research Technology Documentation Skill

This skill automates the process of researching and documenting core technologies for the project.

## Purpose

To create standardized, comprehensive technical documentation for technologies listed in the core overview, ensuring all team members have a clear understanding of the chosen stack.

## Workflow

### 1. Input Analysis

- **Source**: Read `docs/techinical-base/README.md` to identify the list of core technologies.
- **Trigger**:
  - User provides a specific technology name (e.g., "Supabase", "Kafka").
  - User specifies "ALL" to process all technologies listed in the overview using `run_workflow` or similar iteration loop (note: this skill handles one tech at a time).
- **Validation**: Ensure the requested technology is present in the `overview.md` or is a valid candidate for the stack.

### 2. Research Phase

Performs deep research using `search_web` and internal knowledge.

- **Context**: Focus on "Enterprise", "Finance", "Scalability", and "Security".
- **Search Queries**:
  - "[Tech Name] architecture overview"
  - "[Tech Name] vs [Alternative 1] vs [Alternative 2]"
  - "[Tech Name] performance benchmarks 2025"
  - "[Tech Name] security best practices finance"
  - "[Tech Name] nestjs integration pattern" (if backend)
  - "[Tech Name] nextjs integration" (if frontend)

### 3. Documentation Generation

- **Path**: `docs/techinical-base/[TECH_NAME_SLUG]/README.md`
  - _Example_: `docs/techinical-base/supabase/README.md`
- **Action**: Create the directory if it doesn't exist, then create/overwrite `README.md`.

#### Template structure

You **MUST** use the following Markdown structure for the output file:

```markdown
# [Technology Name]

> **Category**: [e.g. Database, Message Queue, Frontend Framework]
> **Status**: [Proposed / Adopted / Deprecated]

## 1. Overview

[A concise high-level summary of what this technology is and why it was chosen for this project.]

## 2. Architecture

[Mermaid diagram showing how this technology fits into our overall system architecture.]

\`\`\`mermaid
graph TD
User-->FrontEnd
FrontEnd-->[Tech Name]
[Tech Name]-->Database
\`\`\`

## 3. Key Features & Trade-offs

### Why we chose this

- **Feature A**: [Benefit]
- **Feature B**: [Benefit]

### Trade-offs / Cons

- **Challenge A**: [Mitigation]
- **Challenge B**: [Mitigation]

## 4. Alternatives Comparison

| Feature         | [Tech Name] | [Alternative A] | [Alternative B] |
| :-------------- | :---------- | :-------------- | :-------------- |
| **Performance** | High        | Medium          | High            |
| **Cost**        | Low         | High            | Medium          |
| **Complexity**  | Low         | High            | Medium          |

> **Verdict**: [Brief sentence on why the winner was selected]

## 5. Implementation Strategy

### Integration

[Code snippets or patterns for integrating with our specific stack (NestJS / Next.js / Supabase).]

### Configuration Criteria

- **Environment Variables**: `VAR_NAME`
- **Infrastructure**: [Docker / Cloud Provider settings]

## 6. Security & Compliance

- **Encryption**: [Data at rest/transit]
- **Access Control**: [RBAC / IAM]
- **Audit Logging**: [How to trace activities]

## 7. References

- [Official Documentation](url)
- [Best Practices Guide](url)
```

### 4. Verification

- Verify the file exists at the correct path.
- Ensure the content is not empty and follows the template.
- Confirm Mermaid diagrams are syntactically correct.
