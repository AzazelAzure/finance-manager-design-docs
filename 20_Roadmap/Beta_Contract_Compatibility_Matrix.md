# Beta Contract Compatibility Matrix (P0-07)

## Purpose
Define the Beta-critical API contract expectations shared across `finance_manager_api`, `finance_manager_cli`, and **`finance_manager_web`** (flagship SPA). `finance_manager_reflex` is **archived** and is not a verification target.

## Scope
- Horizon: tight hosted **beta** (canonical **S1.B**); see `plans/cursor/strategic-roadmap-reframe-53be/` for phase language.
- Focus: auth + core finance CRUD + dashboard/snapshot read flows.
- Excludes: `[Post-Launch Beta]` and `[Far Future]` items.

## Matrix

| Contract Surface | API Endpoint | API Contract Rule | CLI Verification Path | Web verification path (`finance_manager_web`) | Status |
|---|---|---|---|---|---|
| JWT obtain | `POST /api/token/` | Returns access/refresh pair for valid credentials | `fm auth login -u <user>` | Login / signup flows via `src/api/auth.ts` and routed pages | Active |
| JWT refresh | `POST /api/token/refresh/` | Refresh token rotation + blacklist policy enforced | `fm auth login` then token refresh flow | SPA handles 401 → refresh (see `src/api/refreshClient.ts`) | Active |
| JWT verify | `POST /api/token/verify/` | Must be wired to verify view, not refresh serializer | `fm ping` + token lifecycle checks | Session/bootstrap after auth | Active |
| Current user | `GET /finance/user/` | Returns authenticated user payload (dict response) | `fm user me` | Post-login user/bootstrap state | Active |
| Profile snapshot | `GET /finance/appprofile/snapshot/` | Returns dashboard snapshot envelope for authenticated user | `fm profile snapshot` | `DashboardPage.tsx` + dashboard components consuming snapshot | Active |
| Transactions list/create | `GET/POST /finance/transactions/` | Supports filter query + payload validation contract | `fm transactions list/create` | Transactions UI + dashboard refresh after mutations | Active |
| Transactions calendar | `GET /finance/transactions/calendar/` | Returns base-currency normalized daily/weekly/monthly aggregates plus `heat_*` metadata and `due_events`; supports `display_currency_mode` and `heat_metric_mode` params | _No CLI command yet_ (API call only) | `CalendarPage.tsx` / related hooks; heatmap + due overlays | Active |
| Transaction patch | `PATCH /finance/transactions/<tx_id>/` | `date` required; `tx_id`/`entry_id` immutable | `fm transactions patch <tx_id> --date ...` | Transaction edit flows in web UI | Active |
| Sources delete guard | `DELETE /finance/sources/<source>/` | Reserved `unknown` source cannot be deleted | `fm sources delete unknown` expects reject | Source settings / mutations in web UI | Active |
| Tags mapping | `PATCH/PUT/DELETE /finance/tags/` | Mapping shape `{"tags": {"old": "new"}}` / null delete | `fm tags patch/put/delete --map ...` | Tag editors / mappers in web UI | Active |
| Bug report pipeline | `POST /finance/bug-report/` | Authenticated user can submit report email to admin destination | _No CLI command yet_ (use direct API call for now) | _No dedicated web surface yet_ (API contract reserved) | Partial |

## Validation Gates

### Gate A (contract compile-time)
- API schema path exists: `/api/schema/`.
- CLI + **web** docs (and this matrix) stay cross-linked from repo READMEs where applicable.

### Gate B (runtime contract sanity)
Before running Gate B locally, confirm the selected runtime is healthy with
`./scripts/fm_services.sh status` for local services or
`./scripts/fm_docker.sh status` for the container path. Use
`design_docs/10_Current_State/01_Runtime_Validation_Checklist.md` as the
local-machine integration smoke checklist.

- CLI smoke:
  - `fm ping`
  - `fm user me`
  - `fm profile snapshot`
  - `fm transactions list`
- Web smoke (HTTPS **:8443** container path preferred; see runtime checklist):
  - login
  - dashboard load
  - transaction create/update/delete → dashboard refresh

### Gate C (regression sanity after restart)
- Re-run Gate B after script-driven runtime restart.
- No new contract regressions in auth/profile/transaction/snapshot core paths.

## Ownership / Handoff
- API owner lane: `finance_manager_api/`
- CLI owner lane: `finance_manager_cli/`
- Web owner lane: `finance_manager_web/`
- Orchestration and gate status tracked in `design_docs/20_Roadmap/Beta_Execution_Board.md` (superseded for new IDs by `plans/cursor/s1b/`).
