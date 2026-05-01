# T02 - Calendar Phase1 UI

## Plan Task ID and Current Objective
- Task ID: `calendar-phase1-ui`
- Objective (historical — Reflex era): Deliver dedicated calendar experience with full grid, weekly summaries, monthly footer totals, heatmap visualization, due-expense events, and date drill-through.

## Scope Boundary
- Repo/path: `finance_manager_reflex/finance_manager_reflex/`
- Primary files:
  - `features/transactions/api.py`
  - `features/transactions/state.py`
  - `features/transactions/view_components.py`
  - `app/routes.py`
  - `finance_manager_reflex.py`
  - `core/i18n.py` (labels as needed)

## Definition of Done
- Calendar page is full-featured (not stub):
  - month-style grid with date cells
  - week-level summary totals
  - month-level total summary
  - heatmap intensity view from API fields
  - due-event list and date drill-through
- Calendar route is discoverable in nav.
- UI supports row display currency mode while preserving base-currency aggregate display for summaries/heatmap.

## Required Verification Command/Checklist
- Run:
  - `uv run python -m py_compile finance_manager_reflex/finance_manager_reflex/features/transactions/api.py`
  - `uv run python -m py_compile finance_manager_reflex/finance_manager_reflex/features/transactions/state.py`
  - `uv run python -m py_compile finance_manager_reflex/finance_manager_reflex/features/transactions/view_components.py`
  - `uv run python -m py_compile finance_manager_reflex/finance_manager_reflex/app/routes.py`
- Verify:
  - day selection fetches matching transactions + due events
  - heatmap stays consistent when row currency mode toggles
  - weekly/monthly summaries render correctly

## Known Constraints
- Keep heavy calculations in API, not client state logic.
- Keep interaction flows additive and avoid breaking existing transactions pages.
- Do not edit plan file.

## Branch/PR Expectations
- Work on current feature branch (historical: Reflex-era branch naming).
- If PR is opened later: required checks must pass before merge.
