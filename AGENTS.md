# Engineering instructions

## Repository purpose and current state

This repository was created from a Python starter template. At the time these instructions were added, the package is still named `bonacci` and contains a placeholder Fibonacci library and CLI. Do not silently rename the package or replace this sample unless the task explicitly requests it.

## Toolchain

- Python version: `3.14.8`, pinned in `.python-version`.
- Dependency manager and lockfile: `uv` and `uv.lock`.
- Packaging/build backend: Hatchling, invoked through `uv build`.
- Test runner and coverage: Pytest and pytest-cov. Coverage must remain at or above the 100% threshold configured in `pyproject.toml`.
- Lint and formatting: Ruff, dprint, and Lefthook.

## Common commands

Run these from the repository root:

```bash
uv sync
uv run pytest -v --cov
uv build
lefthook run pre-commit --all-files
```

The pre-commit hook may modify files in place with Ruff and dprint. Review every resulting diff and rerun the relevant checks. Do not stage or commit automatic formatting changes without reviewing them.

## Coding conventions

- Follow the existing `src/` package layout and put tests under `tests/` as `test_*.py`.
- Keep changes focused and consistent with nearby code.
- Add or update tests for behavioral changes and edge cases. Prefer test-first development for code changes when practical.
- Use type annotations and clear docstrings in line with the existing project style and Ruff configuration.
- Keep dependencies minimal. Update `pyproject.toml` and `uv.lock` together when dependencies change.
- Do not add secrets, access tokens, private keys, personal data, or local environment files to the repository.

## Engineering workflow

- Before a new feature or architectural change, clarify the requirements and obtain approval of the design before implementation.
- Use Superpowers skills when available: brainstorming for new behavior, writing-plans for multi-step architectural work, systematic-debugging for failures, test-driven-development for code changes, and verification-before-completion before claiming success.
- Keep work on a feature or fix branch and open a pull request to `main`. Do not merge a pull request or change branch protections without explicit authorization.
- In each pull request, describe the intent, summarize the change, link any related issue and Notion specification, list the exact verification commands run, and disclose checks that could not be run.
- Never claim tests, lint, or build passed unless the command actually ran and its result was checked.
- Do not weaken coverage requirements or bypass CI to make a change appear green.

## CI

The current workflow is `.github/workflows/ci.yaml`. Preserve its checks unless a task specifically calls for a justified change. CI currently checks the Lefthook pre-commit jobs, runs `uv run pytest -v --cov`, and builds the package with `uv build`.

## Completion checklist

- [ ] Requirements and acceptance criteria are clear.
- [ ] Tests cover changed behavior and relevant edge cases.
- [ ] The full test command has been run or is clearly marked pending.
- [ ] Formatting/linting and build checks have been run or are clearly marked pending.
- [ ] The diff contains only intended changes and no secrets.
- [ ] Documentation is updated when behavior or workflow changes.
- [ ] The pull request accurately reports checks, risks, and limitations.
