# Beta Contract Compatibility Matrix (P0-07)

## Purpose
Define the Beta-critical API contract expectations shared across `finance_manager_api`, `finance_manager_cli`, and `finance_manager_reflex`.

## Scope
- Horizon: `[Now]` Phase 2 hosted beta readiness.
- Focus: auth + core finance CRUD + dashboard/snapshot read flows.
- Excludes: `[Post-Launch Beta]` and `[Far Future]` items.

## Matrix

| Contract Surface | API Endpoint | API Contract Rule | CLI Verification Path | Reflex Verification Path | Status |
|---|---|---|---|---|---|
| JWT obtain | `POST /api/token/` | Returns access/refresh pair for valid credentials | `fm auth login -u <user>` | Auth login flow in `features/auth` | Active |
| JWT refresh | `POST /api/token/refresh/` | Refresh token rotation + blacklist policy enforced | `fm auth login` then token refresh flow | 401 retry/refresh handling | Active |
| JWT verify | `POST /api/token/verify/` | Must be wired to verify view, not refresh serializer | `fm ping` + token lifecycle checks | Auth/session validity checks | Active |
| Current user | `GET /finance/user/` | Returns authenticated user payload (dict response) | `fm user me` | Session/bootstrap user state | Active |
| Profile snapshot | `GET /finance/appprofile/snapshot/` | Returns dashboard snapshot envelope for authenticated user | `fm profile snapshot` | Dashboard load chain in `agentdash/state_load.py` | Active |
| Transactions list/create | `GET/POST /finance/transactions/` | Supports filter query + payload validation contract | `fm transactions list/create` | Transactions state + dashboard refresh trigger | Active |
| Transaction patch | `PATCH /finance/transactions/<tx_id>/` | `date` required; `tx_id`/`entry_id` immutable | `fm transactions patch <tx_id> --date ...` | Transaction edit flow | Active |
| Sources delete guard | `DELETE /finance/sources/<source>/` | Reserved `unknown` source cannot be deleted | `fm sources delete unknown` expects reject | Source mutation paths | Active |
| Tags mapping | `PATCH/PUT/DELETE /finance/tags/` | Mapping shape `{"tags": {"old": "new"}}` / null delete | `fm tags patch/put/delete --map ...` | Tag mutation payload builders | Active |
| Bug report pipeline | `POST /finance/bug-report/` | Authenticated user can submit report email to admin destination | _No CLI command yet_ (use direct API call for now) | _No Reflex screen yet_ (API contract reserved) | Partial |

## Validation Gates

### Gate A (contract compile-time)
- API schema path exists: `/api/schema/`.
- CLI/Reflex docs link back to this matrix.

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
- Reflex smoke:
  - login
  - dashboard load
  - transaction create/update/delete -> dashboard refresh

### Gate C (regression sanity after restart)
- Re-run Gate B after script-driven runtime restart.
- No new contract regressions in auth/profile/transaction/snapshot core paths.

## Ownership / Handoff
- API owner lane: `finance_manager_api/`
- CLI owner lane: `finance_manager_cli/`
- Reflex owner lane: `finance_manager_reflex/`
- Orchestration and gate status tracked in `design_docs/20_Roadmap/Beta_Execution_Board.md`.
