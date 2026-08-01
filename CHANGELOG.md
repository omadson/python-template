# CHANGELOG


## v0.1.1 (2026-08-01)

### Bug Fixes

- Stop asking for LICENSE year, derive it at generation time
  ([`fc42a51`](https://github.com/omadson/python-template/commit/fc42a5149c4ad1e481f817cf96ea9f05b0dd1673))

Copyright year no longer needs a prompt or a stale hardcoded default; a post-gen task fills __YEAR__
  in LICENSE with the current year.


## v0.1.0 (2026-08-01)

### Bug Fixes

- Avoid forced bump to 1.0.0 in the template (allow_zero_version)
  ([`f19de00`](https://github.com/omadson/python-template/commit/f19de00d0fbfbb1b2743ce0910fab692b77f8c82))

### Documentation

- Explain key files in the generated-project structure
  ([`08db6ff`](https://github.com/omadson/python-template/commit/08db6ff74ba9d378ea4d5988114bd73f379d2c09))

- Rename generated-project section to Structure with a repo tree
  ([`4de5c77`](https://github.com/omadson/python-template/commit/4de5c77aa6331e9ead5e1356b104a485b0a57493))

- Translate template to English
  ([`5929351`](https://github.com/omadson/python-template/commit/5929351737a02412781a8f051225f8f6dfdd8ce8))

### Features

- Add project_type question to drive package/cli/script/data-science structures
  ([`3ff8cbc`](https://github.com/omadson/python-template/commit/3ff8cbc0a00646c7ff3a0525436e5462c1303bba))

- Initial Copier template for Python projects
  ([`7ed8277`](https://github.com/omadson/python-template/commit/7ed827726dd9035f625e949b2d128d63c06769e6))

uv + pre-commit (ruff/mypy/interrogate) + python-semantic-release + CI/CD, with license prompts,
  post-generation tasks, and answer validators in copier.yml.
