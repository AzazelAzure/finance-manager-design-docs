# Validation Gates

Use these checks as the minimum deterministic gate set before marking a task complete.

## T01 - Calendar API Contract
- API tests:
  - `uv run pytest finance/tests/transaction_tests/test_transaction_calendar.py`
- Gate assertions:
  - Base-currency normalized aggregates in calendar payload.
  - Due-event overlays included by date.
  - Currency display mode fields present for row-level rendering semantics.

## T02 - Calendar Reflex UI
- Compile checks:
  - `uv run python -m py_compile finance_manager_reflex/finance_manager_reflex/features/transactions/api.py`
  - `uv run python -m py_compile finance_manager_reflex/finance_manager_reflex/features/transactions/state.py`
  - `uv run python -m py_compile finance_manager_reflex/finance_manager_reflex/features/transactions/view_components.py`
  - `uv run python -m py_compile finance_manager_reflex/finance_manager_reflex/app/routes.py`
- Gate assertions:
  - Full calendar grid and summaries render.
  - Heatmap uses base-currency aggregate values.
  - Transaction row display toggle does not affect aggregate totals.
  - Date click loads transactions and due-expense events for that day.

## T03 - Budgeting Discovery
- Documentation gate:
  - Artifact exists in `design_docs/20_Roadmap/`.
  - Contains bounded context, model options, contract options, migration sketch, and unresolved decisions.

## T04 - Volatile Bills + Partial Payments
- Documentation gate:
  - Rulebook exists in `design_docs/20_Roadmap/`.
  - Includes deterministic algorithm rules, edge-case matrix, and test criteria.

## T05 - Persistent Balance History
- Documentation gate:
  - Decision record exists in `design_docs/20_Roadmap/`.
  - Includes storage strategy, API range exposure, backfill/retention, reconciliation gates.

## Cross-Task Gate
- Currency invariants:
  - Aggregate endpoints default to user base currency.
  - Row display mode (`base` vs `original`) cannot change aggregate/heatmap calculations.
