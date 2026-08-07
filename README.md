# uv_ruff_ds_template

A lightweight scaffold for data-science work: `uv` + Ruff + Jupyter,
structured so you can clone this, get a dataset, and start working
immediately.

> **Status:** Template scaffold. `lib/` is currently a stub. Replace the
> placeholder name and add your own modules as patterns emerge from your
> notebooks.

---

## Using this template

This repository is a generic scaffold. After copying it (fork, "Use this
template", or `git clone`) do the following before adding your own code:

1. **Pick a package name** (PEP 8: lowercase, ideally one word) — or leave
   it as `lib`; it's generic on purpose and doesn't need renaming. If you
   do rename, global-replace `lib` across the workspace, including:
   - the directory `lib/`
   - `pyproject.toml` (`name`, hatch `packages`, ruff `known-first-party`,
     `--cov=…`, `coverage.run.source`)
   - `tests/test_smoke.py`
   - `.github/copilot-instructions.md`, `.github/instructions/*.md`
   - `.pre-commit-config.yaml` (`files:` patterns)
2. **Set the project description** in `pyproject.toml`.
3. **Update the repository URL** in `pyproject.toml` (`[project.urls]
   Repository`) and in `.github/ISSUE_TEMPLATE/config.yml`.
4. **Review `LICENSE`** and **`.github/CODEOWNERS`** — update the
   copyright holder / owner if this isn't your project.
5. Run `uv sync --all-groups` to generate a fresh `uv.lock`.

---

## Quick start

This project uses [`uv`](https://docs.astral.sh/uv/) to manage the Python
toolchain, virtual environment and dependencies. You do **not** need a
system-wide Python 3.11 — `uv` will fetch one for you.

```powershell
# 1. Install uv (one-time, if you don't have it):
#    https://docs.astral.sh/uv/getting-started/installation/

# 2. Clone & enter the repo
git clone <your-repo-url>
cd uv_ruff_ds_template

# 3. Install Python 3.11 (managed by uv) and pin the project to it
uv python install 3.11
uv python pin 3.11

# 4. Create the venv and install the project + dev dependencies
uv venv
uv sync --all-groups

# 5. Install pre-commit hooks
uv run pre-commit install
uv run pre-commit install --hook-type pre-push
```

Verify the install:

```powershell
uv run python -c "import lib; print('lib import OK')"
uv run pytest -q
uv run jupyter lab
```

Drop your dataset into `data/raw/`, open a notebook under `notebooks/`, and
`import lib` works immediately — no extra setup.

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
