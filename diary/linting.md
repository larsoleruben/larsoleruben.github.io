---
layout: page
title: Lionting
permalink: /diary/linting
---
## Ruff One-off commands
### Lint (no changes)
ruff check .

### Lint and auto-fix what it can (imports, simple refactors, etc.)
ruff check . --fix

### Format code (Black-like)
ruff format .

## #CI-style: fail if formatting is needed
ruff format --check .

### Typical “clean it up” combo
ruff check . --fix && ruff format .

## Project config (pyproject.toml)
```toml
[tool.ruff]
# Project-wide settings
line-length = 100
target-version = "py311"
exclude = ["venv", ".venv", "migrations", "build", "dist"]

[tool.ruff.lint]
# Rule sets: E/F (pycodestyle/pyflakes), I (imports), UP (pyupgrade), B (bugbear), etc.
select = ["E", "F", "I", "UP", "B"]
fixable = ["ALL"]
```
## Pre-commit (optional)
```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: vX.Y.Z   # pin to the latest tag
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format
```
!!! And remember 
```bash
pre-commit install
```

## Notes
Choose one formatter. If you already use Black, either keep Black or switch to ruff format—don’t run both.
Ruff can also sort imports (rule set I) and many fixes happen via --fix.
Scope it as needed: ruff check path/to/pkg or ruff format app/**/*.py.