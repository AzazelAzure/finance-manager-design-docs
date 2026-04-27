# CLI Command Architecture

Related overview: `design_docs/cli_docs/00_CLI_Overview.md`

## Entry Point

- Package script: `fm`
- Bound in `finance_manager_cli/pyproject.toml` to `finance_manager_cli.cli:main`

## Composition

- `finance_manager_cli/finance_manager_cli/cli.py`
  - top-level command group
  - auth/user/profile/ping/api commands
- `finance_manager_cli/finance_manager_cli/cli_resources.py`
  - resources: transactions, sources, categories, tags, expenses, transfer helper

## Command Tree (high-level)

- `fm --json ...`
- `fm ping`
- `fm auth login|signup`
- `fm user me|set-password|delete-account`
- `fm profile [snapshot|update]`
- `fm transactions list|get|create|patch|delete`
- `fm xfer`
- `fm sources list|get|create|patch|put|delete`
- `fm categories list|get|create|patch|put|delete`
- `fm tags list|create|patch|put|delete`
- `fm expenses list|get|create|put|patch|delete`
- `fm api serve`

## Payload Strategy

Many mutating commands support:
- direct flags only,
- JSON file input (`-f/--file`),
- file + flag overlay (flags override selected values).

## API Alignment Principle

CLI command groups mirror `/finance/...` resources closely so behavioral drift can be tracked route-by-route.
