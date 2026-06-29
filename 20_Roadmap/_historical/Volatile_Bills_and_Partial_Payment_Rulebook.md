# Volatile Bills and Partial Payments Rulebook

## Objective
Define forecasting and partial-payment domain rules with deterministic validation criteria.

## Domain Rules
- Rolling forecast window:
  - User-configurable: 3-6 months.
  - Minimum-history fallback: if fewer than 2 historical points, use last known amount with low confidence.
- Forecast output:
  - `forecast_amount_base`
  - `confidence_band` (`low`, `medium`, `high`)
  - `window_size_used`
- Partial payment handling:
  - Payments are applied against oldest open bill cycle first.
  - Overpayment creates credit carry-forward to next cycle.
  - Underpayment leaves residual balance and flags cycle as `partial`.

## Deterministic Reconciliation Criteria
- Exact expected residual after each payment event.
- Stable next-cycle estimate despite payment split order.
- Identical forecast output for the same ordered ledger input.

## Test Matrix
- Case 1: underpayment then catch-up payment.
- Case 2: overpayment with carry-forward.
- Case 3: skipped month with sparse history fallback.
- Case 4: currency-mixed historical rows normalized to base before forecast math.

## API Contract Sketch
- `GET /finance/upcoming-expenses/forecast/`
  - Query: `window_size`, `start_date`, `end_date`
  - Response includes per-expense projected amount, confidence metadata, and partial-payment state.

## DoD
- Rulebook complete with deterministic math checks.
- Validation matrix documented for future API implementation tests.
