# Git Basics (Terminal and Git Infrastructure)

This document covers the steps of setting up a local workspace on your computer, creating, managing, and saving files on a single branch.

---

## Section 1: Basic Terminal Operations

To run Git commands in the correct folder and navigate between directories without getting lost, you must master all variations of these commands. *(Note: To run `ls` and `touch` commands on Windows CMD, Git Bash or PowerShell should be used.)*

### 1. Location Detection and Listing

#### pwd (Print Working Directory)
* **Description**: Shows the folder path the terminal is currently focused on, starting from the root directory. It should be checked before every operation to avoid running a Git command in the wrong folder.
```bash
pwd
# Output: /c/Users/Developer/Desktop/projects/website
```

#### ls (List)
* **Description**: Lists all files and folders under the active folder you are currently in. Using its parameters, it allows you to see hidden Git files and file details.
```bash
# Scenario 1: Listing only visible files and folders
ls

# Scenario 2: Listing hidden files as well (like the .git folder)
ls -a

# Scenario 3: Detailed listing with file sizes, creation dates, and permissions
ls -l

# Scenario 4: Show both hidden files and list them in detail (Most frequently used)
ls -la
```

### 2. Directory Navigation

#### cd (Change Directory)
* **Description**: Allows you to switch between folders. You need to know how to move up in the folder tree, return to the root directory, and manage folder names with spaces.
```bash
# Scenario 1: Entering a subfolder within your current location
cd Projects

# Scenario 2: Moving exactly ONE folder UP (to the parent directory)
cd ..

# Scenario 3: Moving TWO folders UP at the same time
cd ../..

# Scenario 4: Returning directly to the computer's home directory
cd ~

# Scenario 5: Entering a folder with a space in its name (Using quotation marks)
cd "New Project Folder"
```

### 3. Directory and File Manipulation

#### mkdir (Make Directory)
* **Description**: Creates new folders in your current location. It has the ability to create nested folder trees with a single command.
```bash
# Scenario 1: Creating a single empty folder in your current location
mkdir e-commerce-project

# Scenario 2: Creating a nested subfolder structure with a single command (-p parameter)
mkdir -p mobile-app/src/components
```

#### touch
* **Description**: Creates an empty file along with its extension inside your current folder. It can create multiple files of different types at the same time in a single line.
```bash
# Scenario 1: Creating a single empty file
touch index.html

# Scenario 2: Creating multiple files with different extensions in a single line
touch styles.css app.js README.md
```

---

## Section 2: Git Initial Setup and Tracking

After entering your project using terminal commands, below are the first Git configuration and tracking commands you will use to manage your project.

### 1. Identity Management

#### git config
* **Description**: Defines a global or project-specific (local) identity for the Git system. If this setting is not made, Git will not allow you to make a commit.
```bash
# Scenario 1: Defining a global identity valid for all projects on the entire computer
git config --global user.name "John Doe"
git config --global user.email "john@example.com"

# Scenario 2: Defining a different identity specific only to the project you are working on
git config --local user.name "John Company"
git config --local user.email "john@company.com"

# Scenario 3: Checking the currently defined information in the terminal
git config --list
```

### 2. Initializing a Repository

#### git init
* **Description**: Turns an ordinary folder into a local Git repository. In the background, it creates the hidden `.git` folder where it will write all the history.
```bash
# Scenario 1: Starting Git tracking inside the current folder
git init

# Scenario 2: Creating a new folder with the specified name and initializing it as a Git repository
git init scratch-project
```

### 3. Status Analysis and Short Reporting

#### git status
* **Description**: Takes a snapshot of the project's current status. It shows which files have been modified and which ones have been queued for saving (Staging Area).
```bash
# Scenario 1: Getting a detailed and long status report
git status

# Scenario 2: Summarizing the status briefly and concisely in a single line for crowded projects
git status -s
```

---

## Section 3: Tracking Mechanism and Saving

To understand how Git processes files, you need to know the file status lifecycle. Files go through these 4 stages:
1.  **Untracked**: Files newly added to the project that Git is not yet aware of.
2.  **Modified**: Files that Git is already tracking but have been changed and not yet saved.
3.  **Staging (Preparation Area)**: Files packaged to be included in the next commit.
4.  **Unmodified**: Up-to-date files permanently saved to the local repository via a commit.

### 1. Staging Variations

#### git add
* **Description**: Moves changes to the Staging Area. It gives Git the instruction: "I will include these files in the next save operation."
```bash
# Scenario 1: Adding only one specific file to the staging area
git add index.html

# Scenario 2: Adding ALL new, deleted, or modified files in the project at once
git add .

# Scenario 3: Adding only files of a certain format (e.g., all CSS files)
git add *.css
```

### 2. Secure Saving and Message Standards

#### git commit
* **Description**: Permanently processes the files in the staging area into the history of the local repository as a version by assigning a unique ID (SHA-1 code).
```bash
# Scenario 1: Making a commit with a standard message (Most frequently used)
git commit -m "Home page slider area coded"

# Scenario 2: Automatically adding and committing modified files that you forgot to `git add` (-am)
# (Critical Note: This command does not cover completely newly created [untracked] files, only modified ones)
git commit -am "CSS color fixes applied"
```

---

## Section 4: The Invisible Wall: .gitignore

### 1. What is .gitignore and Why is it Crucial?
There are files in your project that do not need to be tracked by Git, pose a security risk if sent to the remote server (GitHub), or unnecessarily increase the project size. 
* **node_modules**: External libraries (Everyone can download them on their own computer, they should not be stored).
* **Workspace Files (.env)**: Information that must remain confidential, such as database passwords and API keys.
* **Operating System Files (.DS_Store, thumbs.db)**: Junk files automatically generated by systems.

By writing the names or extensions of these files into a plain text file named `.gitignore` created in the root directory of the project, you ensure that Git completely ignores them.

### 2. Syntax Rules
```text
# 1. Hiding a specific file
.env

# 2. Hiding a specific folder and everything inside it
node_modules/
dist/

# 3. Hiding all files with a specific extension
*.log
*.exe

# 4. Defining Exceptions (Saying "do not hide this" by putting an exclamation mark at the beginning)
# Hide all log files but track only the important.log file:
*.log
!important.log
```

### 3. Crisis Scenario: A Late .gitignore File
* **Problem**: You created a `.env` file in your project, committed it, and Git memorized it. Later, you remembered and added the `.env` file into `.gitignore`. However, the file still continues to appear on the `git status` screen and is still being tracked. This is because Git does not let go of a file it is already tracking.
* **Solution**: Without deleting the file from your computer, you must remove it only from Git's memory (cache).

```bash
# Crisis Solution Step: Removes the file only from Git tracking without deleting it from the disk
git rm --cached .env

# After this process, a commit is made to verify the status:
git commit -m "Crisis Resolved: .env file removed from memory and handled by gitignore"
```
