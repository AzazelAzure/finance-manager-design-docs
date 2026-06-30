# Data Access Layer

Related: [Architecture](02_Data_Flow_and_Architecture.md) · [Models](03_Models_Reference.md)

Database access is almost entirely Django ORM via **custom managers** in [`finance/management/managers.py`](../../finance_manager_api/finance/management/managers.py). There is no separate `data/` package — complex queries live in managers, `api_tools/query_utils.py`, and service functions.

---

## The `for_user(uid)` pattern

Every core model manager exposes:

```python
Model.objects.for_user(uid)  # uid = str(AppProfile.user_id)
```

| Rule | Rationale |
| :--- | :--- |
| **Never** `Transaction.objects.all()` for user data | Cross-tenant leak |
| Always derive `uid` from `request.user.appprofile.user_id` | Single source of truth |
| Use manager helpers for common filters | Keeps views/services thin |

`AppProfile.objects.for_user(uid)` accepts the same uuid string as other models' `uid` field.

---

## Manager highlights

### TransactionManager

| Method | Purpose |
| :--- | :--- |
| `for_user(uid)` | Base scoped queryset |
| `get_current_month()` | Calendar month filter (profile timezone) |
| `get_by_tag_name(tag)` | JSON `tags` contains match |
| `get_latest()` | Most recent transaction |
| Type / source / bill filters | Used by services + visualization |

Bulk reads for snapshot recompute often load full sets in `Updater` to avoid N+1 aggregates.

### UpcomingExpenseManager

| Method | Purpose |
| :--- | :--- |
| `get_current_month()` | Bills due in month window |
| `get_by_remaining()` | Unpaid bills |
| `for_user(uid)` | Scoped list |

### PaymentSourceManager

Balance reads and type grouping for `Calculator.calc_acc_types`.

### FinancialSnapshotManager

`get_totals(acc_type)` — dynamic field access for account-type columns on snapshot row.

### CategoryManager / TagManager

Name-scoped lookups per `uid`.

---

## Query utilities

[`finance/api_tools/query_utils.py`](../../finance_manager_api/finance/api_tools/query_utils.py):

- `apply_transaction_filters(queryset, **kwargs)` — shared GET `/finance/transactions/` filter logic (month, year, source, category, tag).
- Date range helpers for calendar/visualization endpoints.

Keeps filter semantics identical between list and aggregate endpoints.

---

## Performance practices

### DB hit budget

Project convention (enforced in reviews, DEBUG middleware aids counting):

| Target | Limit |
| :--- | :--- |
| Goal | ≤ **10** queries per request |
| Hard cap | **12** queries |

Because most relations are decoupled `CharField`s, prefer:

- Grouped aggregates (`values().annotate(Sum(...))`) — see `transaction_services.get_transactions`.
- Preloaded lists passed into `Updater` for bulk writes.
- `bulk_create` / `bulk_update` for multi-row POST.

`select_related` helps only where real FKs exist (`SavingsGoal`).

### Aggregations in the database

Use Django `Sum`, `Count`, `Avg` on filtered querysets before Python loops. `Calculator.calc_queryset` still converts currencies row-group but minimizes round-trips.

### Snapshot recompute optimization

`update_transaction` compares PATCH fields before calling full `_tx_snapshot_handler` — reduces write amplification on description-only edits.

---

## Exchange rates as read-only data

| Asset | Path |
| :--- | :--- |
| ECB zip | `finance/data/exchange_rates.zip` |
| Loader | `settings.CURRENCY_CONVERTER` / `SUPPORTED_CURRENCIES` |

Treated as a **production asset**:

- Missing file → conversion raises (no fake rates).
- Refresh: `python manage.py update_conversion_file`.
- CI fetches rates before `pytest`.

`GET /finance/exchange_rates/` builds offline PWA matrix from same source.

---

## Idempotency storage (PWA)

`IdempotencyRecord` table stores replay responses:

- Unique `(uid, key_hash)`.
- Pruned by retention policy (`IDEMPOTENCY_RETENTION_DAYS`).

Middleware reads/writes ORM rows around allowlisted mutators.

---

## Migrations & schema discipline

- Migrations in `finance/migrations/` — **tracked in git** (not gitignored).
- CI runs `makemigrations --check` to prevent drift.
- Recent: `0017` cadence, `0014` balance snapshots, `0016` savings goals, `0018` export token purge.

When adding fields:

1. Model + migration.
2. Serializer + validator.
3. Updater/Calculator if snapshot-affecting.
4. Tests in domain folder.

---

## Testing data access

| Pattern | Location |
| :--- | :--- |
| Factory Boy factories | `finance/factories.py` |
| Deterministic seed | `conftest.py` (`FM_TEST_SEED`) |
| Domain bases | `finance/tests/*_tests/*_base.py` |
| Stress / query counts | `finance/tests/stress_tests/` |

Use `pytest -k snapshot` or domain-specific files when touching managers.

---

**[Return to Overview](00_API_Overview.md)**
