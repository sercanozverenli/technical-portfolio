# Code Rollback and Error Recovery (Reset vs Revert)

This document covers the architectural mechanics and methods for cleaning up faulty code, removing incorrect commits, or rolling back unwanted changes cleanly without breaking the repository structure.

---

## Part 1: Architectural Differences: git reset vs git revert

When undoing an operation, the command you choose depends entirely on whether you are working alone or cooperating with a team on a remote server.

### 1. git reset (Rewriting History)
*   **Mechanism**: Directly pulls the HEAD pointer and the tip of the active branch back to a specific commit point in the past. It permanently deletes all commits made after that point from the history.
*   **Safety Risk**: It must only be used for local commits that are not pushed to a remote server yet. Using `reset` on shared public branches breaks the commit history map for team members and causes synchronization conflicts.

```text
[Before]
A --- B --- C --- D (main, HEAD)

[After git reset --hard B]
A --- B (main, HEAD)   [Commits C and D are completely deleted from history]
```

### 2. git revert (Creating Safe History)
*   **Mechanism**: Does not delete the faulty commit from history. Instead, it creates a **brand new correction commit (anti-commit)** that applies the exact opposite changes of the faulty commit.
*   **Safety Advantage**: Since it adds a new record instead of deleting past data, it is completely safe to use on public branches pushed to remote servers. It does not disrupt teamwork.

```text
[Before]
A --- B --- C --- D (main, HEAD)

[After git revert C]
A --- B --- C --- D --- E (main, HEAD)  [Commit E undoes the changes introduced by C]
```

---

## Part 2: git reset Modes and Deep Dive

The flags added to the `git reset <commit-id>` command determine the stage (Staging Area, Working Directory, or completely deleted) of the files from the canceled commits.

### 1. --soft Mode (Safest Rollback)
*   **Description**: Rolls back to the specified commit point but does not touch the code changes from the deleted commits. It keeps those changes directly in the **Staging Area**.
*   **Use Case**: Preferred when you want to change your last commit message or combine the last few commits into a single clean commit.

```bash
# Undo the last commit, but keep the code changes ready in the staging area
git reset --soft HEAD~1
```

### 2. --mixed Mode (Default Mode)
*   **Description**: This mode runs automatically if you do not type any flag. It rolls back to the specified commit point and moves the changes from the canceled commits back to the **Working Directory** (un-staged phase). Your code lines are not deleted, but they return to an un-added state.
*   **Use Case**: Used when you want to work more on the code, reorganize the files into different parts, and commit them separately.

```bash
# Undo the last commit and leave the codes in the working directory as un-staged changes
git reset --mixed HEAD~1
# Or use it directly without a flag:
git reset HEAD~1
```

### 3. --hard Mode (Dangerous and Permanent)
*   **Description**: Rolls back to the specified commit point and **permanently deletes** all commits made after that point, along with all data in the staging area and all code written in the working directory.
*   **Use Case**: Used when your current implementation is completely stuck, and you want to throw away everything you wrote to return to the last stable working commit.

```bash
# Completely delete EVERYTHING made after the last commit and roll back to that state
git reset --hard HEAD~1
```

---

## Part 3: Safe Rollback with git revert

### git revert
*   **Description**: Analyzes the contents of the specified commit ID, deletes the lines added in that commit, restores the lines deleted in that commit, and opens a text editor to create a new merge-like commit for this undo operation.

```bash
# Scenario 1: Safely undoing a specific faulty commit from the past
git revert a1b2c3d

# Scenario 2: Reverting directly without opening the automatic commit message editor
git revert --no-commit a1b2c3d
```

---

## Part 4: State Verification and Code Comparison (git diff)

Running `git diff` before or after fallback operations allows you to verify what changed in which version, ensuring the process completes without errors.

```bash
# Scenario 1: Viewing differences between your current working directory and a commit from the past
git diff HEAD~2

# Scenario 2: Inspecting exactly what a commit changed before running a revert operation
git diff a1b2c3d~1..a1b2c3d
```

