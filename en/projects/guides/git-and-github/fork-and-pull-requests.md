# Open Source Architecture: Fork and Pull Request (PR)

This document comprehensively covers how to safely contribute to projects owned by others (Open Source or different team repositories) where you do not have direct write/edit access. It explains the Fork mechanism, the Pull Request (PR) process (a request to merge code), and the dynamics of Code Review.

---

## 1. What is Fork Architecture?

If you want to push code to a project on GitHub, the repository owner must grant you "Collaborator" access. However, in the open-source world, it is impossible to give access to thousands of developers. The mechanism that solves this problem is the **Fork** system.

**Fork** is the process of taking a complete copy of someone else's GitHub repository (including its commit history) and placing it under your own GitHub account. This copy now belongs entirely to you, and you can modify and push changes to it as you wish.

### A. Downloading the Forked Project to Your Local Computer
First, click the "Fork" button in the top right corner of the project on the GitHub interface to copy the project to your own account. Then, clone this copy from your account to your computer:

```bash
# Clone your fork copy, NOT the original project
git clone git@github.com:your-username/project-name.git

cd project-name
```

### B. Setting Up the Upstream (Original Source) Connection
While you are working on your own fork, the original project will continue to be updated with contributions from others. To prevent your repository from falling behind, you must teach your local Git the address of the original repository (upstream).

```bash
# Add the original repository address as a remote named "upstream"
git remote add upstream git@github.com:original-owner/project-name.git

# Verify the connections to ensure both origin (your repo) and upstream (original repo) exist
git remote -v
```

---

## 2. Keeping Your Fork Synchronized with the Original Project

Follow these professional steps to synchronize (update) your own fork with the latest changes made in the original project:

```bash
# 1. Download new updates from the original repository (upstream) to your local database (this will not overwrite your code)
git fetch upstream

# 2. Make sure you are on your local main branch (usually main or master)
git checkout main

# 3. Merge the updated code you downloaded from the original repository into your own main branch
git merge upstream/main

# 4. After updating your own main branch, push this new state to your own GitHub repository (origin)
git push origin main
```
*Performing this process regularly reduces the possibility of conflicts in future PRs to near zero.*

---

## 3. What is a Pull Request (PR) and How to Open One?

You have completed your development in your own fork or in a side branch of a company project. The formal approval and review process you initiate to include this code into the main project (main branch) is called a **Pull Request**. In other words, you are requesting the repository owner, "Would you pull my code into your own?"

### Development Workflow Before a PR:
Instead of working directly on the main branch, it is industry standard to open an isolated branch for every new feature or bug fix.

```bash
# 1. Create a new branch separate from the main branch for a new feature and switch to it
git checkout -b feature/login-page

# 2. Write your code and save the changes
git add .
git commit -m "feat: login page UI design added"

# 3. Push this new branch to your own GitHub repository (origin)
git push -u origin feature/login-page
```

### Opening a PR via the GitHub Interface:
1. After pushing the code, when you enter your own GitHub repository, a yellow/green bar with a **"Compare & pull request"** button will appear. Click this button.
2. Select the original project's `main` branch as the Base (target) and your own repository's `feature/login-page` branch as the Compare (source).
3. In the PR description, write in detail what this code solves and which issue it closes (e.g., `Closes #42`), then click the **"Create pull request"** button.

---

## 4. Code Review and Change Requests

After a PR is opened, team leaders or project maintainers review the code you wrote. At this stage, they can leave line-by-line comments on the GitHub interface or request changes (Request Changes).

**Updating a PR:**
You do **not** need to open a new PR to update an existing one. If you make the requested fixes on the local branch connected to the PR (`feature/login-page`) and push to the same branch again, GitHub automatically adds these new commits to the existing PR.

```bash
# Make the requested fixes
git add .
git commit -m "fix: button colors updated after code review"
git push origin feature/login-page
```

---

## 5. Branch Protection and Mandatory PRs

In corporate projects, pushing code directly to critical branches like `main` or `production` (`git push origin main`) is usually **prohibited**. Repository administrators add **Branch Protection Rules** to these branches via GitHub Settings.

### Common Protection Rules:
* **Require a pull request before merging:** You cannot push code directly; you must open a side branch and go through a PR.
* **Require approvals:** For a PR to be merged, at least X number of authorized people must review and "Approve" the code.
* **Require status checks to pass:** Automatic tests run via GitHub Actions when a PR is opened. The code cannot be merged into the main branch until these tests pass successfully (get a green checkmark).
