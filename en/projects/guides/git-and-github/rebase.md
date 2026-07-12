# Git Rebase Guide

This documentation explains the `git rebase` mechanism, its architecture, best practices, and interactive rebase workflows. It is one of the most powerful and most discussed commands in the Git version control system.

---

## 1. Basic Command Structure

When you are working on a `feature` branch and want to update it with the latest changes from the `main` branch, use the following commands:

```bash
# 1. Get the latest changes from the main branch
git checkout main
git pull origin main

# 2. Switch to your feature branch
git checkout feature-branch

# 3. Start the rebase process
git rebase main
```

**Alternative (One-Line Command):**
```bash
git rebase main feature-branch
```
*This command tells Git to rebase `feature-branch` directly onto the `main` branch.*

---

## 2. What is Git Rebase?

`git rebase` moves the starting point (base) of a branch to the latest commit of another branch or commit.

### How It Works

1. Git finds the last common commit (common ancestor) between the target branch (`main`) and your current branch (`feature`).
2. Git saves all commits made after that common commit into a temporary area (`.git/rebase-merge/`).
3. Git resets your current branch to the latest commit of the target branch.
4. Git reapplies the saved commits one by one on top of the new base.

> **Important:** Rebase does not physically move existing commits. Instead, it copies their content and creates **completely new commits with new hash values (SHA-1)**. The old commits remain in the background until Git removes them during Garbage Collection.

---

## 3. Git Rebase vs. Git Merge

| Feature | Git Merge | Git Rebase |
| :--- | :--- | :--- |
| **History Structure** | Branched, real chronological history. | Linear, clean, and organized history. |
| **Commit Structure** | Creates a new merge commit that combines two branches. | Reapplies the content of existing commits onto a new base and creates completely new commits. |
| **Traceability** | Clearly shows where branches split and merged. | Makes it look like the branch was always developed on the main branch. |
| **Conflict Resolution** | All conflicts are resolved at once. | Conflicts are resolved commit by commit during the rebase process. |

---

## 4. Is Rebase Required?

**No, it is not required.** How a project manages its Git history depends entirely on the team's or project's workflow.

* **Teams That Prefer Rebase:** Teams that want a clean, linear history without many unnecessary *"Merge branch 'main' into..."* commits.
* **Teams That Prefer Merge:** Teams that want to keep the original project history exactly as it happened, including the original commit hashes and merge points.

---

## 5. Best Use Cases (Do's)

### A. Updating a Local Branch

Rebase is the safest choice when updating a feature branch that exists only on your local computer and has **not** been pushed to the remote repository.

### B. Cleaning Commit History Before a Pull Request (PR)

Before opening a Pull Request, you can use interactive rebase to clean up your local commit history and present a clear, organized commit sequence to reviewers.

---

## 6. When You Should NOT Use Rebase (Don'ts)

### 🚨 The Golden Rule of Rebasing

> **"Never rebase a public branch that has already been pushed and is being used by other people."**

* **Scenario:** You push a shared branch such as `main`, `develop`, or `feature-xyz` to the remote repository. Later, you rebase that branch locally and force-push (`git push --force`) the rewritten history.
* **Result:** When your teammates run `git pull`, Git will detect that the local and remote histories no longer match. This can create duplicate commits with different hashes, large merge conflicts, and even data loss.

---

## 7. Interactive Rebase (`git rebase -i`)

Interactive rebase is a powerful tool that lets you edit, clean, and organize your local commit history before sharing it with others.

### Starting Interactive Rebase

To edit the last three commits:

```bash
git rebase -i HEAD~3
```

Git opens your default text editor (such as Vim or VS Code) and displays the selected commits from oldest to newest.

```text
pick a1b2c3d Initial draft created
pick e5f6g7h Fixed spelling mistakes
pick j9k0l1m Added test cases

# Commands:
# p, pick <commit> = use the commit as it is
# r, reword <commit> = use the commit but change its message
# e, edit <commit> = use the commit but stop to edit it
# s, squash <commit> = combine it with the previous commit
# f, fixup <commit> = like squash, but discard this commit message
# d, drop <commit> = remove the commit completely
```

### Interactive Rebase Examples

#### 1. Changing a Commit Message (`reword`)

If a commit message contains a typo or needs a better description:

* Change `pick` to `reword` (or `r`) for that commit.
* Save and close the editor.
* Git will open another editor where you can edit the commit message. Save the new message to continue.

#### 2. Combining Multiple Commits (`squash` / `fixup`)

If you created several small commits such as *"small fix"* or *"test"* and want to combine them into one meaningful commit:

```text
pick a1b2c3d Main feature completed
squash e5f6g7h Small bug fix
fixup j9k0l1m Code style improvements
```

* **`squash`:** Combines the changes into the previous commit and lets you edit the final commit message.
* **`fixup`:** Combines the changes into the previous commit but discards this commit's message.

#### 3. Reordering Commits

If you want to change the logical order of commits:

* Simply move the commit lines up or down in the editor.
* Git will replay the commits in the new order.

#### 4. Removing a Commit (`drop`)

* Change `pick` to `drop` (or `d`), or delete the entire line.
* After saving the file, Git removes that commit from the history.

---

## 8. Handling Conflicts During Rebase

Because rebase reapplies commits one by one, conflicts are also handled one commit at a time. If a conflict occurs, the rebase process stops.

1. Use `git status` to find the conflicting files.
2. Open the files and resolve the conflicts manually.
3. Stage the resolved files:

   ```bash
   git add <file-name>
   ```

4. **Important:** Never create a new commit (`git commit`) during a rebase. Continue the process with:

   ```bash
   git rebase --continue
   ```

5. If the rebase becomes too complicated and you want to cancel it completely, use:

   ```bash
   git rebase --abort
   ```
