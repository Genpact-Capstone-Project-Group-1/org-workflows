# org-workflows

Centralized GitHub Actions reusable workflows for the Genpact Capstone Project Group 1 organization. All linting checks are defined here once and consumed across every repo which ensures no duplication and consistent standards everywhere.

---

## What's in here

```
org-workflows/
├── .github/
│   └── workflows/
│       ├── python-lint.yml     # Reusable Python linting workflow (ruff)
│       └── yaml-lint.yml       # Reusable YAML linting workflow (yamllint)
└── configs/
    ├── ruff.toml               # Shared ruff configuration
    └── .yamllint.yml           # Shared yamllint configuration
```

---

## Workflows

### `python-lint`
Runs [ruff](https://docs.astral.sh/ruff/) for Python linting and format checking.

| Input | Required | Default | Description |
|---|---|---|---|
| `python-version` | No | `3.12` | Python version to use |
| `working-directory` | No | `.` | Directory to run checks in |

### `yaml-lint`
Runs [yamllint](https://yamllint.readthedocs.io/) for YAML syntax and style checking.

| Input | Required | Default | Description |
|---|---|---|---|
| `working-directory` | No | `.` | Directory to run checks in |

---

## Usage

In any repo under this organization, create `.github/workflows/lint.yml`:

```yaml
name: Lint

on:
  pull_request:
    branches: [main]

jobs:
  python-lint:
    uses: Genpact-Capstone-Project-Group-1/org-workflows/.github/workflows/python-lint.yml@main
    with:
      python-version: "3.12"

  yaml-lint:
    uses: Genpact-Capstone-Project-Group-1/org-workflows/.github/workflows/yaml-lint.yml@main
```

That's it. The workflows pull their configs directly from this repo, so no need to copy config files into each project.

---

## Branch Protection

These workflows are set as **required status checks** on `main`. Pull requests cannot be merged until both checks pass.

To apply to a new repo:
1. Go to **Settings → Branches → Edit rule for `main`**
2. Under **Require status checks to pass**, add:
   - `python-lint / python-lint`
   - `yaml-lint / yaml-lint`

Or apply org-wide via **Org Settings → Rules → Rulesets**.

---

## Linting Rules

### Python (`ruff.toml`)
- Line length: **88 characters**
- Target: **Python 3.12**
- Enabled rule sets: pycodestyle (`E`, `W`), pyflakes (`F`), isort (`I`), bugbear (`B`), pyupgrade (`UP`)
- Quote style: double quotes

### YAML (`.yamllint.yml`)
- Max line length: **120 characters**
- Indentation: **2 spaces**
- Truthy values: only `true` / `false` (not `yes`, `on`, etc.)

---

## Updating Rules

1. Make your changes to `configs/ruff.toml` or `configs/.yamllint.yml`
2. Open a PR —> changes take effect in all consuming repos on the next pipeline run
3. For breaking changes, tag a release and pin consuming repos to a version:

```yaml
uses: Genpact-Capstone-Project-Group-1/org-workflows/.github/workflows/python-lint.yml@v1.1.0
```

---

## Local Setup (optional)

Run the same checks locally before pushing using [pre-commit](https://pre-commit.com/):

```bash
pip install pre-commit
pre-commit install
pre-commit run --all-files
```

Add a `.pre-commit-config.yaml` to your repo:

```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.4.4
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format

  - repo: https://github.com/adrienverge/yamllint
    rev: v1.35.1
    hooks:
      - id: yamllint
        args: [-c, .yamllint.yml]
```

---

## Contributing

Changes to this repo affect **all projects in the organization**. Please:
- Open a PR and get at least one review from Admin/DevOps before merging
- Test rule changes against an existing repo before rolling out
- Document any new workflows or config options in this README