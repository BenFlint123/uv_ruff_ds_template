# uv_ruff_ds_template

A lightweight scaffold for data-science work: `uv` + Ruff + Jupyter,
structured so you can clone this, get a dataset, and start working
immediately.

> **Template notice:** if you're starting a new project from this
> template, follow [Creating a project from this template](#creating-a-project-from-this-template)
> and then delete that section along with this notice. Everything from
> [Quick start](#quick-start) onwards is written for the project itself.

---

## Creating a project from this template

### 1. Get the template's files into your repo

**A — you can create the repo yourself.** Click **Use this template** on
GitHub, or:

```powershell
gh repo create <project-name> --template BenFlint123/uv_ruff_ds_template --private --clone
cd <project-name>
```

**B — the repo already exists** (e.g. your org created it through a
portal, so it already has its own initial commit). Squash the template's
files onto a branch as one change, with none of the template's history:

```powershell
git clone <your-repo-url>
cd <repo-name>
git checkout -b add-template-scaffold
git remote add template https://github.com/BenFlint123/uv_ruff_ds_template.git
git fetch template
git merge --squash --allow-unrelated-histories template/main
git remote remove template
```

Files your repo already had (typically `README.md`, `.gitignore`,
`LICENSE`) will conflict. Run `git status` to list them, and take the
template's version of each with:

```powershell
git checkout --theirs <path>
git add <path>
```

Don't commit yet. Customise first (step 2), then commit (step 4).

### 2. Customise it

- **Name and description:** the title and intro of this README, the
  headings of `CONTRIBUTING.md`, `CLAUDE.md` and
  `.github/copilot-instructions.md`, and `description` in `pyproject.toml`.
- **Repository URL:** `[project.urls] Repository` in `pyproject.toml`,
  both `url:` fields in `.github/ISSUE_TEMPLATE/config.yml`, and the
  "Report a vulnerability" link in `SECURITY.md`.
- **Ownership:** `LICENSE`, `.github/CODEOWNERS`, and `authors` in
  `pyproject.toml`, if this isn't your project.
- **Package name (optional):** `lib` is deliberately generic, so you
  can keep it. To rename it (PEP 8: lowercase, ideally one word),
  replace `lib` everywhere it appears:
  - the `lib/` directory
  - `pyproject.toml` (`name`, hatch `packages`, ruff `src` /
    `known-first-party`, `--cov=…`, `coverage.run.source`)
  - `tests/test_smoke.py`
  - `.pre-commit-config.yaml` (`files:` patterns)
  - `.github/copilot-instructions.md`, `.github/instructions/*.md`
  - prose in `README.md` and `CONTRIBUTING.md`

### 3. Set up the environment and check it works

Run the [Quick start](#quick-start) steps from `uv sync` onwards (you
already have the repo). `uv sync` updates `uv.lock` to match your edits,
and `pytest` confirms the package still imports.

### 4. Delete this section, then commit

Remove this section and the template notice at the top, then:

```powershell
git add -A
git commit -m "Add data-science project scaffold from template"
git push -u origin HEAD
```

For **B**, open a pull request from `add-template-scaffold` into your
default branch, so CI runs before you merge.

---

## Quick start

You need [`uv`](https://docs.astral.sh/uv/getting-started/installation/)
installed. You don't need a system Python: `uv` downloads the version
pinned in `.python-version` (3.11) automatically.

```powershell
git clone <your-repo-url>
cd <repo-name>

# Create .venv (fetching Python 3.11 if needed), then install the
# project in editable mode along with the dev tools.
uv sync --all-groups

# Register the git hooks: lint/format on commit, tests on push.
uv run pre-commit install
```

Check that everything works:

```powershell
uv run pytest -q        # includes a smoke test that `import lib` works
uv run jupyter lab
```

Then put your dataset in `data/raw/`, start a notebook under
`notebooks/`, and `import lib` will work straight away.

## Project structure & workflow

```
data/
├── raw/         # dataset exactly as received — never edited in place
├── interim/     # working intermediate outputs
└── processed/   # final, analysis-ready data notebooks actually read from
notebooks/       # exploration, numbered by topic (01_explore.ipynb, ...)
lib/              # reusable code shared across notebooks (stub package)
tests/            # tests for lib/
```

- **`data/`** — all three subfolders are gitignored except a `.gitkeep`
  marker, so the folders exist after a fresh clone but no data is ever
  committed. `raw/` is the dataset untouched; `interim/` holds your working
  intermediate outputs; `processed/` is what your notebooks should actually
  read from for analysis.
- **`notebooks/`** — exploratory work. Name files with a numeric prefix
  (`01_explore.ipynb`) so the investigation order is visible in the file
  list. `nbstripout` strips cell outputs automatically before each commit,
  keeping diffs readable.
- **`lib/`** — anything you end up reusing across more than one notebook:
  data loading, cleaning, data-quality checks, plotting helpers. For
  example, a function in `lib/dq.py` is used from any notebook via:
  ```python
  from lib.dq import check_missing_rates
  ```
  This works with no `sys.path` setup because `uv sync` installs `lib` in
  editable mode — the same mechanism in VS Code, plain `jupyter lab`,
  scripts, and pytest.
- **`tests/`** — tests for `lib/` code, not the notebooks themselves.

Need to pull in another project of yours that's already packaged and
released (not on PyPI)? No PyPI required — `uv` installs directly from git:
```powershell
uv add "pkgname @ git+https://github.com/org/repo.git@vX.Y.Z"
```

## Day-to-day commands

```powershell
uv run pytest                     # run tests for lib/ + coverage report
uv run ruff format .              # format
uv run ruff check . --fix         # lint + autofix
uv run pre-commit run --all-files # all commit-stage hooks
uv run jupyter lab                # start notebook server
```

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

BSD 3-Clause — see [LICENSE](LICENSE).
