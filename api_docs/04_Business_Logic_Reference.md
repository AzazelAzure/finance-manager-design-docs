# Business Logic Reference

This document details the core financial logic and state mutation rules implemented in the Finance Manager API. The logic is primarily located in `finance_manager_api/finance/logic/`.

## Core Logic Components

### [Calculator](file:///home/pproctor/Documents/python/finance_manager/finance_manager_api/finance/logic/fincalc.py)

The `Calculator` class is responsible for pure, read-only financial computations. It handles currency conversion automatically to ensure all totals are presented in the user's `base_currency`.

#### Key Computations:

- `**calc_sts` (Safe-to-Spend)**: Calculates assets available in "spend accounts" minus unpaid bills due in the current month.
- `**calc_total_assets`**: Sums all account balances (excluding `UNKNOWN` type) across all currencies, converted to the base currency.
- `**calc_leaks**`: Identifies transfer imbalances (drift) by summing `XFER_IN` and `XFER_OUT` transactions. Ideally, this should be zero.
- `**calc_acc_types**`: Groups and sums balances by account type (Savings, Checking, etc.).
- `**calc_queryset**`: A utility to sum any Transaction queryset into the base currency.

---

### [Updater](file:///home/pproctor/Documents/python/finance_manager/finance_manager_api/finance/logic/updaters.py)

The `Updater` class handles stateful mutations and side effects that occur after CRUD operations. It ensures that data remains consistent across different models.

#### Key Handlers:

- `**transaction_handler**`: 
  1. Adjusts `PaymentSource` balances based on transaction amounts.
  2. Links transactions to `UpcomingExpense` (bills).
  3. Triggers a `FinancialSnapshot` rebuild.
- `**expense_handler**`: Updates transaction references when a bill is renamed or deleted and recalculates safe-to-spend metrics.
- `**source_handler**`: Recalculates all snapshot totals when an account is added, modified, or removed.
- `**fix_tx_data` / `fix_source_data**`: Normalization "fixers" that ensure data consistency (e.g., uppercasing currency codes, setting default dates, generating `tx_id`).

---

## Important Business Rules

### 1. Currency Normalization

All incoming financial data is normalized to uppercase 3-letter codes (e.g., `usd` -> `USD`). Calculations always output in the `base_currency` defined in the `AppProfile`.

### 2. The "Fix it Forever" Fixer Pattern

Before any data is saved, it passes through an `Updater` fixer method. This ensures that even if a client sends incomplete data (e.g., missing `tx_id` or `date`), the API populates it correctly based on server-side rules.

### 3. Transaction Sign Logic

- `EXPENSE` and `XFER_OUT` amounts are stored as **negative** values in the database.
- `INCOME` and `XFER_IN` amounts are stored as **positive** values.
This allows for simple `Sum()` aggregations without complex conditional logic in queries.

### 4. Recurring Bill Rolling

When a transaction is linked to a recurring `UpcomingExpense`, the `Updater` automatically:

1. Marks the current instance as "paid" (internally).
2. Rolls the `due_date` forward by one month.
3. Resets the `paid_flag` for the next period.

---

**[Return to Overview](file:///home/pproctor/Documents/python/finance_manager/design_docs/api_docs/00_API_Overview.md)**