# ForTestingSpecficApp

> **A clean, non-real developer scratchpad repository created for testing local dev environments, CLI scripts, shell aliases, editor configs, and repository automation without touching production codebases.**

---

## Table of Contents

- [Overview](#overview)
- [What This Repo Is (and Is Not)](#what-this-repo-is-and-is-not)
- [Developer Experimentation Areas](#developer-experimentation-areas)
- [Repository Baseline Status](#repository-baseline-status)
- [Toolchain & Prerequisites](#toolchain--prerequisites)
- [Getting Started](#getting-started)
- [Testing Shell Scripts & CLI Tools](#testing-shell-scripts--cli-tools)
- [Testing Editor & Formatter Configs](#testing-editor--formatter-configs)
- [Testing Git Hooks & Pre-Commit Checks](#testing-git-hooks--pre-commit-checks)
- [Testing GitHub API & CLI Automation](#testing-github-api--cli-automation)
- [Throwaway Workspace Layout](#throwaway-workspace-layout)
- [Sample GitHub Actions Smoke Workflow](#sample-github-actions-smoke-workflow)
- [Sandbox Hygiene Rules](#sandbox-hygiene-rules)
- [Wiping & Resetting Local State](#wiping--resetting-local-state)
- [License](#license)

---

## Overview

**ForTestingSpecficApp** is an intentionally empty developer repository used as a safe, disposable remote target. Whenever you need to verify a new CLI tool, test how an editor extension behaves on a fresh clone, validate a Git hook, or run automated API calls against a real GitHub repository, you can point your tools here with zero risk.

---

## What This Repo Is (and Is Not)

To avoid confusion for anyone stumbling across this repository:

- **It IS:** A synthetic, non-production dev playground for tooling, scripting, and workflow experiments.
- **It IS:** Completely empty by design, containing no real application source code or runtime dependencies.
- **It IS NOT:** A deployable product, library, or active software service.
- **It IS NOT:** Intended to store sensitive credentials, private configs, or proprietary logic.

---

## Developer Experimentation Areas

Use `ForTestingSpecficApp` to quickly test and validate:

- **Fresh Clone Behavior:** Verify how IDEs, language servers, and dev containers initialize an empty repo.
- **Git Hooks (`pre-commit`, `commit-msg`, `pre-push`):** Test local commit validation scripts and linters.
- **GitHub CLI (`gh`) & REST/GraphQL APIs:** Safely create, label, close, and delete dummy issues, PRs, gists, and releases.
- **Dotfiles & Shell Functions:** Test custom `git` aliases, prompt themes, and directory-aware environment loaders (`direnv`).
- **Package & Task Runners:** Prototype `Makefile`, `Taskfile.yml`, `justfile`, or `npm`/`uv` scripts in isolation.
- **Release & Tag Automation:** Test semantic versioning scripts, tag pushes, and automated release note generators.

---

## Repository Baseline Status

The repository currently sits at its clean initial baseline.

| Item | Current State | Description |
| :--- | :--- | :--- |
| **Source Code** | None | No real or mock application files committed. |
| **Branches** | `main` | Single clean default branch. |
| **Tags / Releases** | None | Available for testing `git tag` and release pipelines. |
| **CI/CD Workflows** | None | Ready for `.github/workflows/` YAML experiments. |
| **Issues / PRs** | Empty | Available for API and webhook payload testing. |

---

## Toolchain & Prerequisites

Depending on what you are testing locally, any standard developer setup works out of the box:

- **Git:** `2.40+`
- **Shell:** `bash`, `zsh`, or `fish`
- **GitHub CLI (`gh`):** Optional, useful for scripting repo interactions from the terminal
- **Task Runners (Optional):** `make`, `just`, or `task`

---

## Getting Started

### 1. Clone the Sandbox

```bash
git clone https://github.com<your-username>/ForTestingSpecficApp.git
cd ForTestingSpecficApp
```

### 2. Spin Up an Isolated Experiment Branch

Always branch off `main` so the default branch remains clean:

```bash
git checkout -b exp/tooling-check
```

### 3. Verify Remote Connectivity

```bash
git remote -v
git status
```

---

## Testing Shell Scripts & CLI Tools

When prototyping shell scripts or testing command-line utilities against a Git working tree, use a temporary `tmp/` directory:

```bash
mkdir -p tmp
cat << 'EOF' > tmp/hello.sh
#!/usr/bin/env bash
set -euo pipefail

echo "Running inside: $(basename "$(pwd)")"
echo "Current branch: $(git branch --show-current)"
EOF

chmod +x tmp/hello.sh
./tmp/hello.sh
```

Remove the scratch script once verified:

```bash
rm -rf tmp
```

---

## Testing Editor & Formatter Configs

Use this empty repo to verify how your editor or formatter handles `.editorconfig`, `.prettierrc`, or `ruff.toml` on newly created files:

```bash
cat << 'EOF' > .editorconfig
root = true

[*]
indent_style = space
indent_size = 2
end_of_line = lf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true
EOF
```

Open the folder in your editor, create a scratch file, save it, and confirm indentation and line endings match your rules.

---

## Testing Git Hooks & Pre-Commit Checks

You can test local Git hooks directly inside `.git/hooks/` without installing third-party hook managers:

```bash
cat << 'EOF' > .git/hooks/commit-msg
#!/usr/bin/env bash
MSG_FILE="$1"
FIRST_LINE=$(head -n1 "$MSG_FILE")

if [[ ! "$FIRST_LINE" =~ ^(feat|fix|chore|docs|test|ci): ]]; then
  echo "Error: Commit message must start with feat:, fix:, chore:, docs:, test:, or ci:"
  exit 1
fi
EOF

chmod +x .git/hooks/commit-msg
```

Test the hook with an invalid commit message:

```bash
git commit --allow-empty -m "bad commit message"
```

Remove the hook when done:

```bash
rm -f .git/hooks/commit-msg
```

---

## Testing GitHub API & CLI Automation

If you are building bots, CLI wrappers, or CI integrations, use `gh` to test repository events safely:

```bash
# Create and immediately close a dummy issue to test webhook payloads
gh issue create --title "Test webhook payload" --body "Synthetic test issue"
gh issue list

# Trigger a dummy workflow run (once a workflow file exists)
gh workflow list
```

---

## Throwaway Workspace Layout

If you temporarily scaffold files while testing a tool, keep them grouped cleanly so you can delete them in one command:

```text
ForTestingSpecficApp/
├── .github/
│   └── workflows/          # Temporary CI workflow YAML files
├── scratch/                # Disposable scripts, configs, and dummy files
├── .editorconfig           # Optional formatting test config
├── .gitignore              # Ignore local temp artifacts
└── README.md               # Repository documentation
```

---

## Sample GitHub Actions Smoke Workflow

Drop this minimal workflow into `.github/workflows/smoke.yml` whenever you want to verify runner execution, environment variables, or secrets:

```yaml
name: Dev Sandbox Smoke Test

on:
  workflow_dispatch:
    inputs:
      test_label:
        description: "Optional label for this test run"
        required: false
        default: "manual-smoke"

jobs:
  smoke:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Print Runner Info
        run: |
          echo "Label: \${{ github.event.inputs.test_label }}"
          echo "OS: \${{ runner.os }}"
          echo "Workspace: \${{ github.workspace }}"
```

---

## Sandbox Hygiene Rules

- **Keep `main` Minimal:** Avoid leaving random scratch files committed to `main`.
- **Use Dummy Data Only:** Never paste real API keys, tokens, passwords, or private hostnames into test files.
- **Delete Remote Branches:** Clean up any `exp/*` branches pushed to `origin` after finishing your test.
- **Close Test Issues & PRs:** Keep the repository issue tracker clear of stale test items.

---

## Wiping & Resetting Local State

If your local working directory becomes cluttered with untracked files, hooks, or test branches, reset everything cleanly:

```bash
# Discard all tracked changes and switch back to main
git checkout main
git reset --hard origin/main

# Remove all untracked files and directories
git clean -fdx

# Delete all local branches except main
git branch | grep -v "main" | xargs -r git branch -D
```

---

## License

Unlicensed — created purely as a non-production developer testing sandbox.
