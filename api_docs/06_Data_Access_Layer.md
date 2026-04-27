# Data Access Layer

The Data Access layer (`data/` and `management/managers.py`) is responsible for all interactions with the database. It encapsulates complex logic and ensures that database operations are performant and consistent.

## Custom QuerySets & Managers

All core models use custom `QuerySet` classes to promote reusability and keep the views clean.

### The `for_user(uid)` Pattern

Every manager implements a `for_user(uid)` method. This is the primary way to ensure data isolation between users.

- **Rule**: Never query a model directly (e.g., `Transaction.objects.all()`). Always scope it to the user: `Transaction.objects.for_user(uid)...`.

### Specialized Managers

- **TransactionManager**:
  - `get_current_month()`: Filters transactions for the current calendar month.
  - `get_by_tag_name()`: Handles complex JSON-based tag matching.
  - `get_latest()`: Returns the single most recent transaction (useful for dashboard snapshots).
- **UpcomingExpenseManager**:
  - `get_current_month()`: Retrieves bills due within the current month window.
  - `get_by_remaining()`: Filters for unpaid bills.
- **FinancialSnapshotManager**:
  - `get_totals(acc_type)`: Dynamically retrieves totals by account type name.

---

## Database Optimization Rules

To maintain high performance, the following rules are enforced in the Data Access layer:

### 1. Database Hit Limit

- **Max Hits**: 12 per request.
- **Target**: 10 per request.
- **Implementation**: Use `select_related` for ForeignKeys (if any) and `prefetch_related` for ManyToMany relationships. However, since the API uses UUID decoupling, many "relationships" are handled via optimized bulk queries in the `Services` or `Logic` layers.

### 2. Bulk Operations

- Always use `.bulk_create()` and `.bulk_update()` when handling multiple records in a single request (e.g., batch transaction imports).
- This significantly reduces the number of round-trips to the database.

### 3. Aggregations at the Database Level

- Use Django's `Sum`, `Avg`, and `Count` annotations to perform calculations in the database rather than in Python memory.
- Example: `Transaction.objects.for_user(uid).aggregate(Sum('amount'))`.

---

## Data Resilience: Exchange Rates

The API relies on an external dataset for currency conversion, stored in `finance/data/exchange_rates.zip`.

- This file is treated as a **Production Asset**.
- If the file is missing, conversion will fail gracefully, and the system will require an update via the `update_conversion_file` management command.

---

**[Return to Overview](file:///home/pproctor/Documents/python/finance_manager/design_docs/api_docs/00_API_Overview.md)**