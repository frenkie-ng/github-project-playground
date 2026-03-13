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
- **Auto-labeling**: Can be set up to label PRs based on changed files.
- **Auto-assign**: (New!) Issues and PRs are now automatically assigned to the person who created them.
- **Auto-add to Project**: (New!) New items are automatically placed into the Project board.

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

## 6. Standardized Templates

We now use Issue Forms and PR Templates to ensure consistent documentation:

- **Issues**: When creating a new Issue, choose between `Epic`, `User Story`, or `Task` templates.
- **Pull Requests**: The PR template includes a mandatory checklist for linking issues and project boards.

## 7. Turn-key Workflow for Developers

1.  **Start a task**: Create an Issue using the `Task` template. (It will auto-assign to you and add to the Project board).
2.  **Code**: Create a branch `feat/US-ID-short-description`.
3.  **Submit**: Open a PR. (It will auto-assign to you and link to the project).
    - *Don't forget to add `Closes #ID` in the description.*
4.  **Done**: Once merged, the issue closes and moves to "Done" automatically. No extra prompts needed.
