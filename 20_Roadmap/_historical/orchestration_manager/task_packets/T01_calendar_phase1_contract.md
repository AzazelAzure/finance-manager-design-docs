# T01 - Calendar Phase1 Contract

## Plan Task ID and Current Objective
- Task ID: `calendar-phase1-contract`
- Objective: Define/freeze API calendar contract with base-currency aggregate normalization, heatmap fields, due-expense date overlays, and row display currency mode metadata.

## Scope Boundary
- Repo/path: `finance_manager_api/`
- Primary files:
  - `finance/api_tools/serializers/tx_serializers.py`
  - `finance/services/transaction_services.py`
  - `finance/views/tx_views.py`
  - `finance/tests/transaction_tests/test_transaction_calendar.py`

## Definition of Done
- Calendar API payload includes data required for:
  - daily/week/month aggregate totals
  - heatmap metric payload
  - due-expense event overlays
  - day drill rows
- Aggregate values are base-currency normalized by default.
- Contract supports row display mode semantics (`base` and `original`) without changing aggregate calculations.
- Tests updated to assert currency invariants and due-event behavior.

## Required Verification Command/Checklist
- Run:
  - `uv run pytest finance/tests/transaction_tests/test_transaction_calendar.py`
- Verify:
  - aggregates stay base-currency normalized
  - row display mode metadata exists
  - event overlays are date-scoped

## Known Constraints
- Keep view layer for request parsing/response shaping; business logic in services.
- Preserve backward-compatible contract changes where possible (additive fields preferred).
- Do not edit plan file.

## Branch/PR Expectations
- Work on current API feature branch.
- If PR is opened later: required checks must pass before merge.
