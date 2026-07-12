# CI/CD Automation and GitHub Actions Architecture

This document details the GitHub Actions architecture, CI/CD principles, and YAML-based workflow configuration that automate testing, building, and deployment processes in the software development lifecycle without human intervention.

---

## 1. What is the CI/CD Concept?

The main purpose of GitHub Actions is to run **CI/CD** (Continuous Integration and Continuous Deployment) processes.

* **Continuous Integration (CI):** When code from multiple developers is pushed to the main repository (main branch), **automated tests** are run to determine whether this code breaks the existing system.
* **Continuous Deployment (CD):** The transfer of code that has successfully passed the CI phase (tests) directly to a live server, cloud service, or package manager (e.g., PyPI) without the need for any manual FTP/SSH operations.

---

## 2. Basic Components of GitHub Actions (Anatomy)

The system consists of 5 core components that are hierarchically linked to each other:

1.  **Workflow:** The highest-level automated process written in YAML format, containing one or more jobs. It is stored in the `.github/workflows/` directory of the project.
2.  **Event:** The trigger that determines when a workflow will run (e.g., when a push is made to the repository, a PR is opened, or every night at 03:00).
3.  **Runner:** The virtual machine (VM) on which the workflow runs. GitHub provides you with Ubuntu, Windows, and macOS servers for free by default.
4.  **Job:** A set of steps that execute on the same runner. A workflow can have multiple jobs, and by default, they run in parallel (simultaneously).
5.  **Step:** Individual operations inside a job. A step either runs a direct terminal command (`run: npm install`) or uses a pre-built action (`uses: actions/checkout@v4`).

---

## 3. Creating Your First Workflow (YAML Architecture)

Let's write a sample CI (Continuous Integration) workflow for a Python project (e.g., an architecture like Amplify Core or PBGO) that tests datasets or contains validation layers.

This file must be saved in the root directory of the repository at the following path: `.github/workflows/python-ci.yml`

```yaml
# 1. Name of the Workflow (This name appears in the GitHub Actions tab)
name: Data Validation CI

# 2. Triggers (Under what conditions will it run?)
on:
  push:
    branches: [ "main", "development" ]
  pull_request:
    branches: [ "main" ]

# 3. Jobs
jobs:
  test-and-validate:
    # The operating system it will run on
    runs-on: ubuntu-latest

    # Steps (Executed sequentially)
    steps:
      # Step 1: Copy the code from the GitHub repository into the runner (virtual machine)
      - name: Checkout Code
        uses: actions/checkout@v4

      # Step 2: Install a specific version of Python on the virtual machine
      - name: Set up Python 3.10
        uses: actions/setup-python@v5
        with:
          python-version: '3.10'

      # Step 3: Install the project's data processing and testing dependencies
      - name: Install Dependencies
        run: |
          python -m pip install --upgrade pip
          pip install pandas numpy pytest scikit-learn
          if [ -f requirements.txt ]; then pip install -r requirements.txt; fi

      # Step 4: Run the data validation tests
      - name: Run Tests with Pytest
        run: pytest tests/
```

**What does this structure do?**
When a developer pushes code to the `main` branch or opens a PR, GitHub provisions a clean Ubuntu Linux server in the cloud. It pulls the code into it, installs Python, installs libraries like Pandas/NumPy, and runs `pytest`. If even one of the tests fails, GitHub marks that PR with a red cross, blocking it from being merged.

---

## 4. Advanced Triggers (Events)

You can trigger a workflow by events other than code pushes. The most frequently used other events are:

**A. Scheduled Tasks (Cron - Schedule)**
For example, you can schedule a workflow to run like an alarm clock to back up a database or generate a weekly data analysis report.
```yaml
on:
  schedule:
    # Runs every night at 00:00 (in the UTC time zone)
    - cron: '0 0 * * *'
```

**B. Manual Triggering (Workflow Dispatch)**
Used when you want to start a workflow manually with your own hands by clicking a button on the GitHub interface. Additionally, you can request parameters (inputs) from the interface.
```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Select the environment for deployment'
        required: true
        default: 'test'
        type: choice
        options:
          - test
          - production
```

---

## 5. Multi-Environment Testing (Matrix Build)

The **Matrix** strategy is used to test whether the code or algorithm you wrote works simultaneously in Python 3.8, 3.9, and 3.10, or compiles without errors on both Linux and Windows.

```yaml
jobs:
  multi-test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        python-version: ['3.9', '3.10']
    
    steps:
      - uses: actions/checkout@v4
      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
```
*This structure provisions separate runners for each combination of operating system and Python version you specify (a total of 4 different runners) and executes the tests in parallel.*

---

## 6. Secure Data Management: GitHub Secrets

Workflows may need to connect to AWS, Docker Hub, or an external API service while running. You must never write API keys, passwords, or tokens as plain text inside the YAML file (the code).

**How to Do It?**
1. Go to the **Settings -> Secrets and variables -> Actions** tab in your GitHub repository.
2. Click the **New repository secret** button (e.g., Name: `API_KEY`, Value: `12345-abcde`).
3. Call this secret safely inside your YAML file like this:

```yaml
      - name: Send Data to API
        run: python senddata.py
        env:
          SECRET_API_KEY: ${{ secrets.API_KEY }}
```
