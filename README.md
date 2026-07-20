# python-template

[Copier](https://copier.readthedocs.io/) template for creating Python projects (uv + pre-commit + python-semantic-release + CI/CD).

## Usage

```bash
uv tool install copier  # or: pipx install copier
copier copy --trust gh:omadson/python-template destination/
```

Copier will ask for `package_name`, `package_module`, `description`, `author_name`, `author_email`, `github_user`, `python_version`, `year` and `license` (MIT, Apache-2.0, BSD-3-Clause, GPL-3.0-or-later or none — see `copier.yml`). `--trust` is required because the template runs post-generation tasks (`git init`, `uv sync`, `pre-commit install`) automatically.

To update an already-generated project when the template changes:

```bash
copier update
```

On the generated project's GitHub repo, set the `RELEASE_TOKEN` secret (a PAT with push/release permission) and `PYPI_TOKEN` (if publishing to PyPI).

## What's included (in the generated project)

- `.pre-commit-config.yaml` — ruff (lint + format), mypy, interrogate, conventional-pre-commit, coverage via pytest.
- `pyproject.toml` — dev dependencies, coverage/interrogate/mypy/ruff config, and `[tool.semantic_release]` already pointing at the versioning source in `pyproject.toml` + `__init__.py`.
- `.github/workflows/ci.yml` — commitlint, tests with coverage, lint (ruff).
- `.github/workflows/release.yml` — python-semantic-release on the `main` branch, with optional PyPI publish.
- `commitlint.config.cjs` — Conventional Commits.
- `CONTRIBUTING.md`, `.gitignore`, `LICENSE` (as chosen), `mkdocs.yml` + `docs/`.

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
