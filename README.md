# 🔐 Secret Scanner & Linter GitHub Actions

This repository provides GitHub Actions workflows for:

- **Linting code** using [Super-Linter](https://github.com/github/super-linter)
- **Scanning pull requests for potential secret leaks** using a custom AI-based secret scanner

It includes configuration files, automation logic, and ignore rules to help maintain a secure and clean codebase.

---

## 🚨 Secret Leak Scanner

### 🔁 Workflow Overview

1. **`trigger-scan.yaml`** (on `pull_request_target`):
   - Identifies changed files in PR.
   - Invokes `trusted-scan.yaml` with those files.

2. **`trusted-scan.yaml`** (reusable workflow):
   - Checks out the PR source.
   - Loads `.leakignore` patterns.
   - Sends each file’s content to a configurable AI endpoint.
   - If any file receives a risk score ≥ 50, the job fails.

### 🔒 `.leakignore`

Use this file to list regex patterns or string literals that should be excluded from the leak scoring (e.g., test tokens, fake passwords).

---

## 🧹 Linter

### 📄 `linter.yaml`

Runs on every `push` or `pull_request`. Uses GitHub’s [Super-Linter](https://github.com/github/super-linter) with Python support selectively enabled.

Disabled validations:

- `black`
- `flake8`
- `mypy`
- `isort`
- `jscpd`

To update behavior or enable more checks, modify the environment flags inside the workflow.

---

## 🔧 Setup

### Required Secrets

Ensure your repository has the following **GitHub secrets** configured:

- `AIAPIURL`: URL of your AI-based secret detection service.
- `AIAPIKEY`: Authorization token for the AI service.

### Optional Configuration

- `.leakignore`: Define ignore rules for secret detection.
- `standarts/pylintrc`: Custom Pylint config if you enable Python linting rules.

### Installation

- Clone repository of default actions: `git clone git@github.com:sdn-sense/default-actions.git`
- Export variable pointing to the cloned directory:

```sh
export DEFACTIONS=$(pwd)/default-actions
```

- In your own repo, create needed directories:

```sh
mkdir -p .github/workflows/
mkdir -p .github/linters/
mkdir -p standarts/
ls -l .github/workflows/
```

- Copy workflow yamls, pylintrc, and leaksignore file:

```sh
cp "${DEFACTIONS}"/github/workflows/* .github/workflows/
cp "${DEFACTIONS}/standarts/pylintrc" standarts/pylintrc
ln -sf ../../standarts/pylintrc .github/linters/.python-lint
cp "${DEFACTIONS}/leakignore" .leakignore
git diff --color
```

- Push your changes to github (Change git push as needed, based on your repo configuration):

```sh
git add .github/workflows/linter.yaml .github/linters/ .github/workflows/trigger-scan.yaml .github/workflows/trusted-scan.yaml standarts/pylintrc .leakignore
git commit -m "Update linters and scans ($(date +'%Y-%m-%d'))"
git push origin master
```

---

## 🧪 Example Usage

For **Secret Scanning**, simply open or update a PR. The system will:

- Detect changed files.
- Run leak analysis.
- Block merge if secrets are found.

For **Linting**, push code or open a PR to see linting results.

---

## 📌 Exit Codes (Secret Scanner)

| Code | Meaning                         |
|------|---------------------------------|
| 0    | All files scanned successfully  |
| 1    | Secret leak detected            |
| 2    | Malformed payload JSON          |
| 3    | Curl request to AI API failed   |

---

## 🧩 Contributions

Feel free to fork, adapt, or extend this to your organization’s needs. PRs and improvements are welcome!

---

## 🛡️ Disclaimer

This project includes integration with an external AI service for leak detection. Always ensure sensitive data handling aligns with your organization's security policies.

---
