# API + Reflex Rollout Execution Manifest

## Plan Reference
- Source plan: `/home/pproctor/.cursor/plans/api_reflex_feature_rollout_1f0a0342.plan.md`
- Plan file is reference-only for execution. Do not modify it.

## Execution Graph
1. `T01_calendar_phase1_contract` (API contract + tests)
2. `T02_calendar_phase1_ui` (Reflex calendar UI + nav + client integration)
3. `T03_budgeting_discovery_track` (discovery artifacts only)
4. `T04_volatile_bills_partial_payments` (rulebook + deterministic test criteria)
5. `T05_persistent_balance_history` (decision record + API exposure/gates)

## Dependency Rules
- `T01` must pass before `T02`.
- `T03`, `T04`, and `T05` can proceed after `T01`; run sequentially unless orchestration explicitly parallelizes docs-only work.
- If contract mismatches appear between API and Reflex, route to bugfix flow before continuing.

## Scope Boundaries
- API implementation scope: `finance_manager_api/`
- Reflex implementation scope: `finance_manager_reflex/`
- Design/documentation scope: `design_docs/20_Roadmap/`

## Completion Contract
Each task is complete only when all are true:
- Definition of done met from the corresponding task packet.
- Validation evidence captured using `validation_gates.md`.
- No unresolved blocker for downstream dependent tasks.
- Status explicitly transitioned `pending -> in_progress -> completed`.

## Task Status Ledger
- `T01_calendar_phase1_contract`: pending
- `T02_calendar_phase1_ui`: pending
- `T03_budgeting_discovery_track`: pending
- `T04_volatile_bills_partial_payments`: pending
- `T05_persistent_balance_history`: pending
