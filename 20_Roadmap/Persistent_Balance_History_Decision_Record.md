# Persistent Balance History Decision Record

## Objective
Select a persistence strategy for historical balances and define API exposure plus validation gates.

## Decision
Choose **daily snapshot persistence** as the primary strategy for Phase 1 history tracking.

## Rationale
- Lower query complexity for time-series reads in Reflex.
- Deterministic reconstruction checks are simpler than event-sourced replay for this stage.
- Supports straightforward retention and pagination limits.

## Data Model Draft
- `balance_history_daily`
  - `uid`
  - `as_of_date`
  - `base_currency`
  - `total_balance_base`
  - optional source-level breakdown JSON

## API Exposure Draft
- `GET /finance/appprofile/balance-history/`
  - Query: `start_date`, `end_date`, `limit`, `offset`
  - Response:
    - `points` array with date and base-normalized totals
    - pagination metadata

## Validation Gates
- Gate A: integrity
  - Compare persisted daily totals against reconstructed ledger totals for sampled periods.
- Gate B: performance
  - Validate range query latency under realistic history volumes.
- Gate C: operational
  - Verify updater writes one row per day boundary and remains idempotent on retries.

## Risks and Mitigations
- Storage growth -> retention policy and archival cadence.
- Drift from ledger corrections -> scheduled reconciliation job with drift alert threshold.

## DoD
- Storage strategy selected and justified.
- API draft and validation gates documented.
- Known risks listed with concrete mitigation path.
