# T04 - Volatile Bills and Partial Payments

## Plan Task ID and Current Objective
- Task ID: `volatile-bill-and-partial-payments`
- Objective: Define deterministic domain rules for variable recurring bill forecasting and partial payment reconciliation.

## Scope Boundary
- Repo/path: `design_docs/20_Roadmap/`
- Suggested artifact:
  - `Volatile_Bills_and_Partial_Payment_Rulebook.md`

## Definition of Done
- Rulebook includes:
  - rolling average policy (user-configurable 3-6 month windows)
  - sparse history fallback behavior
  - partial payment handling rules (underpay/overpay/carryover/missed cycle)
  - deterministic test matrix for each edge case
- Forecast outputs documented as base-currency standardized for aggregates.

## Required Verification Command/Checklist
- Checklist review:
  - edge-case matrix present
  - deterministic formulas/steps documented
  - test criteria map directly to expected API behaviors

## Known Constraints
- Keep rules implementation-ready but docs-focused unless explicitly implementing.
- Avoid ambiguous language around estimate confidence.
- Do not edit plan file.

## Branch/PR Expectations
- Docs-only changes on current branch; PR optional depending on workflow.
