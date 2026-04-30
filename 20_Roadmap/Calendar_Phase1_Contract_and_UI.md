# Calendar Phase 1 Contract + UI Packet

## Objective

Freeze the API and Reflex contract for Phase 1 calendar delivery with explicit currency semantics, heatmap rules, and due-expense overlays.

## Contract Freeze (API)

- Endpoint: `GET /finance/transactions/calendar/`
- Required query params: `start_date`, `end_date`
- Optional query params:
  - `display_currency_mode`: `base | original` (transaction row display preference)
  - `heat_metric_mode`: `net | expense_only | count`
- Response invariants:
  - `base_currency` is always returned and governs aggregate values.
  - `daily`, `weekly`, `monthly` aggregate amounts are base-currency normalized.
  - `daily` rows include `heat_value` and `heat_intensity` (0-100).
  - `due_events` are date-scoped and include both original amount and `amount_base`.
  - `heat_max` and `heat_metric_mode` are returned to keep client bucket logic deterministic.

## Reflex Calendar UI Structure

- Route: `/transactions/calendar`
- Primary controls:
  - Date range picker (start/end)
  - `display_currency_mode` selector
  - `heat_metric_mode` selector
  - Current-month preset and reload action
- Page sections:
  - Contract context strip (`base_currency`, `heat_max`)
  - Daily trend chart
  - Day cards with heat context and quick day selection
  - Due events table
  - Day drill-through table
- Navigation promotion:
  - Calendar route promoted to protected nav (`Calendar`) for direct discovery.

## Deterministic Validation

- API gate:
  - `uv run pytest finance/tests/transaction_tests/test_transaction_calendar.py`
- Reflex gate:
  - `uv run python -m py_compile finance_manager_reflex/app/routes.py finance_manager_reflex/features/transactions/api.py finance_manager_reflex/features/transactions/state.py finance_manager_reflex/features/transactions/view_components.py finance_manager_reflex/finance_manager_reflex.py`
- Manual smoke:
  - Load current month.
  - Toggle heat mode and verify daily heat values update.
  - Confirm due-events section renders when upcoming expenses exist.