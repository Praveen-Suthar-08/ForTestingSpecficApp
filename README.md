# ForTestingSpecficApp

> **A developer sandbox and empty repository baseline built specifically for testing Git diffs, patch files, merge conflict resolution, PR review bots, and VCS comparison tools.**

---

## Table of Contents

- [Purpose of This Repository](#purpose-of-this-repository)
- [Diff & VCS Testing Scenarios](#diff--vcs-testing-scenarios)
- [Current Repository State](#current-repository-state)
- [Local Environment Setup](#local-environment-setup)
- [Git Diff Configuration Tips](#git-diff-configuration-tips)
- [Branching Strategy for Diff Tests](#branching-strategy-for-diff-tests)
- [Quick Start: Simulating Diffs](#quick-start-simulating-diffs)
- [Testing Merge & Rebase Conflicts](#testing-merge--rebase-conflicts)
- [Generating & Applying Patch Files](#generating--applying-patch-files)
- [Directory Blueprint for Dummy Files](#directory-blueprint-for-dummy-files)
- [CI/CD Diff & PR Check Sandbox](#cicd-diff--pr-check-sandbox)
- [Safety & Hygiene Rules](#safety--hygiene-rules)
- [Resetting the Repository](#resetting-the-repository)
- [License](#license)

---

## Purpose of This Repository

**ForTestingSpecficApp** is an intentionally empty, non-production repository created as a clean baseline for developers testing file differences, commit histories, pull request diffs, and automated code-review tooling.

Because there is no real application logic here, every line added, modified, renamed, or deleted is completely disposable—making it ideal for stress-testing diff algorithms, formatting checkers, and Git clients without risking real source code.

---

## Diff & VCS Testing Scenarios

Use this repository as a controlled scratchpad to test:

- **Line-by-Line & Word-Level Diffs:** Whitespace changes, EOL conversions (`LF` vs `CRLF`), indentation shifts, and trailing newlines.
- **File Operations:** File renames, moves across directories, permission mode changes (`chmod +x`), symlinks, and binary file diffs.
- **Pull Request UI & Review Bots:** Large multi-file PRs, inline review comments, suggested changes, and automated PR summarizers.
- **Merge & Rebase Conflicts:** Three-way merges, overlapping line edits, delete/modify conflicts, and octopus merges.
- **Patch Workflows:** Exporting `.patch` / `.diff` files with `git format-patch` and applying them via `git apply` or `git am`.
- **Diff Algorithms:** Comparing output across `myers`, `minimal`, `patience`, and `histogram` diff strategies.

---

## Current Repository State

This repository contains no application code or dependencies and serves purely as an empty baseline.

| Component | Status | Notes |
| :--- | :--- | :--- |
| **Source Files** | Empty | No application code exists on `main`. |
| **Commit History** | Minimal | Contains only baseline repository initialization files. |
| **Diff Baseline** | Clean | `main` acts as the immutable reference point for comparisons. |
| **CI Workflows** | Unconfigured | Ready for PR diff-check or linter workflow experiments. |
| **Branches** | `main` | Create temporary branches for each diff test case. |

---

## Local Environment Setup

Ensure you have standard Git and diff utilities available locally:

- **Git:** `v2.40+`
- **GitHub CLI (`gh`):** Optional, for creating and viewing PR diffs from the terminal (`gh pr diff`)
- **Diff Viewers (Optional):** `delta`, `difftastic`, `meld`, `vimdiff`, or VS Code diff editor

### Verify Your Git Diff Settings

Check your active diff configuration before running experiments:

```bash
git config --list | grep diff
```

---

## Git Diff Configuration Tips

When testing how different tools render changes in `ForTestingSpecficApp`, you can toggle Git's built-in diff options per command:

```bash
# Compare using the histogram algorithm (cleaner for moved blocks)
git diff --diff-algorithm=histogram

# Highlight word-level changes inline instead of full lines
git diff --word-diff

# Detect file renames and copies aggressively
git diff -M -C

# Ignore all whitespace differences
git diff -w
```

---

## Branching Strategy for Diff Tests

Keep `main` clean and isolate each diff experiment on a dedicated branch:

- `main` — Clean baseline state; avoid force-pushing or cluttering with dummy files.
- `diff/whitespace-<id>` — Testing line endings, tabs vs spaces, and blank-line diffs.
- `diff/rename-<id>` — Testing file moves, splits, and similarity thresholds.
- `diff/conflict-<id>` — Deliberate conflicting edits for merge/rebase testing.
- `diff/large-pr-<id>` — Multi-file dummy payloads to test PR rendering limits.

---

## Quick Start: Simulating Diffs

### 1. Clone the Repository

```bash
git clone https://github.com<your-username>/ForTestingSpecficApp.git
cd ForTestingSpecficApp
```

### 2. Create a Baseline Dummy File on a Test Branch

```bash
git checkout -b diff/sample-test
mkdir -p sandbox
cat << 'EOF' > sandbox/sample.txt
line 1: initial baseline
line 2: unchanged context
line 3: target for modification
line 4: trailing context
EOF
git add sandbox/sample.txt
git commit -m "test: add baseline sample file"
```

### 3. Modify the File and Inspect the Unified Diff

```bash
cat << 'EOF' > sandbox/sample.txt
line 1: initial baseline
line 2: unchanged context
line 3: updated value for diff verification
line 4: trailing context
line 5: newly appended line
EOF
git diff
```

### 4. Stage and Compare Cached vs Working Tree

```bash
git add sandbox/sample.txt
git diff --cached
```

---

## Testing Merge & Rebase Conflicts

To simulate a controlled three-way merge conflict between two branches:

```bash
# Branch A modifies line 1
git checkout -b diff/branch-a main
echo "Version A content" > conflict-test.txt
git add conflict-test.txt && git commit -m "test: branch A edit"

# Branch B modifies the same file differently from main
git checkout -b diff/branch-b main
echo "Version B content" > conflict-test.txt
git add conflict-test.txt && git commit -m "test: branch B edit"

# Merge Branch A into Branch B to trigger a conflict
git merge diff/branch-a
```

Inspect the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) using:

```bash
git diff --cc
```

Abort the test merge cleanly when finished:

```bash
git merge --abort
```

---

## Generating & Applying Patch Files

Use this sandbox to verify `.patch` and `.diff` portability across environments:

```bash
# Export the last commit as a formatted patch file
git format-patch -1 HEAD -o patches/

# Check if a patch applies cleanly without modifying files
git apply --check patches/0001-*.patch

# Apply a raw unified diff
git apply patches/0001-*.patch
```

---

## Directory Blueprint for Dummy Files

If you generate synthetic files for multi-file diff tests, organize them under a throwaway directory so they are easy to purge later:

```text
ForTestingSpecficApp/
├── patches/                # Generated .patch and .diff artifacts
├── sandbox/                # Disposable dummy files for diff/merge tests
│   ├── nested/             # Subdirectory for testing file moves & renames
│   └── fixtures/           # Sample text, JSON, or YAML payloads
├── .gitignore              # Ignore local scratch logs or editor temp files
└── README.md               # Repository documentation
```

---

## CI/CD Diff & PR Check Sandbox

To test automated PR diff inspection or changed-file path filters in GitHub Actions, add `.github/workflows/diff-check.yml`:

```yaml
name: PR Diff Inspector

on:
  pull_request:
    branches: [ main ]

jobs:
  inspect-diff:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout with Full History
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: List Changed Files
        run: |
          git diff --name-status origin/\${{ github.base_ref }}...HEAD

      - name: Show Diff Stat Summary
        run: |
          git diff --stat origin/\${{ github.base_ref }}...HEAD
```

---

## Safety & Hygiene Rules

- **No Real Code or Data:** Never commit proprietary source code, customer records, or production configs into this sandbox.
- **No Secrets:** Use synthetic dummy strings only; never test secret-scanning tools with real API keys or tokens.
- **Clean Up Remote Branches:** Delete temporary `diff/*` branches after closing test pull requests.

---

## Resetting the Repository

Return your local clone to the exact state of `origin/main` after running diff or conflict experiments:

```bash
git checkout main
git fetch origin
git reset --hard origin/main
git clean -fd
```

Delete all local test branches except `main`:

```bash
git branch | grep -v "main" | xargs -r git branch -D
```

---

## License

Unlicensed — intended strictly for internal developer testing and VCS experimentation.
