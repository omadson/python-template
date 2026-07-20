# python-template

Template [Copier](https://copier.readthedocs.io/) para criação de projetos Python (uv + pre-commit + python-semantic-release + CI/CD).

## Uso

```bash
uv tool install copier  # ou: pipx install copier
copier copy --trust gh:omadson/python-template destino/
```

O Copier vai perguntar `package_name`, `package_module`, `description`, `author_name`, `author_email`, `github_user`, `python_version`, `year` e `license` (MIT, Apache-2.0, BSD-3-Clause, GPL-3.0-or-later ou nenhuma — veja `copier.yml`). O `--trust` é necessário porque o template roda tarefas pós-geração (`git init`, `uv sync`, `pre-commit install`) automaticamente.

Para atualizar um projeto já gerado quando o template mudar:

```bash
copier update
```

No GitHub do projeto gerado, configure os secrets `RELEASE_TOKEN` (PAT com permissão de push/release) e `PYPI_TOKEN` (se for publicar no PyPI).

## O que vem incluído (no projeto gerado)

- `.pre-commit-config.yaml` — ruff (lint + format), mypy, interrogate, conventional-pre-commit, cobertura via pytest.
- `pyproject.toml` — dependências dev, config de coverage/interrogate/mypy/ruff, e `[tool.semantic_release]` já apontando pro padrão de versionamento em `pyproject.toml` + `__init__.py`.
- `.github/workflows/ci.yml` — commitlint, testes com cobertura, lint (ruff).
- `.github/workflows/release.yml` — python-semantic-release na branch `main`, com publish opcional no PyPI.
- `commitlint.config.cjs` — Conventional Commits.
- `CONTRIBUTING.md`, `.gitignore`, `LICENSE` (conforme escolhido), `mkdocs.yml` + `docs/`.

## Versionamento do template

O Copier versiona pelo **tags git do repositório do template** (não por um número em arquivo): `copier copy` usa a última tag por padrão, e `copier update` aplica o diff entre a tag registrada no `.copier-answers.yml` do projeto gerado e a mais nova.

Commits neste repo seguem Conventional Commits (`fix:`, `feat:`, etc). A cada push na `main`, o workflow `.github/workflows/release.yml` (raiz) roda o `python-semantic-release` em modo tag-only: cria a próxima tag `vX.Y.Z` e atualiza o `CHANGELOG.md` (raiz) automaticamente — sem build/publish, já que o template não é um pacote.

Para fixar uma versão específica do template ao gerar:

```bash
copier copy --trust --vcs-ref vX.Y.Z gh:omadson/python-template destino/
```

Para atualizar um projeto já gerado para a versão mais nova do template: `copier update` (dentro do projeto gerado).

## Estrutura deste repositório

- `copier.yml` — perguntas do template.
- `template/` — conteúdo copiado para os novos projetos (arquivos `*.jinja` são renderizados; os demais são copiados como estão).
- `pyproject.toml` (raiz) — config do `python-semantic-release` para versionar o *template em si* (tag-only, não copiado para projetos gerados).
- `.github/workflows/release.yml` (raiz) — gera as tags/CHANGELOG do template a cada push na `main`.
