# API Business Rules and Invariants

Related: [Models Reference](03_Models_Reference.md) · [Endpoints](01_Endpoints_and_Contracts.md) · [Business Logic](04_Business_Logic_Reference.md)

Cross-cutting rules enforced by validators, views, services, and `Updater`. Breaking these causes snapshot drift, 403s, or data corruption.

---

## Transactions

| Rule | Enforcement |
| :--- | :--- |
| Client cannot supply `tx_id` or `entry_id` on create | View returns **403** |
| `PATCH` requires `date` in body | View-level check |
| Amount sign normalization | `Updater.fix_tx_data`: `EXPENSE`/`XFER_OUT` → negative; `INCOME`/`XFER_IN` → positive |
| `source` must exist for user | `TransactionValidator` |
| `currency` must be supported | `validation_core._validate_currency` |
| Future-dated transactions disallowed | Validator date rules |
| Category defaults applied when missing | Fixer assigns by `tx_type` |
| Bill link by **name** | Matching `UpcomingExpense.name`; payment can roll recurring due dates |
| Balance-affecting PATCH fields | `amount`, `source`, `currency`, `tx_type`, and `date`/`bill` when bill-linked → snapshot recompute |
| Idempotent DELETE | With `Idempotency-Key`, missing tx may return success envelope |

**Transfers:** always submit `XFER_OUT` + `XFER_IN` pair; leak metric should trend toward zero.

---

## Payment sources

| Rule | Enforcement |
| :--- | :--- |
| Reserved source `unknown` | Auto-created; **403** on create/rename/delete/PATCH as user action |
| `unknown` ∉ `spend_accounts` | Profile validator |
| Unique `source` name per `uid` | DB constraint + validator |
| Delete → orphan txs → `unknown` | `pre_delete` signal on `PaymentSource` |
| Source balance updated on tx writes | `Updater.transaction_handler` |

---

## App profile

| Rule | Enforcement |
| :--- | :--- |
| `spend_accounts` entries must name existing sources | Profile validator |
| Cannot include `unknown` in spend list | Validator |
| `base_currency`, `timezone`, `sts_window_mode`, pay-cycle fields | Changes trigger snapshot recompute |
| `completed_tours` | JSON list for guided tour state (F-007) |

---

## Categories & tags

| Rule | Enforcement |
| :--- | :--- |
| Unique category name per user | DB + validator |
| System categories (`expense`, `income`, `transfer`) | Protected from delete/rename destructive ops |
| Category delete | Transactions remapped to type-appropriate defaults via `Updater.category_deleted` |
| Tags stored as single JSON list per user | `Tag` model; API accepts multiple input shapes |
| Tag rename/delete | Mapping payloads; transaction `tags` JSON updated in services |

---

## Upcoming expenses (bills)

| Rule | Enforcement |
| :--- | :--- |
| Unique `name` per `uid` | DB constraint |
| `end_date` ≥ relevant start boundaries | Serializer validation |
| `cadence=custom` ⇒ `custom_interval_days > 0` | Serializer `_validate_cadence_fields` |
| Non-custom cadence clears `custom_interval_days` | Serializer |
| `planned_partial_amount <= amount` | DB check constraint |
| Effective STS obligation | `Calculator._effective_bill_amount`: partial → residual → full `amount` |
| Recurring + paid via tx link | `Updater._handle_upcoming` advances `due_date` via `advance_bill_due_date` |
| Catch-up endpoint | Max **24** periods (`MAX_CATCH_UP_PERIODS`) |
| Legacy field aliases on PATCH | `paid`→`paid_flag`, `recurring_flag`→`is_recurring` |

---

## Savings goals

| Rule | Enforcement |
| :--- | :--- |
| Real FK to `AppProfile` and optional `PaymentSource` | ORM |
| Cross-user access | **404** (not 403) in views |
| `per_cycle_required` | Computed server-side from pay-cycle frequency |

---

## User account & ToS

| Rule | Enforcement |
| :--- | :--- |
| Signup requires allowed `tos_version` | `UserSerializer` / register serializer |
| `tos_accepted_at` from client not trusted as authority | Server sets canonical timestamp |
| Password change | Requires `old_password` verification |
| Account delete | Requires `password` confirmation; full finance wipe via `pre_delete User` |
| Case-insensitive username/email uniqueness | `UserView` POST |

---

## Export & privacy

| Rule | Status |
| :--- | :--- |
| CSV + full JSON export | Authenticated GET only |
| Public share bearer URLs | **Removed 2026-06-29** — do not reintroduce without privacy gate |

---

## Support intake

| Rule | Enforcement |
| :--- | :--- |
| Rate limit 20/min per user | `SupportTicketThrottle` |
| `FEATURE` tickets when `BETA_FEATURE_REQUESTS_ENABLED=false` | Serializer blocks |
| Secret redaction in `nature`/`comment` | Serializer sanitization |
| Operator notify | Async Celery; UUID-only in operator email |

---

## PWA offline write contract

| Rule | Enforcement |
| :--- | :--- |
| `Idempotency-Key` only on allowlisted `/finance/*` mutators | Middleware |
| `X-Client-Build` minimum when env set | `409 CLIENT_BUILD_UNSUPPORTED` |
| Replay returns stored status + body | `IdempotencyRecord` |

Allowlisted paths include: transaction create/detail, upcoming expense create/detail, categories, tags, sources detail, app profile PATCH.

---

## Currency & snapshots

| Rule | Enforcement |
| :--- | :--- |
| All snapshot totals in `base_currency` | `Calculator._calc_totals` |
| Missing exchange rate file/rate | `convert_currency` **raises** (no silent fallback) |
| ECB rates file | `finance/data/exchange_rates.zip`; refresh via `update_conversion_file` command |

---

## Why these matter

Financial fidelity depends on **signed amounts**, **consistent source balances**, and **denormalized snapshots** staying in sync. The decoupled `uid` model means the database will not save you from orphan references — validators and signals are part of the contract, not optional guards.

When adding features, extend tests under `finance/tests/<domain>_tests/` and verify snapshot + source balance invariants.

---

**[Return to Overview](00_API_Overview.md)**
