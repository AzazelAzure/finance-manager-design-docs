# Validators & Tools

Related: [Architecture](02_Data_Flow_and_Architecture.md) · [Business Rules](03_Business_Rules_and_Invariants.md)

Validation and shared infrastructure live in [`finance/validators/`](../../finance_manager_api/finance/validators/) and [`finance/api_tools/`](../../finance_manager_api/finance/api_tools/).

---

## Validation architecture

Validators are primarily **decorators** applied to service functions:

```python
@validator.UserValidator
@TransactionValidator
@transaction.atomic
def create_transaction(uid, **kwargs):
    ...
```

[`finance/logic/validators.py`](../../finance_manager_api/finance/logic/validators.py) re-exports decorators for backward compatibility.

### Core primitives (`validation_core.py`)

| Function | Purpose |
| :--- | :--- |
| `_validate_currency(code)` | Supported ISO code; returns uppercase |
| `_validate_timezone(tz)` | Canonical IANA name |

### Domain modules

| Module | Guards |
| :--- | :--- |
| `tx_validators.py` | Source exists, category, dates, duplicate `tx_id`, future dates |
| `source_validators.py` | `acc_type`, unique name, `unknown` protection |
| `exp_validators.py` | Bill dates, cadence fields, amount constraints |
| `user_validators.py` | Password verification, deletion safety |
| `profile_validators.py` | `spend_accounts` membership, timezone/currency |
| `cat_validators.py` | System category protection |
| `tag_validators.py` | Tag list mutations |

**Fail fast:** validation errors become DRF `ValidationError` before ORM writes.

---

## Serializers (`api_tools/serializers/`)

Contract definitions between JSON and Python dicts. **Most are plain `Serializer`**, not `ModelSerializer` — matches decoupled models.

| Module | Key serializers |
| :--- | :--- |
| `tx_serializers.py` | `TransactionSerializer`, `TransactionSetSerializer`, calendar/visualization returns |
| `base_serializers.py` | `FinancialSnapshotSerializer`, `UserSerializer`, `PasswordChangeSerializer` |
| `profile_serializers.py` | `AppProfileGetSerializer`, `SnapshotSerializer` |
| `exp_serializers.py` | Cadence validation, legacy field aliases |
| `src_serializers.py`, `cat_serializers.py`, `tag_serializers.py` | CRUD + `*SetReturnSerializer` envelopes |
| `register_serializers.py` | `EmailUniqueRegisterSerializer` + ToS |
| `support_serializers.py` | `SupportTicketSerializer` (ModelSerializer) + redaction |
| `balance_serializers.py` | Balance history response shapes |

### Return envelope pattern

`*SetReturnSerializer` families include:

- `accepted` — successful entities
- `rejected` — validation failures with `input` + `errors`
- `snapshot` — post-write `FinancialSnapshot` (nullable)

**Exception:** `SavingsGoal` views parse JSON manually — no serializer module.

---

## Signals (`api_tools/signals.py`)

| Signal | Handler summary |
| :--- | :--- |
| `post_save(User)` | `_generate_base_profile`: AppProfile, FinancialSnapshot, `cash` + `unknown` sources |
| `user_logged_in` | Heal missing profile / `unknown` source |
| `pre_delete(PaymentSource)` | Reassign txs → `unknown` (contextvar skip during user wipe) |
| `pre_delete(User)` | Manual cascade delete all `uid`-keyed finance rows |

Signals handle **integrity only** — not business calculations (those stay in `Updater`).

---

## Other api_tools

| File | Purpose |
| :--- | :--- |
| `query_utils.py` | Shared transaction filters |
| `tos.py` | ToS version allowlist + server timestamp |
| `spectactular_serializers.py` | OpenAPI helper serializers (filename typo historical) |

---

## Middleware (cross-cutting tools)

See [Authentication & Security](05_Authentication_and_Security.md) for detail.

| Middleware | Role |
| :--- | :--- |
| `UserLogContextMiddleware` | Bind user id to loguru context |
| `DBHitCounterMiddleware` | DEBUG query counting |
| `PwaWriteContractMiddleware` | Idempotency + client build |
| `ObservabilityMiddleware` | Metrics + security counters |

---

## Management commands (operator tools)

| Command | Purpose |
| :--- | :--- |
| `update_conversion_file` | Fetch ECB rates into zip |
| `backfill_balance_snapshots` | F-001 historical snapshots |
| `create_ux_testuser` | Deterministic demo seed |
| `prod_setup` / `schema_setup` | Environment bootstrap |
| `benchmark_calendar_visualization` | Perf benchmarking |

Invoke via `python manage.py <command>` inside API container or venv.

---

## Best practices

1. **Validators before services** — never skip decorators on new service entrypoints.
2. **Canonical casing** — currencies uppercase, timezones IANA from validators.
3. **No heavy logic in signals** — queue Celery for email/notify work.
4. **Serializer = contract** — update OpenAPI `@extend_schema` when response shapes change.
5. **Redaction** — support and log paths must use shared redaction helpers for secrets.

---

## Adding a new validated write path

1. Define request/return serializers.
2. Add domain validator decorator or extend existing.
3. Implement `services/<domain>_services.py` function with `@transaction.atomic`.
4. Call `Updater` handler for snapshot side effects.
5. Add tests: happy path, validation errors, snapshot assertion, idempotency if PWA-visible.

---

**[Return to Overview](00_API_Overview.md)**
