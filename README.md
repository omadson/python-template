# python-template

[Copier](https://copier.readthedocs.io/) template for creating Python projects (uv + pre-commit + python-semantic-release + CI/CD).

## Usage

```bash
uv tool install copier  # or: pipx install copier
copier copy --trust gh:omadson/python-template destination/
```

The first question is **`project_type`**, and it drives the rest of the generated structure:

- **package** — installable library: versioned module, semantic-release, PyPI publish, mkdocs site, commitlint.
- **cli** — same as package, plus a [Typer](https://typer.tiangolo.com/) app (`{{ package_module }}/cli.py`) wired up as `[project.scripts]`.
- **script** — a single runnable `main.py` at the root, no module, no build backend, no release machinery.
- **data-science** — an importable module plus `data/{raw,processed}/` and `notebooks/`, with pandas/numpy/matplotlib/seaborn/scikit-learn; also asks `ds_notebook` (Jupyter or marimo) for the starter notebook format.

`script` and `data-science` skip semantic-release, PyPI publishing, mkdocs and commitlint — just ruff, mypy and pytest.

Copier also asks `package_name`, `package_module`, `description`, `author_name`, `author_email`, `github_user`, `python_version`, `year` and `license` (MIT, Apache-2.0, BSD-3-Clause, GPL-3.0-or-later or none — see `copier.yml`). `--trust` is required because the template runs post-generation tasks (`git init`, `uv sync`, `pre-commit install`) automatically.

To update an already-generated project when the template changes:

```bash
copier update
```

On the generated project's GitHub repo, set the `RELEASE_TOKEN` secret (a PAT with push/release permission) and `PYPI_TOKEN` (if publishing to PyPI).

## Structure

The tree below is for `project_type: package` (also what `cli` looks like, plus
`cli.py` and `[project.scripts]`):

```
.
├── commitlint.config.cjs
├── CONTRIBUTING.md
├── docs
│   └── index.md
├── LICENSE
├── my_package
│   └── __init__.py
├── mkdocs.yml
├── pyproject.toml
├── README.md
├── tests
│   └── test_version.py
└── uv.lock
```

`script` drops the module, `docs/`, `mkdocs.yml`, `commitlint.config.cjs`,
`CONTRIBUTING.md` and `.github/workflows/release.yml`, and adds `main.py`
instead:

```
.
├── LICENSE
├── main.py
├── pyproject.toml
├── README.md
├── tests
│   └── test_main.py
└── uv.lock
```

`data-science` keeps the module (no `__version__`, not released) and the same
trimmed-down machinery as `script`, plus `data/` and `notebooks/`:

```
.
├── data
│   ├── raw
│   └── processed
├── LICENSE
├── my_package
│   └── __init__.py
├── notebooks
│   └── exploration.ipynb   # or exploration.py, if ds_notebook = marimo
├── pyproject.toml
├── README.md
├── tests
│   └── test_smoke.py
└── uv.lock
```

Plus a few dotfiles not shown above: `.github/workflows/`, `.gitignore`, `.pre-commit-config.yaml`, `.python-version`, `.copier-answers.yml`.

What matters in each:

- **`pyproject.toml`** — project metadata, dev dependencies, and tool config: `[tool.ruff]` (lint + format), `[tool.mypy]`, `[tool.coverage.report]`, `[tool.interrogate]` (docstring coverage), and `[tool.semantic_release]` (reads the version from `my_package/__init__.py`).
- **`my_package/__init__.py`** — holds `__version__`, the single source of truth that `python-semantic-release` bumps on release.
- **`.pre-commit-config.yaml`** — hooks that run on every commit: ruff, mypy, interrogate, conventional-commit message linting, and a local hook that runs `pytest --cov`.
- **`.github/workflows/ci.yml`** — runs on pull requests: commitlint, tests with coverage, ruff.
- **`.github/workflows/release.yml`** — runs on push to `main`: computes the next version from Conventional Commits, tags, updates the changelog, and optionally publishes to PyPI.
- **`commitlint.config.cjs`** — enforces Conventional Commits (paired with the `conventional-pre-commit` hook and the CI `commitlint` job).
- **`mkdocs.yml` + `docs/`** — docs site config and content (built with `mkdocs-material` + `mkdocstrings`).
- **`LICENSE`** — whichever license you picked when generating (or absent, if you picked none).
- **`.copier-answers.yml`** — the answers you gave Copier, used later by `copier update` to reapply template changes.
- **`tests/test_version.py`** — the one test the template ships with, just asserting `__version__` is set.

## Versioning the template

Copier versions by the **git tags of the template repository** (not a number in a file): `copier copy` uses the latest tag by default, and `copier update` applies the diff between the tag recorded in the generated project's `.copier-answers.yml` and the newest one.

Commits in this repo follow Conventional Commits (`fix:`, `feat:`, etc). On every push to `main`, the `.github/workflows/release.yml` workflow (root) runs `python-semantic-release` in tag-only mode: it creates the next `vX.Y.Z` tag and updates `CHANGELOG.md` (root) automatically — no build/publish, since the template itself isn't a package.

To pin a specific template version when generating:

```bash
copier copy --trust --vcs-ref vX.Y.Z gh:omadson/python-template destination/
```

To update an already-generated project to the newest template version: `copier update` (inside the generated project).

## Repository structure

- `copier.yml` — the template's questions.
- `template/` — content copied into new projects (`*.jinja` files are rendered; everything else is copied as-is).
- `pyproject.toml` (root) — `python-semantic-release` config to version *the template itself* (tag-only, not copied into generated projects).
- `.github/workflows/release.yml` (root) — generates the template's tags/CHANGELOG on every push to `main`.
