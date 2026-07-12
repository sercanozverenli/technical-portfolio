# Time Travel, Branching Architecture, and Basic Merge

This document covers exploring your project history, opening secure parallel work environments (branches) independent of the main line, using modern navigation tools, and executing a clean, conflict-free merge process.

---

## Part 1: Project Timeline (git log)

All commits made in a project from past to present are stored chronologically in the Git history. The `git log` command allows you to read this project timeline.

### git log
*   **Description**: Lists the entire commit history of the project, including the unique commit ID (SHA-1), date, and author information.
```bash
# Scenario 1: Viewing the detailed commit history
git log

# Scenario 2: Listing commits compressed into a single line for quick reading
git log --oneline

# Scenario 3: Limiting the number of listed recent actions (e.g., last 3 commits)
git log -n 3

# Scenario 4: Viewing the commit history as a visual graph showing branching structures
git log --oneline --graph --all
```

---

## Part 2: The Heart of Git Mechanics: The HEAD Concept

### 1. What is HEAD?
In Git architecture, HEAD is a pointer that **shows the active commit or the active branch you are currently working on in your local repository**. Git knows exactly which code version is open on your screen thanks to this pointer.

### 2. Architectural Structure and Movement of HEAD
Under normal conditions, HEAD points to the latest commit of the active branch (for example, `main`). As you make new commits, your branch moves forward, and HEAD automatically moves to the latest commit along with that branch.

*   **Detached HEAD State**: If you jump back to a specific past commit using the command `git checkout <old-commit-id>`, HEAD stops pointing to a branch and attaches directly to that specific past commit. This is called a "Detached HEAD" state. Changes made in this state are not linked to any branch, so they will not be permanent unless saved to a new branch.

---

## Part 3: Modern Branch Management (git branch & git switch)

Branches are independent parallel universes where you can develop new features or fix bugs without breaking the working code of the main project.

### 1. Critical Difference: Why git switch instead of git checkout?
In older Git versions, the `git checkout` command performed two completely different tasks by itself: it switched between branches and it restored deleted or changed files. Because this caused confusion, modern Git architecture separated these duties:
*   `git switch`: A modern command designed **strictly for branch switching and branch management**.
*   `git restore`: Separated strictly for bringing files back to a previous state or restoring them.
Note: It is generally recommended to use a **git switch** for branch switching operations.

### 2. Commands and Variations

#### git branch
*   **Description**: Lists existing branches in the project or creates a brand new side branch from scratch.
```bash
# Scenario 1: Listing all local branches (the active branch has an * symbol next to it)
git branch

# Scenario 2: Creating a new side branch named "login-screen" (does not switch to it yet)
git branch login-screen

# Scenario 3: Deleting a finished side branch safely (warns you if there are unmerged codes)
git branch -d login-screen

# Scenario 4: Force-deleting a branch even if it contains unmerged codes
git branch -D branch-to-delete
```

#### git switch
*   **Description**: Moves the HEAD pointer to switch between existing branches or creates a new branch and switches to it in a single command.
```bash
# Scenario 1: Switching to another existing branch
git switch login-screen

# Scenario 2: Creating a new branch and switching to it immediately (-c for create)
git switch -c payment-system

# Scenario 3: Quickly switching back to the previous branch you were working on
git switch -
```

---

## Part 4: Code Comparison (git diff)

The `git diff` command allows you to view detailed line-by-line code changes (added lines in green, deleted lines in red) between two different stages or two different branches in your project.

### git diff
*   **Description**: Lists line-by-line differences between your Working Directory, Staging Area, or existing branches.
```bash
# Scenario 1: Viewing changes made in the working directory that are not yet added with "git add"
git diff

# Scenario 2: Viewing differences between the staged codes and the last commit
git diff --staged

# Scenario 3: Comparing all code differences between two different branches
git diff main..payment-system

# Scenario 4: Viewing the difference of a specific file between two branches
git diff main..payment-system index.html
```

---

## Part 5: Basic Merge (git merge)

When code written in a side branch is finished and tested, you need to add these changes to the main project (the `main` or `master` branch). This process is called merging.

### git merge
*   **Description**: Pulls the commit history and code changes from a specified branch into the active branch you are currently on.
```bash
# Clean (Conflict-Free) Basic Merge Scenario:

# Step 1: Switch to the target main branch where codes will be merged
git switch main

# Step 2: Call the updated codes from the side branch into the main branch
git merge payment-system
```

### How Does the Mechanism Work?
In this basic scenario, if no new commits were made on the `main` branch after the `payment-system` branch was created, Git performs a linear merge. In this clean process, files merge automatically without conflicts, and the terminal completes the process without waiting for user action.


