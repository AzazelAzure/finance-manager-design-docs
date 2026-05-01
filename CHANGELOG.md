# Changelog — finance-manager-design-docs

Notable changes to the Obsidian vault: current state, roadmap pointers, system design, and release runbooks.

## [Unreleased]

### Changed (2026-05-01 — strategic alignment CPPRD)

- **Canonical sources:** `00_Dashboard.md` and `10_Current_State/00_Alpha_Overview.md` now anchor on `plans/cursor/strategic-roadmap-reframe-53be/` and `plans/_governance/` (S1.B, web flagship, Reflex archived).
- **Roadmap vault:** `20_Roadmap/Roadmap_Overview.md`, `Planned_Features.md`, `Phase_Execution_Triggers_and_Breakpoints.md`, `API_Feature_Roadmap.md`, `Reflex_Feature_Roadmap.md` updated; legacy **`orchestration_manager/`** and **`Calendar_Phase1_Contract_and_UI.md`** moved to **`20_Roadmap/_historical/`** with index `README.md`.
- **Runtime/scaling:** `40_System_Design/07_Server_Runtime_and_Scaling.md` §2 aligned to **PH-first** + passive US (P-6).
- **Conflict log:** `10_Current_State/Strategic_doc_conflicts_pending_direction.md` converted to resolved audit table.
- **Releases / multi-repo:** `30_Releases/Git_and_Workflow_Strategy.md`, `Subagent_Multi_Repo_Routine.md` — pipeline uses `finance_manager_web`; links to governance and strategic plan.
- **System design:** `12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md` and `13_Server_Runtime_Agent_Operations_Contract.md` — plan roots under `plans/cursor/<phase-stage>/<sub-plan>/`.
- **Coding guidelines:** `00_Coding_Guidelines.md` — plan layout, archiving, and knowledge hierarchy reference strategic + governance paths.

### Added

- **`20_Roadmap/_historical/README.md`** — index for archived roadmap/orchestration artifacts.
