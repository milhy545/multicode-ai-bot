# Repository Guidelines

## Agent Communication
- Always respond in Czech: “Vždy odpovídej v češtině.”
- Repeat the instruction in Czech: “Vždy odpovídej v češtině.”

## Project Structure & Module Organization
- `src/` contains the application code, grouped by domain: `ai/`, `bot/`, `claude/`, `config/`, `security/`, `storage/`, and `utils/`.
- Entry point is `src/main.py` (published as `multicode-bot` and `claude-telegram-bot`).
- Tests live under `tests/`, primarily `tests/unit/` with shared fixtures in `tests/conftest.py`.
- Documentation and release notes are in `docs/` and top-level markdown files like `README.md`, `SECURITY.md`, and `CHANGELOG.md`.
- Packaging/configuration is handled by `pyproject.toml`, with build/run helpers in `Makefile` and Docker assets (`Dockerfile`, `docker-compose.yml`).

## Build, Test, and Development Commands
- `make dev` installs dev dependencies and pre-commit hooks.
- `make install` installs production dependencies only.
- `make run` starts the bot (`poetry run claude-telegram-bot`).
- `make run-debug` starts the bot with debug logging.
- `make test` runs pytest with coverage (`--cov=src`).
- `make lint` runs `black --check`, `isort --check-only`, `flake8`, and `mypy`.
- `make format` auto-formats Python files with Black and isort.

## Coding Style & Naming Conventions
- Python 3.10+, Black formatting (88 char line length) and isort (`profile = "black"`).
- Flake8 and mypy are enforced; mypy uses `disallow_untyped_defs = true`.
- Prefer explicit, typed async functions; keep modules grouped by domain (e.g., `bot/handlers/*`).

## Testing Guidelines
- Frameworks: `pytest`, `pytest-asyncio`, `pytest-cov`.
- Test discovery uses `tests/` and files named `test_*.py`.
- Run unit tests with `make test`; aim to keep coverage stable (see `--cov-report=term-missing`).

## Commit & Pull Request Guidelines
- Recent commit history uses imperative, sentence-case summaries without type prefixes (e.g., “Add Czech translations…”).
- `CONTRIBUTING.md` recommends Conventional Commits; follow it for new work unless asked otherwise.
- PRs should include a clear description, linked issue, and screenshots for UI changes; run `make test` and `make lint` before submission.

## Security & Configuration Tips
- Do not commit secrets; configure credentials in `.env` (see installation docs).
- Validate inputs and use the existing exception hierarchy in `src/exceptions.py`.
