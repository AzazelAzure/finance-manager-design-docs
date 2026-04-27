# Models Reference

This document provides a detailed reference for all data models in the Finance Manager API. All models are defined in `finance_manager_api/finance/models.py`.

## Core Models

### [AppProfile](file:///home/pproctor/Documents/python/finance_manager/finance_manager_api/finance/models.py#L46)

The central user configuration model. Automatically created via signals when a new Django user is registered.


| Field            | Type          | Default              | Description                                               |
| ---------------- | ------------- | -------------------- | --------------------------------------------------------- |
| `username`       | OneToOneField | -                    | Link to the Django Auth User model.                       |
| `user_id`        | UUIDField     | `uuid4`              | Primary key and unique identifier used across services.   |
| `spend_accounts` | JSONField     | `[]`                 | List of source names considered "spending" accounts.      |
| `base_currency`  | CharField     | `"USD"`              | The default currency for calculations.                    |
| `timezone`       | CharField     | `"America/New_York"` | User's local timezone for date-based logic.               |
| `start_of_week`  | IntegerField  | `1`                  | Day of the week (0-6) representing the start of the week. |


---

### [Transaction](file:///home/pproctor/Documents/python/finance_manager/finance_manager_api/finance/models.py#L188)

Records of individual financial movements.


| Field         | Type         | Default | Description                                                  |
| ------------- | ------------ | ------- | ------------------------------------------------------------ |
| `date`        | DateField    | -       | The date the transaction occurred.                           |
| `description` | CharField    | -       | Human-readable description.                                  |
| `amount`      | DecimalField | `0`     | Financial value (positive for income, negative for expense). |
| `created_on`  | DateField    | -       | System timestamp of record creation.                         |
| `category`    | CharField    | -       | Decoupled link to a Category name.                           |
| `source`      | CharField    | -       | Decoupled link to a PaymentSource name.                      |
| `currency`    | CharField    | -       | 3-letter currency code (e.g., "USD").                        |
| `tags`        | JSONField    | `[]`    | List of associated tag names.                                |
| `tx_id`       | CharField    | -       | Unique business identifier (e.g., TX-12345).                 |
| `bill`        | CharField    | -       | Optional decoupled link to an UpcomingExpense name.          |
| `tx_type`     | CharField    | -       | Enum: `EXPENSE`, `INCOME`, `XFER_IN`, `XFER_OUT`.            |
| `uid`         | CharField    | -       | The `user_id` of the owner.                                  |


---

### [PaymentSource](file:///home/pproctor/Documents/python/finance_manager/finance_manager_api/finance/models.py#L87)

Represents financial accounts (Checking, Savings, etc.).


| Field      | Type         | Default   | Description                                                   |
| ---------- | ------------ | --------- | ------------------------------------------------------------- |
| `source`   | CharField    | -         | Unique name of the source (per user).                         |
| `acc_type` | CharField    | `UNKNOWN` | Enum: `SAVINGS`, `CHECKING`, `CASH`, `INVESTMENT`, `EWALLET`. |
| `currency` | CharField    | `"USD"`   | Account currency.                                             |
| `amount`   | DecimalField | `0`       | Current balance (calculated/synced).                          |
| `uid`      | CharField    | -         | The `user_id` of the owner.                                   |


---

### [UpcomingExpense](file:///home/pproctor/Documents/python/finance_manager/finance_manager_api/finance/models.py#L125)

Planned or recurring bills used for forecasting.


| Field          | Type         | Default | Description                                           |
| -------------- | ------------ | ------- | ----------------------------------------------------- |
| `name`         | CharField    | -       | Unique name of the expense (per user).                |
| `amount`       | DecimalField | -       | Expected amount.                                      |
| `due_date`     | DateField    | -       | Next date this expense is due.                        |
| `start_date`   | DateField    | -       | When the recurring cycle starts.                      |
| `end_date`     | DateField    | -       | Optional termination date.                            |
| `paid_flag`    | BooleanField | `False` | Whether it has been satisfied for the current period. |
| `is_recurring` | BooleanField | `False` | Whether it repeats automatically.                     |
| `uid`          | CharField    | -       | The `user_id` of the owner.                           |


---

### [FinancialSnapshot](file:///home/pproctor/Documents/python/finance_manager/finance_manager_api/finance/models.py#L254)

Aggregated financial health data. One per user.


| Field                      | Type         | Description                                            |
| -------------------------- | ------------ | ------------------------------------------------------ |
| `total_assets`             | DecimalField | Sum of all positive account balances.                  |
| `safe_to_spend`            | DecimalField | Assets minus upcoming expenses for the period.         |
| `total_savings`            | DecimalField | Aggregate of all `SAVINGS` accounts.                   |
| `total_checking`           | DecimalField | Aggregate of all `CHECKING` accounts.                  |
| `total_monthly_spending`   | DecimalField | Sum of expenses in the current month.                  |
| `total_remaining_expenses` | DecimalField | Sum of unpaid `UpcomingExpense` records for the month. |


---

## Architectural Note: UUID Decoupling

To support high performance and future microservice migration, this project avoids strict database-level Foreign Keys between most models. Instead:

- Relationships are maintained via **UUID or unique name strings** (stored in `CharField`).
- Referential integrity is enforced at the **Application Layer** (Validators and Signals).
- This prevents database-level locking and allows for easier horizontal scaling.

---

**[Return to Overview](file:///home/pproctor/Documents/python/finance_manager/design_docs/api_docs/00_API_Overview.md)**