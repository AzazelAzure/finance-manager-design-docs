# CLI Configuration and Runtime Modes

Related overview: `design_docs/cli_docs/00_CLI_Overview.md`

## Environment Variables

- `API_BASE_URL`
  - API origin only (for example `http://127.0.0.1:8000`).
- `API_TIMEOUT_SECONDS`
  - HTTP timeout.
- `API_REFRESH_ON_401`
  - enables refresh-and-retry flow.
- `FINANCE_API_ROOT`
  - Django root used by `fm api serve`.
- `FINANCE_API_PYTHON`
  - interpreter used for `fm api serve`.

## Config Loading

- `.env` in current working directory is loaded when available.
- Defaults are applied by `config.py` if env vars are absent.

## Runtime Modes

- Unauthenticated mode
  - `fm ping`, `fm auth ...`
- Authenticated mode
  - all protected `/finance/...` command groups
- JSON automation mode
  - `fm --json ...`
- Local API runner mode
  - `fm api serve` (foreground process)

## Operational Notes

- Keep API and CLI contract versions aligned.
- For full local-machine integration smoke tests, start the shared API + Reflex stack from the workspace root with `./scripts/fm_services.sh start`, then run CLI checks such as `fm ping`, `fm user me`, and `fm profile snapshot` against the same API origin.
- Prefer `--json` for scripts and CI automation.
- Prefer table/message mode for interactive usage.
