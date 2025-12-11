# Repository Guidelines

## Project Structure & Module Organization
- Top level scripts (`driver.py`, `prepare_fuzzbench.py`, `start_tgi_servers*.sh`) orchestrate experiment runs and data moves.
- `cli/` holds the Click-based `elfuzz` CLI; run locally via `python -m cli.main ...`.
- `evaluation/` contains experiment configs, fuzzers, and adapters; `evaluation/glade/sqlite3_parser/` is the Rust workspace member.
- `analysis/` and `plot/` store notebooks, spreadsheets, and plotting helpers; `extradata/`, `preset/`, and `tmp/` are large artifacts/seeds—keep them out of commits.
- Docker entrypoints and setup live in `.devcontainer/` and `docker_readme.md`; follow `README.md` for pulling/publishing images.

## Build, Test, and Development Commands
- Build Docker image for a reproducible environment: `docker build -t ghcr.io/osuseclab/elfuzz:25.08.0 -f .devcontainer/Dockerfile --target publish .`.
- Run CLI help locally: `python -m cli.main --help`; common subcommands include `synth`, `produce`, `minimize`, and `run`.
- Quick smoke of synthesis with minimal resources (inside the container): `elfuzz synth -T fuzzer.elfuzz jsoncpp --use-small-model --evolution-iterations 1`.
- Rust crate checks (only in `evaluation/glade/sqlite3_parser/`): `cargo fmt` and `cargo test`.

## Coding Style & Naming Conventions
- Python: 4-space indentation, snake_case functions, prefer type hints where practical; keep Click command names short and nouns/verbs (`synth`, `produce`, `run`).
- Shell: bash with `set -euo pipefail`; keep scripts idempotent and log key paths.
- Rust: run `cargo fmt`; keep modules small and prefer explicit `use` lists.
- Paths in docs/scripts should be relative to repo root or container paths noted in `docker_readme.md`.

## Tooling & Checks
- Formatting/linting config lives in `pyproject.toml` and `.editorconfig`.
- Python env: create with `UV_CACHE_DIR=.uv-cache uv venv .venv && source .venv/bin/activate`; install dev tools from extras with `uv pip install -e '.[dev]'`.
- Python checks: `uv run ruff check .` and `uv run ruff format .` (ruff uses line length 100; artifacts excluded). `uv run black .` is available if preferred.
- Rust crate (`evaluation/glade/sqlite3_parser/`): `cargo fmt` and `cargo test`.
- Shell: run `shellcheck` when editing `.sh` files; keep scripts executable after edits.

## Testing Guidelines
- No global CI; run targeted checks relevant to your change.
- For CLI changes, run the smallest viable command (e.g., `elfuzz synth -T grammar.glade jsoncpp --use-small-model`) to ensure arguments parse and side effects land in the expected `evaluation/` or `extradata/` subtrees.
- For Rust edits, ensure `cargo test` passes in `evaluation/glade/sqlite3_parser/`; add unit tests alongside modules when adding parsing logic.

## Commit & Pull Request Guidelines
- Use short, imperative commit messages (current history favors `Add ...`, `Fix ...`, `Update ...`).
- Scope commits narrowly; avoid committing large artifacts from `extradata/`, `preset/`, or `tmp/`.
- In PRs, include: purpose, commands run (build/test/smoke), data or paths touched, and any environment assumptions (Docker vs host).
- Link related issues or experiments when available; attach screenshots only if UI output changes (plots, tables).***
