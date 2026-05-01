# T03 - Budgeting/Savings Discovery Track

## Plan Task ID and Current Objective
- Task ID: `budgeting-discovery-track`
- Objective: Produce discovery-only artifacts for smart budgeting/savings models/contracts and integration boundaries.

## Scope Boundary
- Repo/path: `design_docs/20_Roadmap/`
- Suggested artifact:
  - `Budgeting_Savings_Discovery_Packet.md`

## Definition of Done
- Discovery packet includes:
  - bounded context and entities (`Budget`, `SavingsGoal`, planned expense links)
  - API contract options (budget-vs-actual, savings trajectory)
  - migration sketch and rollout sequencing
  - unresolved decisions and risk notes
- Explicitly states base-currency normalization default for aggregate outputs.
- No production code changes required for this task.

## Required Verification Command/Checklist
- Checklist review:
  - packet exists and is complete
  - contract options include currency invariants
  - unresolved decisions are explicit and actionable

## Known Constraints
- Keep as discovery only (no implementation creep).
- Keep budget contracts decoupled from calendar UI specifics.
- Do not edit plan file.

## Branch/PR Expectations
- Docs-only changes on current branch; PR optional depending on workflow.
