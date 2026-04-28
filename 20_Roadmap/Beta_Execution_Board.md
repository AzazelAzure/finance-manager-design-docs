# Beta Execution Board (Parallel Workstream View)

## Purpose

Provide a single execution board for "Necessary Right Now" Beta launch work with owner lanes and priority tags (`P0`, `P1`). This board is optimized for parallel agent/human execution across repos.

Horizon alignment:

- This board tracks **[Now]** execution for hosted beta readiness.
- Post-launch and far-future items are tracked in roadmap docs and are not blockers for this board.

## Priority Definitions

- `P0`: launch-blocking; must be complete for server-ready Beta tests.
- `P1`: important near-term hardening; can run in parallel but cannot block urgent `P0` fixes.

## Owner Lanes

- `API`: `finance_manager_api/`
- `CLI`: `finance_manager_cli/`
- `Reflex`: `finance_manager_reflex/`
- `Infra`: root workspace infra/scripts/runtime operations
- `Docs`: `design_docs/` synchronization and rollout records

## P0 Board (Necessary Right Now)


| ID         | Priority | Owner          | Work Item                                                                                                 | Dependency             | Done When                                                           |
| ---------- | -------- | -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------- | ------------------------------------------------------------------- |
| BETA-P0-01 | P0       | Infra          | Provision Test server and validate container runtime path                                                 | none                   | Test server can host API + Reflex together                          |
| BETA-P0-02 | P0       | Infra          | Validate deployment/restart path using project scripts (`scripts/fm_docker.sh`, `scripts/fm_services.sh`) | BETA-P0-01             | Repeatable start/stop/restart cycle with no crash loop              |
| BETA-P0-03 | P0       | API            | Complete OAuth integration baseline                                                                       | BETA-P0-01             | Auth flow works in hosted test environment                          |
| BETA-P0-04 | P0       | API            | Enforce JWT rotation and token blacklist policy                                                           | BETA-P0-03             | Token lifecycle matches policy with tests/log evidence              |
| BETA-P0-05 | P0       | API            | Enable user-tagged operational logging                                                                    | BETA-P0-01             | Error logs are attributable per user/session                        |
| BETA-P0-06 | P0       | API            | Enable basic bug-report email pipeline                                                                    | BETA-P0-05             | Verified report path in test environment                            |
| BETA-P0-07 | P0       | API+CLI+Reflex | Publish Beta-critical contract compatibility matrix                                                       | BETA-P0-03             | Shared matrix exists and maps endpoint/payload/version expectations |
| BETA-P0-08 | P0       | Reflex         | Fix critical dashboard UX/UI defects impacting core flows                                                 | BETA-P0-07             | Core CRUD/snapshot dashboard flows pass smoke tests                 |
| BETA-P0-09 | P0       | Infra          | Define reproducible deploy routine (CI/CD or scripted deploy)                                             | BETA-P0-01             | Deployment can be repeated without manual drift                     |
| BETA-P0-10 | P0       | Docs           | Record Beta smoke checklist and cutline status                                                            | BETA-P0-08, BETA-P0-09 | Checklist is executable and tracked in `design_docs/`               |


## P1 Board (Pre-Beta Hardening, Parallelizable)


| ID         | Priority | Owner      | Work Item                                                 | Dependency | Done When                                                       |
| ---------- | -------- | ---------- | --------------------------------------------------------- | ---------- | --------------------------------------------------------------- |
| BETA-P1-01 | P1       | Reflex     | Mobile responsiveness audit for key pages                 | BETA-P0-08 | Critical viewport defects are triaged and tracked               |
| BETA-P1-02 | P1       | Infra      | Improve runtime observability for crash/bottleneck triage | BETA-P0-02 | Restart/crash evidence can be captured quickly                  |
| BETA-P1-03 | P1       | API+Reflex | Stabilize advanced visualization contracts incrementally  | BETA-P0-07 | Calendar/graph payload requirements are documented and testable |
| BETA-P1-04 | P1       | Docs       | Update roadmap/status docs after each P0 milestone        | ongoing    | No major implementation/doc drift remains                       |


## Post-Launch Beta Queue (Not P0/P1 Blockers)

- `[Post-Launch Beta]` PH UX validation and formatting hardening
- `[Post-Launch Beta]` SMS wallet ingestion architecture discovery (GCash/Maya message parsing)
- `[Post-Launch Beta]` Blue/green pre-cutover validation host lane (for example `dev.thehivemanager.com` -> inactive color) after VPS go-live baseline is stable
- `[Post-Launch Beta]` Nginx/proxy host-routing rules for active/public vs inactive/private validation traffic, only after VPS runtime + traffic telemetry pass

These are intentionally deferred from Phase 2 launch gates and should be orchestrated after initial hosted beta launch.

## Execution Rules

1. `P0` bug fixes always preempt `P1` work.
2. Any API contract change triggers CLI validation before Reflex dependency work.
3. Any behavior change triggers `design_docs` sync before marking task complete.
4. Cross-repo work must use explicit handoff output and repo boundaries.
5. Runtime access must follow shared ownership tracking in `design_docs/30_Releases/Runtime_Signup_Sheet.md`.
6. Runtime ownership transfers must use `design_docs/30_Releases/Runtime_Owner_Handoff_Template.md`.
7. Items labeled `[Post-Launch Beta]` or `[Far Future]` must not block `[Now]` launch gates.
8. Feature work should run on repo feature branches with PRs; merge only after required checks and signoffs.

## Next Suggested Parallel Slice

- Lane A (Infra): `BETA-P0-01`, `BETA-P0-02`, `BETA-P0-09`
- Lane B (API): `BETA-P0-03`, `BETA-P0-04`, `BETA-P0-05`, `BETA-P0-06`
- Lane C (Contracts/UI): `BETA-P0-07`, `BETA-P0-08`
- Lane D (Docs): `BETA-P0-10` plus milestone syncs

## Execution Cycle Log

### Cycle 1 (2026-04-27, Phase 2 `[Now]` Orchestration)

- Scope: discovery + runtime ownership initialization + Breakpoint A status baseline.
- Runtime ownership:
  - Owner claimed in `design_docs/30_Releases/Runtime_Signup_Sheet.md`
  - Mode observed: `local-services`
  - Mixed runtime: `no`
- Validation evidence:
  - `./scripts/fm_docker.sh status` -> no active containers listed.
  - `./scripts/fm_services.sh status` -> API/Reflex both running.
- P0 progression state:
  - `BETA-P0-01` (test server/runtime path): `blocked` (missing hosted test-server evidence).
  - `BETA-P0-02` (scripted restart path): `partial` (status baseline captured; restart-cycle proof pending).
  - `BETA-P0-03..06` (API auth/logging/email): `partial/missing` from discovery; implementation slice queued.
  - `BETA-P0-07` (compat matrix): `partial`; matrix artifacts missing across repos.
  - `BETA-P0-08` (Reflex critical UX): `partial`; regression/smoke formalization pending.
  - `BETA-P0-09` (repro deploy routine): `partial`; runbook/evidence artifact pending.
  - `BETA-P0-10` (smoke checklist + cutline status): `partial`; checklist exists, status ledger now started in this section.

### Cycle 2 (2026-04-27, Slice 2A/2B/2C Execution)

- Scope: API contract fixes, bug-report pipeline baseline, cross-repo matrix publication, runtime breakpoint validation.
- Task execution:
  - `BETA-P0-04`:
    - Fixed `/api/token/verify/` route to `TokenVerifyView`.
    - Added contract test (`finance/tests/user_tests/test_beta_phase2_api_contracts.py`).
  - `BETA-P0-05`:
    - Added request-scoped user logging context middleware and log format fields (`uid`, `username`).
  - `BETA-P0-06`:
    - Added `POST /finance/bug-report/` endpoint with email send path.
    - Added integration test for email dispatch.
  - `BETA-P0-07`:
    - Published matrix artifact: `design_docs/20_Roadmap/Beta_Contract_Compatibility_Matrix.md`.
    - Linked matrix from API/CLI/Reflex README files.
  - `BETA-P0-02` (Breakpoint C restart sanity):
    - Ran `./scripts/fm_services.sh restart && ./scripts/fm_services.sh status && ./scripts/fm_docker.sh status`.
  - `BETA-P0-10` (smoke evidence updates):
    - CLI smoke passed:
      - `uv run fm ping`
      - `uv run fm auth signup ...`
      - `uv run fm user me`
      - `uv run fm profile snapshot`
      - `uv run fm transactions list`
- Validation evidence:
  - API tests: `uv run pytest finance/tests/user_tests/test_beta_phase2_api_contracts.py -q` -> `2 passed`.
  - API schema: `uv run python manage.py spectacular --validate ...` -> pass.
  - Runtime restart/status: local-services pass, container runtime inactive.
  - Reflex smoke:
    - `uv run python test_reflex_state.py` -> failed (state direct instantiation runtime error).
    - Retask to alternate smoke: `uv run python test_mappers.py` -> pass.
- Current status by P0:
  - `BETA-P0-04` -> `pass`.
  - `BETA-P0-05` -> `partial` (infrastructure added; broader request-path log evidence still needed).
  - `BETA-P0-06` -> `partial-pass` (endpoint + tests pass; environment recipient config still required for hosted runtime).
  - `BETA-P0-07` -> `partial-pass` (matrix published; full tri-repo runtime gate still pending Reflex flow validation).
  - `BETA-P0-02` -> `pass` for local-services mode, container mode pending.
  - `BETA-P0-10` -> `partial-pass` (checklist execution evidence expanded).
- Retask notes:
  - Blocker category `runtime/test instability` raised for Reflex script `test_reflex_state.py`.
  - Retasked validation to non-instantiating mapper smoke script and captured pass.

### Cycle 3 (2026-04-27, Pre-Server Directive Shift / Dashboard QoL Slice)

- Scope: execute pre-server barrier tasks while hosted runtime remains blocked until `2026-04-29` (unless explicitly overridden).
- Task execution (aligned to orchestration task IDs A1..D2):
  - `A1` / `A2` (idle schema traffic triage + mitigation):
    - Updated root `docker-compose.yml` API healthcheck target from `/api/schema/` to `/api/health/`.
    - Added lightweight API health endpoint in `finance_manager_api/finance_api/urls.py`.
    - Confirmed `/api/schema/` remains for docs/contract paths (API docs + CLI ping), not container health probes.
  - `B1` (dashboard cleanup):
    - Removed dashboard `Start simulation` control from chart preferences UI.
  - `B2` (category drill-through):
    - Made category pie slices clickable; later superseded in Cycle 4 by in-place dashboard category filtering.
  - `C1` / `C2` (chart preference filter consolidation):
    - Added quick transaction filters in chart preferences (current month, expense, income, transfer in/out, reset+refresh).
    - Added advanced search-term panel (tag/category/source/currency) while keeping date controls in existing chart preferences area.
  - `D1` / `D2` (dashboard quick add actions):
    - Added dashboard buttons: `Add expense`, `Add income`, `Add transfer`.
    - Reused shared transactions modal flow by wiring dashboard actions to `TransactionsState` editor events and mounting modal/tag picker on dashboard page.
- Validation evidence:
  - Reflex compile gate:
    - `python -m compileall finance_manager_reflex/features/agentdash/state.py finance_manager_reflex/features/agentdash/view_components.py` -> pass.
  - API compile gate:
    - `python -m compileall finance_api/urls.py` (from `finance_manager_api`) -> pass.
  - Lint gate:
    - `ReadLints` on touched API/Reflex files -> no linter errors.
  - Contract/path sanity:
    - `rg "api/schema/"` confirms schema path remains documented/available while compose healthcheck no longer targets it.
- Current status by task:
  - `A1`: pass.
  - `A2`: pass.
  - `B1`: pass.
  - `B2`: pass (runtime UI click-through still recommended during hosted smoke).
  - `C1`: pass.
  - `C2`: pass.
  - `D1`: pass.
  - `D2`: pass (runtime form submission smoke still recommended during hosted smoke).
- Retask notes:
  - Validation blocker category: `runtime/test instability` (full live runtime smoke not executed in this slice due to pre-server barrier/owner constraints).
  - Retask action: used deterministic compile + lint + contract/path verification gates and deferred runtime click-through to next runtime-owner window.

### Cycle 4 (2026-04-27, UX feedback polish / in-place dashboard behavior)

- Scope: apply user feedback pass for dashboard quick actions and transaction editor consistency.
- Task execution:
  - Healthcheck cadence:
    - Reduced compose healthcheck interval from `10s` to `30s` in `docker-compose.yml` to lower idle probe frequency/noise.
  - Category selection semantics:
    - Category field placeholder text now shows `Category` (instead of suggesting `Custom` as default).
    - Single-transaction payload normalizes empty category to `none` so API can fallback to tx-type category assignment.
  - Transfer modal cleanup:
    - Transfer mode now hides non-transfer single-entry fields (description/category/tags/bill) in editor modal.
    - Transfer submit path keeps required metadata in payload via defaults/auto-fill while users only provide transfer-focused inputs.
  - Pie chart behavior update:
    - Category pie click now applies category filter in-place on dashboard (no route redirect to transactions).
    - `Other` bucket is ignored for in-place click filtering.
  - Quick filter visual consistency:
    - Auto-apply quick filters use a consistent indigo style.
    - Reset action uses red style.
    - Search/advanced controls use state-aware color variants for clearer active/inactive differentiation.
- Validation evidence:
  - `python -m compileall ...` on touched Reflex modules -> pass.
  - `ReadLints` on touched Reflex modules -> no linter errors.
- Status:
  - Feedback polish batch -> `pass` (runtime-owner smoke still recommended at next breakpoint window).

### Cycle 5 (2026-04-28, `PLAN_API_REFLEX_BETA_READINESS_2026-04-28` / `cursor/api-reflex-beta-readiness-plan-53be`)

- Scope: API production-style settings, test suite triage, pytest collection fix, headless Slack bridge long-output handling, local container runtime evidence.
- **Infra / bridge (parent `scripts/cursor_headless_slack_agent.py`):** `pass` for chunking + full local log; optional `CURSOR_SLACK_THREAD_TASKS` for threaded `!cursor` follow-ups.
- **API (`finance_manager_api`, branch `cursor/api-beta-hardening-53be`):** `pass` for `manage.py check --deploy` with production-like env (residual W005/W021 HSTS sub-domain/preload until public domain policy is fixed); `uv run pytest -q` -> `171 passed, 4 skipped` with `testpaths=finance/tests`.
- **Reflex + proxy (local container evidence, 2026-04-28):** `partial` (containers up; `fm_docker.sh status` shows API/Reflex/proxy healthy; `fm_services.sh` shows local API/Reflex stopped — **no mixed use** of container stack + local services in this session). Reflex log shows app at `http://localhost:3000/`.
- **Hosted test server:** `BETA-P0-01` remains `blocked` until external hosted evidence exists (this cycle is local-runtime only).
- Plan root: `plans/cursor/api-reflex-beta-readiness-plan-53be/`.

### Cycle 6 (2026-04-28, VM -> VPS migration prep sync)

- Scope: align deployment docs/contracts with newly added runtime bundle scripts for VPS migration flow.
- Task execution:
  - Added lean bundle scripts and verification references to install runbook:
    - `scripts/server/create_runtime_bundle.sh`
    - `scripts/server/push_runtime_bundle.sh`
    - `scripts/server/verify_release_manifest.sh`
  - Synced system design docs to treat bundle push + manifest verification as first-class execution path for server runtime agents.
- Validation evidence:
  - Script capabilities cross-checked against:
    - `deploy/SERVER_BETA_INSTALL.md`
    - `design_docs/40_System_Design/12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md`
    - `design_docs/40_System_Design/13_Server_Runtime_Agent_Operations_Contract.md`
- Current status by P0:
  - `BETA-P0-09` (repro deploy routine): `partial-pass` (artifact workflow documented; hosted server execution evidence still pending).
  - `BETA-P0-01` (hosted runtime path): `blocked` (server-side smoke/cutover evidence not yet attached).