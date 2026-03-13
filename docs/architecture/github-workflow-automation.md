# GitHub Workflow Automation

This document outlines how to automate Pull Requests (PRs), Issue closing, and status updates within the `github-project-playground` repository.

## 1. Automatic Issue Closing

GitHub automatically closes issues when a Pull Request is merged if the PR description contains specific keywords followed by the issue number.

**Keywords**: `close`, `closes`, `closed`, `fix`, `fixes`, `fixed`, `resolve`, `resolves`, `resolved`.

**Example PR Description**:
```markdown
## Description
Implemented the OCR scanning logic for US-004.001.

Closes #12
```
*When this PR is merged to `main`, issue #12 will close automatically.*

---

## 2. GitHub Project V2 Automation

You can set up built-in workflows in your GitHub Project board:

1.  **Auto-add to Project**: Configure the project to automatically pull in any new issues created in the repo.
2.  **Status Sync**:
    - When a PR is opened → Move linked issue to **"In Progress"**.
    - When a PR is approved → Move linked issue to **"Review"**.
    - When a PR is merged → Move linked issue to **"Done"**.

*To configure: Go to Project > Workflow > Set up "Item closed" and "Pull request merged" rules.*

---

## 3. GitHub Actions (CI/CD)

We use `.github/workflows/` to automate tests and deployments:

- **Lint & Test**: Triggers on every push to a branch or opening of a PR.
- **Auto-labeling**: Can be set up to label PRs based on changed files (e.g., `be` label if `backend/` changes).
- **Preview Deployments**: Auto-deploy a staging version of the app when a PR is opened.

---

## 4. How to Create and Link a Pull Request

To ensure your work is tracked correctly in the Agile board, follow these steps when creating a PR:

### Step 1: Branch Naming
Always create a new branch from `main`:
`git checkout -b feat/US-001.001-ui-checklist`

### Step 2: Create the PR
When you push your branch, go to GitHub and click **"Compare & pull request"**.

### Step 3: Link the Issue (The "Magic" Link)
In the **Description** field, use the keyword `Closes` followed by the issue number.
> **Description**:
> Implemented the checklist UI for US-001.001.
>
> Closes #19

### Step 4: Link the Project (The "Agile" Link)
On the right-hand sidebar of the PR creation page:
- **Projects**: Click the gear icon and select `github-project-playground`.
- **Labels**: Add labels like `type: User Story` or `track: This Weekly`.
- **Linked issues**: GitHub usually suggests the issue if you used the keyword in the description, but you can also search manually here.

---

## 5. AI Agent Integration

When I (the AI Agent) help you create a feature:
1.  I will propose a branch name: `feat/[US-ID]-[short-name]`.
2.  I will remind you to use the `Closes #[IssueID]` syntax in your PR description.
3.  I can assist in generating the PR description text for you.
