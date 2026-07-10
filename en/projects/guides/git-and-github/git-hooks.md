# Git Hooks and Local Automation Scripts

This document covers Bash/Python scripts (Hooks) that are triggered automatically immediately before or after Git events (commit, push, merge, etc.), the setup of `pre-commit` and `pre-push` hooks that ensure code quality, and how to share these configurations with team members.

---

## 1. What are Git Hooks and Where are They Located?

**Git Hooks** are scripts that intervene when specific Git commands are executed, allowing you to perform custom operations. They are divided into two main categories:
* **Client-Side:** Hooks that run on your local computer (when committing, changing branches, etc.).
* **Server-Side:** Hooks that run on servers like GitHub/GitLab (when a push is received).

If you go to the `.git/hooks/` directory in a local Git repository, you will see template files prepared by Git that are not active. As soon as the `.sample` extension at the end of these files is removed, the hook becomes active.

```bash
# Listing hook templates in the project
ls -la .git/hooks/

# Example Output:
# applypatch-msg.sample
# commit-msg.sample
# pre-commit.sample
# pre-push.sample
```

---

## 2. Most Frequently Used Client-Side Hooks

### A. pre-commit
It runs immediately before a commit is created (the moment you type `git commit` and press Enter, before the message screen opens).
* **Purpose:** To prevent forgotten `print()` or `breakpoint()` debugging commands from leaking into the code, to run code formatters (Black, Flake8), and to check if large data files (.csv) have been added accidentally.

### B. commit-msg
It runs immediately after you write and save the commit message, but before the commit is finalized.
* **Purpose:** To check if commit messages comply with a specific standard (e.g., Conventional Commits - `feat:`, `fix:`) or to automatically add the Jira/Trello Issue number to the message.

### C. pre-push
It is triggered when the `git push` command is executed, immediately before the data starts being transferred to the remote server.
* **Purpose:** To run local unit tests (e.g., `pytest`) to prevent broken code from reaching the server. If tests fail, the push process is aborted.

---

## 3. Creating a Custom Hook Step-by-Step

In a Python-based data project, let's write a `pre-commit` hook that checks whether a forgotten `breakpoint()` function exists in the code before committing.

**Step 1:** Activate the hook by renaming the template file.
```bash
mv .git/hooks/pre-commit.sample .git/hooks/pre-commit
```

**Step 2:** Give the file execution permissions (mandatory for Linux/macOS).
```bash
chmod +x .git/hooks/pre-commit
```

**Step 3:** Open the file with a text editor and write the following Bash script:
```bash
#!/bin/sh
# Find staged Python files in the project
STAGED_FILES=$(git diff --cached --name-only --diff-filter=ACM | grep ".py$")

if [ -z "$STAGED_FILES" ]; then
    exit 0
fi

echo "🔍 Analyzing Python files..."

# Check if 'breakpoint()' exists in the files
for FILE in $STAGED_FILES; do
    if grep -q "breakpoint()" "$FILE"; then
        echo "❌ ERROR: A forgotten 'breakpoint()' found in file $FILE!"
        echo "Please delete debugging code and commit again."
        exit 1 # Abort the operation (Commit is not made)
    fi
done

echo "✅ Code clean, proceeding with commit."
exit 0 # Complete the operation successfully
```

---

## 4. Bypassing Hooks

Sometimes there is an emergency, and you might want to commit directly without being caught by the rules in `pre-commit` or `commit-msg` hooks (e.g., you haven't fixed the tests yet but want to back up the code).

For this, the `--no-verify` parameter is used:

```bash
# Force commit ignoring hooks
git commit -m "wip: tests broken but code backed up" --no-verify

# Push ignoring hooks
git push origin main --no-verify
```

---

## 5. Sharing (Integrating) Hooks Within the Team

One of the biggest problems with Git architecture is that the `.git/hooks/` folder is **not** included in version control. This means a fantastic `pre-commit` script you wrote does not go to your teammate who clones the project.

There are two professional ways to solve this:

### Method A: Changing the Folder Path with Git Config
A normal folder named `.githooks` is created in the project's root directory. All scripts are placed here, and this folder is committed and pushed to the server. When a teammate downloads the project, they run the following command to tell Git to read hooks from here:

```bash
git config core.hooksPath .githooks
```

### Method B: Using a Framework (Recommended)
Especially in the Python ecosystem, instead of managing hooks with manual Bash scripts, an external package called `pre-commit` is used. A `.pre-commit-config.yaml` file is added to the project.

```bash
# Install the framework
pip install pre-commit

# Automatically construct hooks in the .git/hooks folder according to the config file
pre-commit install
```
*This way, every developer joining the project can automatically install all standard test and formatting rules to their computer by running the `pre-commit install` command once.*
