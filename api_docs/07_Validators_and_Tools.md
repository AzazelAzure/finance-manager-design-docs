# Validators & Tools

This document describes the shared validation logic and utility functions that power the Finance Manager API.

## Validators (`validators/`)

The validation layer ensures that all data entering the logic layer is clean, formatted, and adheres to business rules.

### Core Primitives (`validation_core.py`)

These are shared validation functions used across all domain-specific validators.

- `**_validate_currency(code)**`: Checks if the currency code exists in the IANA-based supported currencies list and returns the uppercase version.
- `**_validate_timezone(tz)**`: Ensures the timezone string matches a canonical IANA name (e.g., `America/New_York`) and corrects casing if necessary.

### Domain-Specific Validators

Each domain (Transactions, Sources, Expenses) has its own validator module:

- `**tx_validators.py**`: Ensures transactions have a valid source, category, and date. It also prevents duplicate transactions via `tx_id` checks.
- `**source_validators.py**`: Validates account types and ensures source names are unique per user.
- `**user_validators.py**`: Handles sensitive operations like password verification and account deletion safety checks.

---

## API Tools (`api_tools/`)

The `api_tools` directory contains reusable utilities and framework-level hooks.

### Signals (`signals.py`)

Django signals are used to handle side effects that must occur automatically during the lifecycle of a model instance.


| Signal                       | Trigger           | Action                                                                                                           |
| ---------------------------- | ----------------- | ---------------------------------------------------------------------------------------------------------------- |
| `post_save (User)`           | User Registration | Automatically creates an `AppProfile` and `FinancialSnapshot`. Seeds default `CASH` and `UNKNOWN` sources.       |
| `pre_delete (User)`          | User Deletion     | Triggers a "Complete Wipe" of all financial data (Transactions, Sources, etc.) associated with the user's `uid`. |
| `pre_delete (PaymentSource)` | Source Deletion   | Reassigns any transactions linked to the deleted source to the `UNKNOWN` source to prevent orphaned records.     |
| `user_logged_in`             | User Login        | Performs a health check on the user's profile and recreates any missing mandatory sources (like `unknown`).      |


### Serializers (`serializers/`)

While technically part of the View layer, serializers are stored in `api_tools` to highlight their role as contract definitions. They handle the translation between JSON payloads and Python objects.

### Query Utils (`query_utils.py`)

Contains helper functions for building complex ORM queries, particularly those involving date ranges and currency-aware aggregations.

---

## Best Practices for Validators & Tools

1. **Fail Fast**: Validation should occur as early as possible in the request lifecycle (ideally at the View/Serializer level).
2. **Canonical Casing**: Tools should always return data in canonical casing (e.g., uppercase for Currencies, IANA-standard for Timezones).
3. **No Logic in Signals**: Signals should only be used for simple, non-blocking side effects or mandatory data integrity tasks. Complex business logic belongs in the `Logic` layer.

---

**[Return to Overview](file:///home/pproctor/Documents/python/finance_manager/design_docs/api_docs/00_API_Overview.md)**