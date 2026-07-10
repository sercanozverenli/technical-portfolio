# Project Management, Bug Tracking, and Team Organization

This document focuses not on writing code, but on how to manage a software project, how to distribute tasks, how to report and track software bugs, and the use of GitHub's built-in Agile management tools, the Issues and Projects systems.

---

## 1. What is GitHub Issues and What is it Used For?

**GitHub Issues** is a repository's built-in bug tracking and ticket system. Although named "Issue," it is not used solely for bugs; every work item in the software development lifecycle is opened as an Issue.

**Basic Usage Scenarios:**
* **Bug Tracking:** "The 'forgot password' button on the login screen is not working."
* **Feature Request:** "Dark mode support should be added to the application."
* **Task Management:** "Writing documentation for database schemas."
* **Question and Discussion:** "Should we use ElasticSearch or Redis for the search algorithm?"

When an Issue is created, the system assigns it a unique ID (e.g., `#12`). All commits and Pull Requests (PRs) within the repository can refer to the Issue using this ID.

---

## 2. Issue Management Parameters (Metadata)

To avoid getting lost in a project containing hundreds of tasks, GitHub provides powerful classification and filtering tools in the panel on the right:

### A. Assignees
Determines who is responsible for a task. An Issue should not be ownerless. When you assign an Issue to someone, a notification is sent to that person's e-mail address and GitHub notification panel, making the person responsible for the work clear.

### B. Labels
These are color-coded tags that allow you to categorize and filter tasks. GitHub provides a few labels by default, but project-specific labels can be created:
* `bug`: Something is not working. (Red)
* `enhancement`: New feature or improvement. (Light Blue)
* `documentation`: Needs documentation update, not code. (Blue)
* `good first issue`: Easy, warm-up tasks for those new to open source projects.
* `help wanted`: Issues where the project owner needs external help or expertise.

### C. Milestones
Used to link multiple related Issues to a specific goal (Sprint or Release).
* **Example Milestone:** `v1.0.0 Beta Release` or `October Sprint`.
* A due date can be given to a Milestone. When 6 out of 10 Issues added to it are closed, the system automatically displays a progress bar showing "%60 Completed".

---

## 3. Issue Templates Configuration

Especially in Public (open source) repositories, Issue Templates are used to prevent incoming users from making inadequate bug reports ("The app crashed, fix it").

If you create a `.github/ISSUE_TEMPLATE/` folder in your repository's root directory and add templates in Markdown format, a form is presented directly to the user who wants to open a new Issue.

**Example `bug_report.md` template structure:**
```markdown
---
name: Bug Report
about: Use this to report that something is broken
title: '[BUG] '
labels: bug
assignees: ''
---

**Description of the bug**
State clearly and concisely what the bug is.

**Steps to Reproduce**
Write the steps to reach the bug screen:
1. Go to '...' page.
2. Click '...' button.
3. See the error.

**Expected Behavior**
Explain how the broken part should actually work.

**Environment:**
 - Operating System: [e.g. iOS, Windows]
 - Browser [e.g. Chrome, Safari]
 - Version [e.g. 22]
```

---

## 4. Smart Automation: Closing Issues with Commits

One of GitHub's most powerful features is that it allows developers to automatically close Issues by just sending code (push) from the terminal, without needing to enter the GitHub interface.

If specific keywords and the Issue number (`#ID`) are used together in a commit message, GitHub automatically sets the relevant Issue to "Closed" status the moment that commit is merged into the main branch.

**Valid Keywords:** `close`, `closes`, `closed`, `fix`, `fixes`, `fixed`, `resolve`, `resolves`, `resolved`

**Terminal Example:**
```bash
git add .

# When this commit reaches the server, Issue #42 is automatically closed
git commit -m "fix: crash in password reset button fixed. Fixes #42"

git push origin main
```

---

## 5. GitHub Projects (Agile/Kanban Board)

In large projects, **GitHub Projects** is used to visualize Issues instead of reading them as a list. If you have used Jira or Trello, GitHub Projects is a system that works with the exact same logic and is integrated directly with the code and Issues in your repository.

**Basic Architecture (Kanban Columns):**
A project board typically consists of 3 to 4 basic columns:
1. **To Do:** The stack where Issues or notes that have not yet been started are kept.
2. **In Progress:** Assigned tasks where a developer is currently actively writing code.
3. **In Review:** Tasks for which code has been written, a Pull Request (PR) has been opened, and approval (Code Review) is pending.
4. **Done:** Tasks whose code has been successfully merged into the main branch and closed.

**Built-in Automation:**
GitHub Projects' biggest advantage is its connection to the code. You can add rules to the board:
* "When a PR is opened for an Issue, automatically move that card from the 'In Progress' column to the 'In Review' column."
* "When the PR is merged, automatically move the card to the 'Done' column and close the Issue."
