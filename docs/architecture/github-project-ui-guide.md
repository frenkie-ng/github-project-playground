# GitHub Project V2 - Agile Setup Guide

Since I cannot access your private login session in the browser, please follow these steps to configure your [Project #3](https://github.com/users/frenkie-ng/projects/3) for the Agile workflow we've established.

## View 1: 🏗️ Product Roadmap (Epic View)
*This view shows the Big Picture.*

1.  **Rename View**: Click the tab name and rename it to `🏗️ Roadmap`.
2.  **Layout**: Select **Roadmap** (if available) or **Table**.
3.  **Filter**: Type `label:"type: EPIC"` in the filter bar.
4.  **Group by**: (If Table) Group by `Status`.
5.  **Columns**: Show `Title`, `Priority`, `Status`, `Iteration`.

## View 2: 📋 Story Backlog
*This view groups all User Stories under their respective Epics.*

1.  **New View**: Click the `+` next to the tabs and select **Table**.
2.  **Rename**: `📋 Story Backlog`.
3.  **Filter**: Type `label:"type: User Story"` in the filter bar.
4.  **Group by**: Select the **Epic** field (or the label that starts with `epic:`).
5.  **Sort**: Sort by `Priority` (Critical -> Low).

## View 3: 🏃 This Weekly (Sprint Board)
*The active Kanban board for current work.*

1.  **New View**: Click `+` and select **Board**.
2.  **Rename**: `🏃 This Weekly`.
3.  **Filter**: Type `label:"track: This Weekly"` (or simply filter by the current `Iteration`).
4.  **Columns**: Grouped by **Status** (Todo, In Progress, Review, Done).

---

## 💡 Pro-Tips for Automation

Go to the **Workflows** tab (top right of the project) and enable:
- **Auto-add to project**: Set it to automatically add issues from the `github-project-playground` repository.
- **Item added to project**: Set status to `Todo`.
- **Item closed**: Set status to `Done`.
- **Pull request merged**: Set status to `Done`.

## 🛠️ Status Customization
In the Project Settings, I recommend setting these Status values:
- `🎯 Backlog` (White)
- `🕙 Todo` (Gray)
- `⚡ In Progress` (Blue)
- `👀 Review` (Purple)
- `✅ Done` (Green)
