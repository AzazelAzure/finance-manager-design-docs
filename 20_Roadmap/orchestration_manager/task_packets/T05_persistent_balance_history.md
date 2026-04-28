# T05 - Persistent Balance History

## Plan Task ID and Current Objective
- Task ID: `persistent-balance-history`
- Objective: Finalize persistence and API strategy for historical balance tracking.

## Scope Boundary
- Repo/path: `design_docs/20_Roadmap/`
- Suggested artifact:
  - `Persistent_Balance_History_Decision_Record.md`

## Definition of Done
- Decision record includes:
  - chosen storage approach (snapshot vs event/delta derived)
  - backfill strategy and retention policy
  - API range endpoints and payload shape
  - reconciliation/integrity checks and operational monitoring gates
- Currency default is explicit: aggregate history values standardized to user base currency.

## Required Verification Command/Checklist
- Checklist review:
  - strategy decision and rationale are explicit
  - API exposure and pagination constraints documented
  - integrity/performance gate criteria documented

## Known Constraints
- Prefer additive schema and forward-compatible rollout path.
- Include drift detection and mitigation path.
- Do not edit plan file.

## Branch/PR Expectations
- Docs-only changes on current branch; PR optional depending on workflow.
