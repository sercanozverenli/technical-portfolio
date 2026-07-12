# Structural Organization of Large Projects: Git LFS and Submodules

This document covers **Git LFS** (Large File Storage), which manages high-capacity files (large datasets, machine learning models, etc.) without bloating the repository, and the **Git Submodules** structure, which allows for integrating one Git repository into another for modular/microservice architectures.

---

## Part 1: Git LFS (Large File Storage) Architecture

Git is designed to track line-by-line changes (diff) in text-based source code. However, the system struggles when high-capacity binary files (e.g., `.csv`, `.parquet`, `.pkl` model weights, high-resolution graphics, or `.mp4` videos) are added to the project.

**What is the problem?**
When you make a small change to a 100 MB dataset and commit it, Git does not delete the old file; it creates a new 100 MB copy. After a few commits, your repository size reaches gigabytes, `git clone` or `git push` operations take hours, and you encounter errors due to GitHub's 100 MB single-file limit.

**How does LFS solve this?**
Instead of keeping large files in your repository's main Git history, Git LFS stores them on an external LFS server. In your local Git repository, only a small 1-2 KB **pointer** text file remains, indicating where that file is located. When you pull the code, LFS intervenes, reads the pointer, and downloads the actual large file.

---

## Part 2: Git LFS Installation and Usage

### A. Activating LFS
After installing the Git LFS tool on your system (downloaded according to your OS), you need to activate LFS globally once for your user account:

```bash
git lfs install
# Output: Git LFS initialized.
```

### B. Tracking Files (.gitattributes)
The `track` command is used to specify which files LFS will manage. You can entrust LFS with large file formats frequently used in data analysis or modeling processes.

```bash
# Track all .csv large datasets with LFS
git lfs track "*.csv"

# Track trained machine learning models (.pkl) with LFS
git lfs track "*.pkl"
```

When you perform this action, a `.gitattributes` file is automatically created (or updated) in your project's root directory. This file, just like `.gitignore`, is read by Git and contains the LFS rules.

### C. Commit and Push Process
After the `track` operation, the added large files and the `.gitattributes` file are committed with standard Git commands.

```bash
git add .gitattributes
git add dataset.csv
git commit -m "feat: 500MB customer dataset included in project via LFS"
git push origin main
```
*During the push process, you can see in the terminal that LFS is triggered and uploading the file in chunks (uploading LFS objects).*

---

## Part 3: Git Submodules Architecture

There may be situations where you need to use another project developed entirely independently within your own project.

For example, assume you have a main project called "PBGO" (PiratesButGoodOnes) that analyzes consumption data. You want to use another repository of yours called "Amplify Core" for this project's data validation operations. Instead of mixing the codes of the two repositories, you can embed Amplify Core into PBGO using the **Submodule** architecture.

**Advantage:** The two repositories are connected, but they continue to maintain their independent commit histories, branches, and tags.

---

## Part 4: Adding and Updating Submodules

### A. Adding a New Submodule
While inside your main project, use the following command to add an external Git repository as a subdirectory:

```bash
# Add the external project as a submodule in the "libs/data-validation" folder
git submodule add git@github.com:username/amplify-core.git libs/data-validation
```

This creates a `.gitmodules` file in the root directory. This file stores the URL of the sub-repository and the local folder path. When you push the main project, the sub-repository's folder appears as a clickable link in the GitHub interface; clicking it takes you directly to that repository's page.

### B. Cloning a Project Containing Submodules
If a colleague downloads your main project containing submodules using standard `git clone`, the submodule folders (e.g., `libs/data-validation`) will arrive **empty**.

The `--recurse-submodules` parameter is required to download the entire system at once:

```bash
git clone --recurse-submodules git@github.com:username/pbgo-main.git
```

*(If you forgot the parameter while cloning, you can pull the sub-repositories later with: `git submodule update --init --recursive`)*

### C. Pulling Submodules to the Latest Version
When the original owner of the sub-repository updates their own project, these changes are not automatically reflected in your main project. You must fetch the updates manually:

```bash
# Update all submodules according to the latest code on their servers
git submodule update --remote --merge

# After the update, commit in the main project to pin the new version
git add .
git commit -m "chore: integrated submodule updates"
```

---

## Part 5: Deleting a Submodule (Critical Operation)

Deleting a submodule in Git architecture is much more complex than simply deleting the folder with the `Delete` key. You must clear the traces of the repository from four different places in the system.

```bash
# 1. Remove the submodule from the cache and detach it from the Git system (De-init)
git submodule deinit -f -- libs/data-validation

# 2. Completely destroy the background folder in the submodule's .git directory
rm -rf .git/modules/libs/data-validation

# 3. Delete the submodule from the Git index (it will also be removed from the .gitmodules file)
git rm -f libs/data-validation

# 4. Save the cleanup and push to the remote server
git commit -m "refactor: data validation submodule completely removed from project"
git push origin main
```
