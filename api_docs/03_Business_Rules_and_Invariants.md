# API Business Rules and Invariants

Related overview: `design_docs/api_docs/00_API_Overview.md`

## Transactions

- `tx_id`, `entry_id`, and identity fields are immutable through client update paths.
- Transaction `PATCH` requires `date` in the request body at view level.
- Amount sign normalization:
  - `EXPENSE` and `XFER_OUT` are normalized negative.
  - `INCOME` and `XFER_IN` are normalized positive.
- Source must exist and currency must validate.
- Category defaults may be assigned by type when missing.
- Future-dated explicit transaction dates are disallowed by validation logic.

## Sources

- Reserved source `unknown` is protected.
- `unknown` cannot be created/renamed/deleted through normal source mutations.
- When a source is deleted, linked transactions are reassigned to `unknown`.
- Source names are unique per user scope.

## Profile

- `spend_accounts` cannot include `unknown`.
- `spend_accounts` entries must reference existing sources.
- Changes that affect budgeting/snapshot context trigger recalculation.

## Categories and Tags

- Category names are unique per user scope.
- System/default categories (`expense`, `income`, `transfer`) are protected from destructive changes.
- Category deletion remaps existing transactions to default categories.
- Tag update/delete semantics use mapping payloads.

## Upcoming Expenses

- Expense names are unique per user scope.
- Date relationships are validated (`end_date` cannot precede required boundaries).
- Recurring expenses influence future due-date progression when paid.

## Why these matter

These invariants protect financial fidelity and reduce data drift between raw transaction history and derived snapshot metrics.