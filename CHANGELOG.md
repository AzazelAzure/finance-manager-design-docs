# Changelog — finance-manager-design-docs

Notable changes to the Obsidian vault: current state, roadmap pointers, system design, and release runbooks.

## [Unreleased]

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

- **Canonical sources:** `00_Dashboard.md` and `10_Current_State/00_Alpha_Overview.md` now anchor on `plans/cursor/strategic-roadmap-reframe-53be/` and `governance/` (S1.B, web flagship, Reflex archived).
- **Roadmap vault:** `20_Roadmap/Roadmap_Overview.md`, `Planned_Features.md`, `Phase_Execution_Triggers_and_Breakpoints.md`, `API_Feature_Roadmap.md`, `Reflex_Feature_Roadmap.md` updated; legacy **`orchestration_manager/`** and **`Calendar_Phase1_Contract_and_UI.md`** moved to **`20_Roadmap/_historical/`** with index `README.md`.
- **Runtime/scaling:** `40_System_Design/07_Server_Runtime_and_Scaling.md` §2 aligned to **PH-first** + passive US (P-6).
- **Conflict log:** `10_Current_State/Strategic_doc_conflicts_pending_direction.md` converted to resolved audit table.
- **Releases / multi-repo:** `30_Releases/Git_and_Workflow_Strategy.md`, `Subagent_Multi_Repo_Routine.md` — pipeline uses `finance_manager_web`; links to governance and strategic plan.
- **System design:** `12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md` and `13_Server_Runtime_Agent_Operations_Contract.md` — plan roots under `plans/cursor/<phase-stage>/<sub-plan>/`.
- **Coding guidelines:** `00_Coding_Guidelines.md` — plan layout, archiving, and knowledge hierarchy reference strategic + governance paths.

### Added

- **`20_Roadmap/_historical/README.md`** — index for archived roadmap/orchestration artifacts.
