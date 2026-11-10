# Contributing to lib

## First-time setup

After cloning the repository:

```powershell
uv sync --all-groups
uv run pre-commit install
```

- `uv sync --all-groups` creates `.venv/`, downloading the Python version
  pinned in `.python-version` (3.11) if needed, so no system Python is
  required. It then installs `lib` in editable mode along with the `dev`
  dependency group.
- `pre-commit install` registers both git hooks: lint/format on commit and
  tests on push. Both stages are listed under `default_install_hook_types`
  in `.pre-commit-config.yaml`.

## Adding a new module

When a helper function starts getting copy-pasted across notebooks, move it
into `lib/`:

1. Add the function to a module under `lib/` (create a new file if it
   doesn't fit an existing one, e.g. `lib/dq.py`, `lib/features.py`).
2. Add a test under `tests/` if the logic is worth protecting against
   regressions (not required for one-off exploratory helpers).
3. Import it from your notebook: `from lib.dq import check_missing_rates`.

## Pre-commit hooks

| Stage | Hook | What it does |
|-------|------|--------------|
| `pre-commit` | `check-case-conflict` | Catches case-insensitive filename conflicts |
| `pre-commit` | `check-merge-conflict` | Blocks accidental merge conflict markers |
| `pre-commit` | `end-of-file-fixer` | Ensures files end with a newline |
| `pre-commit` | `trailing-whitespace` | Strips trailing whitespace |
| `pre-commit` | `check-toml` / `check-yaml` / `check-json` | Validates config file syntax |
| `pre-commit` | `nbstripout` | Strips notebook outputs before commit |
| `pre-commit` | `ruff format` | Formats Python code |
| `pre-commit` | `ruff check` | Lints and auto-fixes Python code |
| `pre-push`   | `pytest` | Runs the test suite for `lib/` (with coverage report; no threshold) |

Run all commit-stage hooks manually at any time:

```powershell
uv run pre-commit run --all-files
```

## Code quality

Before submitting a PR, ensure the following pass locally:

```powershell
uv run ruff format .
uv run ruff check . --fix
uv run pytest
```

CI runs the same checks on `ubuntu-latest` / Python 3.11 against every PR.

## Pull request conventions

Prefix PR titles with the type of change. Supported types:

- `feat` — new feature
- `fix` — bug fix
- `refactor` — code change that is neither a fix nor a feature
- `perf` — performance improvement
- `test` — adding or updating tests
- `docs` — documentation only
- `build` / `ci` — build system or CI/CD changes
- `deps` — dependency updates
- `revert` — reverts a previous commit

Example: `feat: add churn feature helpers`

Link related issues in the PR description using `Closes #<issue>` or
`Relates to #<issue>`.

## Common pitfalls

- ❌ Adding `sys.path` manipulation to `conftest.py`, a notebook, or any
  module → ✅ Rely on the editable install from `uv sync`. If imports fail,
  fix the install, not the path.
- ❌ Adding `python.analysis.extraPaths` to `.vscode/settings.json` → ✅
  Leave it out; it masks the same class of bug.
- ❌ Committing files under `data/` → ✅ Keep data local; only `.gitkeep`
  markers are tracked.
- ❌ Creating `requirements.txt` → ✅ `pyproject.toml` and `uv.lock` are the
  source of truth. Use `uv export -o requirements.txt` if a flat list is
  ever needed.
