```markdown
# Versioning and Release Management (Tags & Releases)

This document covers tagging operations for freezing the current state of the code at specific milestones (e.g., first stable version or a major patch) in the development cycle, Semantic Versioning (SemVer) standards, and the processes of converting these tags into downloadable, official releases on GitHub.

---

## 1. Semantic Versioning (SemVer)

The **SemVer (Semantic Versioning)** logic, which is the industry standard, is used when creating Git tags. A version number generally consists of three parts: `v(Major).(Minor).(Patch)`

For architectures like PiratesButGoodOnes (PBGO) or projects involving data validation layers like Amplify Core, you should construct versioning with the following logic:

* **Major (v1.x.x):** Increased when very large, radical architectural changes are made that break backward compatibility. (e.g., A complete change in database structure).
* **Minor (x.1.x):** Increased when a new, significant feature is added to the project without breaking backward compatibility.
* **Patch (x.x.1):** Increased in cases where no new features are added, and only minor bugs are fixed or security vulnerabilities are patched in the existing code.

---

## 2. Git Tag Architecture

A Git tag is a pointer assigned to a specific commit that never changes (fixed) over time. While branches move forward as new commits arrive, tags remain fixed on the commit they were assigned to.

There are two types of tagging methods:

### A. Lightweight Tag
Used only to name a specific commit. It does not contain extra metadata (who created it, date, description message). It is suitable only for local experiments or temporary markings.

```bash
# Adds a lightweight tag to the active commit you are on
git tag v1.0.1
```

### B. Annotated Tag (Recommended)
This is the standard required for actual version releases. It is a full Git object in its own right; it contains who created the tag, the creation date, GPG signature, and a description message.

```bash
# Creating a tag with -a (annotated) and -m (message) parameters
git tag -a v1.1.0 -m "Amplify Core: Data validation layer integrated"
```

---

## 3. Tagging Past Commits and Analysis

Tags do not always have to be applied to the latest commit (HEAD). You can also tag a commit in the past that was later recognized as stable.

```bash
# 1. First, find the hash values of past commits (e.g., 9fceb02)
git log --oneline

# 2. Tag the past by adding the relevant hash value to the end of the command
git tag -a v0.9.0 9fceb02 -m "Beta release candidate version"
```

**Listing and Inspecting Tags:**
```bash
# Lists all tags locally
git tag

# Searches for tags matching a specific pattern (e.g., Only those starting with v1)
git tag -l "v1.*"

# Shows all details of an annotated tag (who, when, with what message)
git show v1.1.0
```

---

## 4. Pushing Tags to the Remote Server (GitHub)

One of the most common mistakes is thinking that tags created locally will go to the server with a standard `git push` command. **By default, the `git push` command does NOT send tags to the server.** You must send tags separately.

```bash
# Scenario 1: Pushing only a specific tag to the remote server (origin)
git push origin v1.1.0

# Scenario 2: Pushing ALL tags you have created locally to the server at once
git push origin --tags
```

---

## 5. Deleting Tags (Local and Remote)

To delete a tag that was incorrectly assigned or assigned to the wrong commit, you must perform operations in both the local database and the remote server.

```bash
# 1. Delete the tag from your local computer (-d: delete)
git tag -d v1.1.0

# 2. Reflect the deletion operation to the remote server and remove it from there as well
git push origin --delete v1.1.0
```

---

## 6. GitHub Releases

Git tags (`v1.0.0`) are pointers at the code base level. **GitHub Releases**, however, is a GitHub-specific interface feature that takes these tags and turns them into a "Product Page" that end-users (or other developers) can read and download.

A GitHub Release page includes:
* **Release Notes:** A human-readable summary of what changed in that version and which bugs were fixed (Changelog).
* **Compiled Assets:** If your project is compiled software (e.g., `.exe`, `.dmg`, `.apk`), the latest compiled version of the code is uploaded here.
* **Source Code:** An automatically generated `.zip` and `.tar.gz` archive containing the code for that specific version.

### How to Create a Release from the GitHub Interface?
1. Click on the **Releases** section on the right side of your GitHub repository's home page.
2. Press the **"Draft a new release"** button.
3. Select a tag you previously pushed from the **"Choose a tag"** section (or type a new tag from the interface; GitHub will create that tag for you).
4. Click the **"Generate release notes"** button to add the automatically generated PR history to the release notes.
5. Upload your compiled binary files (Assets) if you have them, and publish by clicking **"Publish release"**.

```
