# CLI Auth, HTTP, and Output Pipeline

Related overview: `design_docs/cli_docs/00_CLI_Overview.md`

## Token Lifecycle

- Login/signup obtain JWT via API token endpoints.
- Tokens persist in local config storage via `auth_store.py`.
- Authenticated commands call `authorized_request()` from `auth_http.py`.

## HTTP Request Flow

1. Command builds params/body.
2. `authorized_request()` loads access/refresh tokens.
3. `ApiClient.request()` executes HTTP call.
4. On eligible `401`, refresh token path executes and request retries once.
5. Parsed response is returned to command handler.
6. Rotated access token is saved if changed.

## Error Handling

- API errors are normalized as `ApiError`.
- `die_api()` prints status + details to stderr and exits non-zero.

## Output Modes

### JSON mode
- Enabled via global `--json`.
- Prints raw JSON envelopes for scripting/automation.

### Human mode (default)
- `output.py` renders tables/messages using Rich.
- Known response envelopes are rendered with domain-aware sections/columns.
- Unknown dict responses fallback to `Field | Value` tables.

## Output Contract Guidance

- Use `--json` for machine parsing.
- Use default mode for operator-facing UX.
- Error paths may include structured JSON details even in human mode.
