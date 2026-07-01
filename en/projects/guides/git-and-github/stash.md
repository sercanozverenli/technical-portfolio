# Temporary Storage Management (git stash)

This document covers how to save your half-finished changes securely to a stash area without making a commit, move flexibly between branches, and manage temporary storage data from start to finish.

---

## Part 1: Git Stash Concept and Stashing Scenarios

### 1. What is Git Stash and Why is it Critical?
When you have unfinished code changes in your current branch, you might need to switch to another branch suddenly to fix an urgent bug. You do not want to create an uncompleted commit, but if you try to switch branches without saving your changes, Git will block you or your work might get lost.

The `git stash` command takes all uncommitted changes from your Working Directory and Staging Area, saves them securely inside a **temporary storage queue (the stash)**, and leaves you with a completely clean working directory.

### 2. Commands and Variations

#### git stash
*   **Description**: Saves all changes in tracked files automatically to the top of the stash stack.
```bash
# Standard stashing operation
git stash
# Output: Saved working directory and index state WIP on main: c2b3a1d Initial commit
```

#### git stash save "message"
*   **Description**: Saves the changes with a custom descriptive note so you can easily remember it later. (In modern Git versions, `git stash push -m "message"` performs the exact same function).
```bash
git stash save "Unfinished CSS fixes on the checkout button"
```

#### git stash -u (or --include-untracked)
*   **Description**: The standard `git stash` command does not include completely new files that Git does not track yet (`untracked` files). This parameter is required to include those untracked files into the temporary storage.
```bash
# Move everything including untracked files into temporary storage
git stash -u
```

---

## Part 2: Inspecting and Managing the Stash

Every entry saved into the stash is stored inside a stack structure. The most recently added stash is always placed at the very top of the list and is represented by the index `stash@{0}`.

### 1. git stash list
*   **Description**: Lists all saved stash records that are not restored yet, along with their index numbers and messages.
```bash
git stash list
# Output:
# stash@{0}: On main: Unfinished CSS fixes on the checkout button
# stash@{1}: WIP on feature-login: d4e5f6a Login structure started
```

### 2. git stash show
*   **Description**: Summarizes which files were changed and how many lines were modified without changing the stash content. Adding the `-p` parameter shows the exact line-by-line code changes (in diff format).
```bash
# Scenario 1: Viewing a file-based summary of the latest stash entry
git stash show

# Scenario 2: Inspecting exact line-by-line code changes inside a specific stash entry
git stash show -p stash@{1}
```

---

## Part 3: Restoring from Stash and Cleaning Operations

When you finish your urgent work and want to go back to your old half-finished code, you must restore the changes from the stash into your working directory. There are two different strategies for this.

### 1. git stash pop (Restore and Remove from Stash)
*   **Description**: Takes the specified stash entry, restores it to your working directory, and **permanently deletes it from the stash list (memory)**. If you do not specify an index, it restores the latest `stash@{0}` entry by default.
```bash
# Restore the latest stashed work and clean it from the list
git stash pop

# Restore a specific index entry and clean it from the list
git stash pop stash@{1}
```

### 2. git stash apply (Restore but Keep in Stash)
*   **Description**: Restores the code changes to your working directory but **does not delete the entry from the stash list**. This is preferred if you want to test the same changes on multiple different branches or keep the code in memory as a backup.
```bash
# Restore codes but keep the entry as stash@{0} in the stash list
git stash apply
```

### 3. Cleaning the Stash Pool

#### git stash drop
*   **Description**: Permanently deletes a specific stash entry from the list using its index number without restoring any code changes to the working directory.
```bash
git stash drop stash@{0}
```

#### git stash clear
*   **Description**: Completely wipes out all records from the stash list with a single command. This operation resets the temporary storage pool and cannot be undone.
```bash
git stash clear
```

