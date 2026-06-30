# Changelog — finance-manager-design-docs

Notable changes to the Obsidian vault: current state, roadmap pointers, system design, and release runbooks.

## [Unreleased]

### Changed
- **`api_docs/` (full rewrite):** Updated all nine API vault pages to reflect tight-beta implementation — decoupled `uid` model, `/finance/` routing, bill cadence engine, F-001 balance history, F-005 savings goals, F-010 export (share tokens removed), PWA idempotency middleware, support intake, Celery beat tasks, and contributor onboarding path. Replaces stale Reflex-centric and pre-recurrence content.

### Added
- **`README.md`:** Vault index for active system design scope (post-2026-06-30 cleanup).

### Changed
- **`00_Dashboard.md`:** Reworked navigation — removed dead wikilinks to archived/removed sections (`10_Current_State`, `20_Roadmap`, `30_Releases`, reflex, research); index only tracks `00_*`, `api_docs/`, `cli_docs/`, `rust_docs/`, `40_System_Design/`.
- **`api_docs/*.md`:** Replaced Obsidian `file:///home/pproctor/...` absolute links with repo-relative paths (ecosystem-relative for API source code).

### Changed (2026-06-30 — Web #97 green promotion)

- **`30_Releases/Runtime_Signup_Sheet.md`:** Log inactive-green restage on `main` (API `9938614`, Web `9436e3b` / PR #97 Profile fix + ErrorBoundary), manual smoke after `sprint_verify.sh --smoke` skip, and active color flip **blue → green** (captured 2026-06-30T07:24+08).

### Changed (2026-06-29 — design-docs restructure, D6)

- **Archive moves (T01–T02):** `reflex_docs/` → `_historical/reflex_docs/`; `10_Current_State/00_Alpha_Overview.md` → `_historical/10_Current_State/`; active-sounding `20_Roadmap/*.md` → `20_Roadmap/_historical/`; release-ops overlap (`Git_and_Workflow_Strategy`, `pr_status`, etc.) → `30_Releases/_historical/`. **Retained:** `Runtime_Signup_Sheet.md`, handoff templates.
- **`00_Dashboard.md`:** Removed dead links (`30_Security/`, `Versioning_and_Contracts`, `Dependency_Management`); repointed archived paths; clarified boundary (design_docs = how-it-works; strategy/governance = roadmap/plan ops).
- **`10_Current_State/02_Documentation_Sync_Protocol.md`:** Updated boundary table and doc-map for post-restructure layout.
- **`50_Research_Backlog.md`:** Pointer note to `strategy/research/`.

### Changed (2026-05-22 — `#sprint-queue` spec location)

- **`40_System_Design/12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md`:** `#sprint-queue` **`sprint-queue-v1`** contract points to parent-repo [`governance/sprint_queue_message_spec_v1.md`](../governance/sprint_queue_message_spec_v1.md) for the normative spec; F-007 `SLACK_SPRINT_QUEUE.md` for worked examples; **`Task Id:`** spelling aligned with automation.

### Added (2026-05-05 — inter-agent relay contract)

- **`40_System_Design/14_Inter_Agent_Message_Relay_and_Ownership_Contract.md`:** Versioned **relay envelope** (`schema_version`, `correlation_id`, `owning_entity`, `next_owner`, Slack + GitHub anchors, `allowed_actions` / `forbidden`), **single routing truth**, **idempotent handoffs**, **no silent relay**, and **golden-path** test matrix for **cursor-executor**, **antigravity-executor**, **antigravity-reviewer**, and **Cursor PA** across Slack/GitHub/models.
- **`40_System_Design/12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md`:** Related-doc link to §14.

### Changed (2026-05-04 — #cli-interface table vs Cursor PA)

- **`40_System_Design/12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md`:** `#cli-interface` channel row no longer implies `CURSOR_SLACK_PREFIX` for the default stack; documents **@Cursor PA** + threaded Socket replies and calls out legacy finance_manager **poller** only if run separately.

### Changed (2026-05-04 — #cli-interface hygiene + single pin)

- **`40_System_Design/12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md`:** Operator section for **one** `#cli-interface` pin, no absolute paths in history, cleanup/archive steps; pin template shortened and consolidated.

### Changed (2026-05-04 — Slack paths are tilde-only)

- **`40_System_Design/12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md`:** `WORKSPACE_PATH` / pins / examples use **`~/Documents/python/finance_manager`** (and `.../<sub_repo>`) in Slack — no `/home/...`; local export example uses `$HOME/...`.

### Changed (2026-05-04 — Cursor PA intake + pins)

- **`40_System_Design/12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md`:** Pin text clarifies channel = `@PA` only; DM prefix option; recommend `CURSOR_PA_BLOCK_CHANNEL_IDS` for `#pull-requests`; runner envs documented in Cursor PA agreement §5.

### Changed (2026-05-04 — Cursor PA pins + local agreement pointer)

- **`40_System_Design/12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md`:** Separate **Slack pinned message** copy-paste blocks for `#cli-interface`, `#pull-requests`, and optional `#beta-incidents`; pointer to workstation file **`~/CursorAgent/headless-cursor-agent/docs/CURSOR_PA_AGREEMENT.md`** (rigid PA scope on local hardware).

### Changed (2026-05-04 — Cursor PA as canonical Slack intermediary)

- **`40_System_Design/12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md`:** Preferred headless path is **`~/CursorAgent/headless-cursor-agent/`** (Slack display name **Cursor PA**; Socket Mode + companion `slack.env`); finance_manager `cursor_headless_slack_agent.py` documented as supplementary poller.

### Changed (2026-05-04 — Slack-first beta incident intake)

- **`40_System_Design/15_Beta_Week_Incident_Triage_and_Human_Gated_Autofix_Contract.md`:** Intake pipeline assumes **Slack/webhook** operator notification; **VPS transactional email not required** for beta.
- **`40_System_Design/12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md`:** Recommended **`#beta-incidents`** channel row; hygiene note for retiring stale channels and separating incident traffic from `#cli-interface` / `#pull-requests`.

### Changed (2026-05-04 — single parent plan template)

- **Coding / PR matrix:** `00_Coding_Guidelines.md` and `30_Releases/Slack_PR_Protocol_Compliance_Matrix.md` now reference **`governance/plan_template.md`** only; legacy `plans/templates/GEMINI_PLAN_TEMPLATE*.md` paths removed (those files archived in the parent repo under `plans/archived/gemini_plan_templates/`).

### Changed (2026-05-04 — strategic roadmap path in parent)

- **Cross-links:** Canonical multi-year roadmap in the parent repo is now **`strategy/strategic-roadmap-reframe-53be/`** (replacing `plans/cursor/strategic-roadmap-reframe-53be/`). Vault pointers updated where they referenced the old path.

### Changed (2026-05-04 — active S1.B plans path in parent)

- **Cross-links:** Parent active Stage **S1.B** implementation tree is now **`plans/S1/S1.B/`** (replacing `plans/cursor/s1b/`). Vault pointers updated where they referenced the old path.

### Changed (2026-05-04 — governance path at ecosystem root)

- **Cross-links:** Vault references to `plans/_governance/` now use **`governance/`** (workspace root sibling to `plans/` in the parent `finance_manager` repo). Tactical plans use `plans/<Phase>/<Stage>/` in the parent (see parent `CHANGELOG.md`).

### Added (2026-05-01 — Web PWA plans bridge + Android cross-ref)

- **`40_System_Design/12_Web_PWA_Install_Offline_Sync.md`:** Bridge from vault to ecosystem parent `plans/S1/S1.B/pwa-install-offline-sync-research/` (D0–D4, seeding, sprint activation index on `plans/S1/S1.B/README.md`) and strategic `validation_gates.md` / `S1_public_beta_position.md`; aligns web PWA with Android offline-first vocabulary.
- **`40_System_Design/08_Android_Offline_First_Sync_Architecture.md`:** "Related — Flagship Web PWA" pointer to the new bridge doc.
- **`01_Business_Vision.md`**, **`20_Roadmap/Beta_Launch_Cutline_and_SPA_Transition.md`:** Touch-up edits aligned with strategic §2.0 / flagship web wording (batched on same branch as the bridge doc).

### Changed (2026-05-01 — PH monetization tiers vs strategic §2.0 lock)

- **`01_Business_Vision.md`:** Tier table shows **₱249/mo Pro** list lock, **Founding Lifetime** capped at **100 seats** planning lock; cross-ref `01_unit_economics_and_costs.md` §2.0.

### Changed (2026-05-01 — beta cutline + runtime sheet alignment)

- **`20_Roadmap/Beta_Launch_Cutline_and_SPA_Transition.md`:** Rewritten for **`finance_manager_web`** flagship, archived Reflex, **S1.B** / `s1b` drift gate, and canonical `plans/` pointers (replaces Reflex-era checklist wording).
- **`30_Releases/Runtime_Signup_Sheet.md`:** Example owner scope line uses **S1.B** vocabulary.
- **`10_Current_State/Strategic_doc_conflicts_pending_direction.md`:** Audit row for this pass.

### Changed (2026-05-01 — design_docs vs strategic plan integration pass)

- **Execution reality:** `20_Roadmap/Beta_Execution_Board.md` now states **S1.B** as canonical, **web** flagship, VPS **:8443** proxy, drift-cleanup / **S0** distribution gate, and adds **Cycle 7** huddle log; legacy `BETA-P0/*` rows updated (Reflex → Web) but kept as audit IDs.
- **Roadmap vault:** `20_Roadmap/Roadmap_Overview.md` — legacy Phase 1/2 + Tracks A–E long-form **replaced** with a mapping table to **S1–S6**; glossary-first Phase/Stage wording.
- **Dashboard + runtime:** `00_Dashboard.md`, `10_Current_State/01_Runtime_Validation_Checklist.md`, `30_Releases/Runtime_Signup_Sheet.md` aligned to **API + web** containers and archived Reflex.
- **Architecture / gates:** `40_System_Design/08_Android_Offline_First_Sync_Architecture.md` (program gate keys off **web**), `40_System_Design/05_Deployment_Strategy.md` (inactive-color feature rule), `api_docs/02_Data_Flow_and_Architecture.md`, `20_Roadmap/License_and_Release_Gates.md`, `30_Releases/pr_status.md`.
- **Contract matrix:** `20_Roadmap/Beta_Contract_Compatibility_Matrix.md` — third lane is **`finance_manager_web`** (Reflex column removed); smoke gates say **web** + **:8443**.
- **Audit trail:** `10_Current_State/Strategic_doc_conflicts_pending_direction.md` — appended row for this pass.

### Changed (2026-05-01 — strategic alignment CPPRD)

- **Canonical sources:** `00_Dashboard.md` and `10_Current_State/00_Alpha_Overview.md` now anchor on `strategy/strategic-roadmap-reframe-53be/` and `governance/` (S1.B, web flagship, Reflex archived).
- **Roadmap vault:** `20_Roadmap/Roadmap_Overview.md`, `Planned_Features.md`, `Phase_Execution_Triggers_and_Breakpoints.md`, `API_Feature_Roadmap.md`, `Reflex_Feature_Roadmap.md` updated; legacy **`orchestration_manager/`** and **`Calendar_Phase1_Contract_and_UI.md`** moved to **`20_Roadmap/_historical/`** with index `README.md`.
- **Runtime/scaling:** `40_System_Design/07_Server_Runtime_and_Scaling.md` §2 aligned to **PH-first** + passive US (P-6).
- **Conflict log:** `10_Current_State/Strategic_doc_conflicts_pending_direction.md` converted to resolved audit table.
- **Releases / multi-repo:** `30_Releases/Git_and_Workflow_Strategy.md`, `Subagent_Multi_Repo_Routine.md` — pipeline uses `finance_manager_web`; links to governance and strategic plan.
- **System design:** `12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md` and `13_Server_Runtime_Agent_Operations_Contract.md` — plan roots under `plans/<Phase>/<Stage>/<sub-plan>/`.
- **Coding guidelines:** `00_Coding_Guidelines.md` — plan layout, archiving, and knowledge hierarchy reference strategic + governance paths.

### Added

- **`20_Roadmap/_historical/README.md`** — index for archived roadmap/orchestration artifacts.
