# Endpoints & Contracts

Related: [API Overview](00_API_Overview.md) · [Business Rules](03_Business_Rules_and_Invariants.md)

Complete HTTP reference for `finance_manager_api`. Unless noted, endpoints require **`Authorization: Bearer <access_token>`** (SimpleJWT). Global default: `IsAuthenticated`.

## Base URLs

| Environment | Base |
| :--- | :--- |
| Production | `https://api.thehivemanager.com` |
| Local dev | `http://localhost:8000` or HTTPS sim on `:8443` per ecosystem README |

**Path convention:** Finance routes mount at **`/finance/`** (not `/api/finance/`). Auth and health use **`/api/`**.

## Response patterns

Most finance mutators return a **set envelope** (not a generic `{status, data}` wrapper):

```json
{
  "accepted": [ { "...entity fields..." } ],
  "rejected": [ { "input": {}, "errors": {} } ],
  "snapshot": { "total_assets": "...", "safe_to_spend": "...", "...": "..." }
}
```

- `snapshot` may be `null` before onboarding completes.
- List GET endpoints often return domain-specific shapes (transactions include computed totals; calendar/visualization return aggregate packets).
- Errors: DRF standard (`400` validation, `401` auth, `403` forbidden, `404` not found, `409` client build unsupported).

### PWA write headers (allowlisted mutators)

| Header | Purpose |
| :--- | :--- |
| `Idempotency-Key` | Replay-safe offline outbox (required on allowlisted `POST/PATCH/PUT/DELETE` under `/finance/*`) |
| `X-Client-Build` | Build stamp; rejected with `409 CLIENT_BUILD_UNSUPPORTED` when below `CLIENT_BUILD_MIN_WRITE` |

Off-allowlist `Idempotency-Key` → `400 IDEMPOTENCY_SCOPE`.

---

## Operational & schema

| Endpoint | Method | Auth | Description |
| :--- | :--- | :--- | :--- |
| `/api/health/` | `GET` | Public | Liveness: `status`, `api_server_build`, `min_client_build_write` |
| `/api/schema/` | `GET` | Public | OpenAPI 3 schema (drf-spectacular) |
| `/api/docs/` | `GET` | Public | Swagger UI |
| `admin/` | * | Staff | Django admin |

---

## Authentication

| Endpoint | Method | Auth | Description |
| :--- | :--- | :--- | :--- |
| `/api/token/` | `POST` | Public | Obtain access + refresh JWT. Body: `username` **or** `email` + `password` |
| `/api/token/refresh/` | `POST` | Public | Refresh access token; deleted user → `401 InvalidToken` (not 500) |
| `/api/token/verify/` | `POST` | Public | Verify token validity |
| `/api/token/auth/` | `POST` | Public | Alias of token obtain |
| `/api/auth/` | * | Mixed | dj-rest-auth (login, logout, password) |
| `/api/auth/registration/` | `POST` | Public | Register via `EmailUniqueRegisterSerializer` + ToS fields |
| `/api/auth/google/` | `POST` | Public | Google OAuth2 → JWT (`GoogleLogin`) |
| `/api/auth/github/` | `POST` | Public | GitHub OAuth2 → JWT (`GitHubLogin`) |

Token lifetimes: access **1 day**, refresh **7 days**, rotate + blacklist on refresh.

---

## User account (`/finance/user/`)

Permission: `IsAuthenticatedOrCreateOnly` — **open `POST` signup**, authenticated for other methods.

| Method | Body / notes | Description |
| :--- | :--- | :--- |
| `POST` | `username`, `email`, `password`, `tos_version`, `tos_accepted_at` | Create user + profile (signals seed defaults). Case-insensitive username/email uniqueness. |
| `GET` | — | Current user summary |
| `PATCH` | `old_password`, `new_password` | Password change (requires current password) |
| `DELETE` | `password` | Account deletion; cascades all finance data via signals |

ToS: server validates `tos_version` against `ALLOWED_TOS_VERSIONS`; timestamp set server-side.

---

## App profile & dashboard snapshot

| Endpoint | Method | Description |
| :--- | :--- | :--- |
| `/finance/appprofile/` | `GET` | Profile: `base_currency`, `timezone`, `start_of_week`, `spend_accounts`, pay-cycle fields (`sts_window_mode`, `pay_cycle_frequency`, `pay_cycle_anchor_date`), `completed_tours`, `feature_requests_enabled` |
| `/finance/appprofile/` | `PATCH` | Partial profile update; budget-affecting fields trigger snapshot recompute |
| `/finance/appprofile/snapshot/` | `GET` | Full dashboard snapshot (`FinancialSnapshot` fields in base currency) |

---

## Transactions

| Endpoint | Method | Query / body | Description |
| :--- | :--- | :--- | :--- |
| `/finance/transactions/` | `GET` | `month`, `year`, `source`, `category`, `tag`, date filters | Filtered list + **computed totals** by type in base currency |
| `/finance/transactions/` | `POST` | Single object **or** JSON **array** for bulk | Create; client `tx_id` / `entry_id` forbidden (403) |
| `/finance/transactions/<tx_id>/` | `GET` | — | Single transaction |
| `/finance/transactions/<tx_id>/` | `PATCH` | **`date` required** in body | Partial update; balance-affecting fields trigger source + snapshot updates |
| `/finance/transactions/<tx_id>/` | `DELETE` | — | Delete; idempotent success shape when `Idempotency-Key` + already gone |
| `/finance/transactions/calendar/` | `GET` | `start_date`, `end_date`, `granularity` (`daily`/`weekly`/`monthly`) | Heat-map aggregates |
| `/finance/transactions/visualization/` | `GET` | `start_date`, `end_date` | Chart packets (flow, category, tag, daily spend/income) |

**Create payload fields:** `date`, `amount`, `source`, `currency`, `tx_type` (`EXPENSE`|`INCOME`|`XFER_IN`|`XFER_OUT`); optional `description`, `category`, `bill`, `tags` (list).

**Transfers:** submit paired `XFER_OUT` + `XFER_IN` entries (web client auto-fills metadata).

**Sign convention:** `EXPENSE` and `XFER_OUT` stored **negative**; `INCOME` and `XFER_IN` **positive** (normalized in `Updater.fix_tx_data`).

---

## Payment sources

| Endpoint | Method | Description |
| :--- | :--- | :--- |
| `/finance/sources/` | `GET` | List all sources for user |
| `/finance/sources/` | `POST` | Create: `source`, `acc_type`, `currency`, `amount` |
| `/finance/sources/<name>/` | `GET` | Detail |
| `/finance/sources/<name>/` | `PUT` / `PATCH` | Update type and/or balance |
| `/finance/sources/<name>/` | `DELETE` | Delete; transactions reassigned to `unknown` via signal |

`acc_type`: `SAVINGS`, `CHECKING`, `CASH`, `INVESTMENT`, `EWALLET`, `UNKNOWN`.

Reserved source **`unknown`**: auto-created, cannot be renamed/deleted/used in `spend_accounts` (403 on mutate).

---

## Upcoming expenses (bills)

| Endpoint | Method | Description |
| :--- | :--- | :--- |
| `/finance/upcoming_expenses/` | `GET` | List bills |
| `/finance/upcoming_expenses/` | `POST` | Create bill |
| `/finance/upcoming_expenses/<name>/` | `GET` | Detail by **name** (not numeric id) |
| `/finance/upcoming_expenses/<name>/` | `PUT` / `PATCH` | Update; accepts legacy aliases `paid`→`paid_flag`, `recurring_flag`→`is_recurring` |
| `/finance/upcoming_expenses/<name>/` | `DELETE` | Delete |
| `/finance/upcoming_expenses/<name>/catch-up/` | `POST` | Advance overdue recurring bills (max **24** periods per call) |

**Cadence fields (recurrence engine):** `cadence` — `weekly` | `biweekly` | `semimonthly` | `monthly` | `quarterly` | `annual` | `custom` (default `monthly`). `custom` requires positive `custom_interval_days`.

**F-004 bill realism:** `bill_class` (`rigid`|`volatile`), `planned_partial_amount`, `cycle_residual_amount`, `remainder_due_date` — affect effective obligation in `Calculator._effective_bill_amount`.

---

## Categories & tags

| Endpoint | Method | Description |
| :--- | :--- | :--- |
| `/finance/categories/` | `GET`, `POST` | List / create category names |
| `/finance/categories/<cat_name>/` | `GET`, `PATCH`, `PUT`, `DELETE` | CRUD by name; system categories protected |
| `/finance/tags/` | `GET`, `POST`, `PATCH`, `PUT`, `DELETE` | Single endpoint; tags stored as one JSON list row per user |

Tag input accepts array, string, or CSV shapes on write.

---

## Savings goals (F-005)

| Endpoint | Method | Description |
| :--- | :--- | :--- |
| `/finance/savings-goals/` | `GET`, `POST` | List / create goals |
| `/finance/savings-goals/<pk>/` | `GET`, `PATCH`, `DELETE` | Integer primary key (real FKs to `AppProfile` + optional `PaymentSource`) |

Views parse/validate manually (no DRF serializer). Response includes computed `per_cycle_required` from pay-cycle frequency.

---

## Balance history (F-001)

| Endpoint | Method | Query | Description |
| :--- | :--- | :--- | :--- |
| `/finance/balance-history/` | `GET` | `range` (`7d`/`30d`/`90d`/`all`), optional `start`/`end` | Per-source closing balance series in base currency |

Populated by nightly Celery task + `backfill_balance_snapshots` command.

---

## Export (F-010)

| Endpoint | Method | Description |
| :--- | :--- | :--- |
| `/finance/export/transactions/csv/` | `GET` | Streaming CSV of transactions |
| `/finance/export/full/` | `GET` | Full JSON backup dump |

> **Removed 2026-06-29:** Public share-link endpoints (`/finance/export/share/`) were deleted after privacy review. `ExportShareToken` model retained for audit only; migration `0018` cleared outstanding rows.

---

## Exchange rates (PWA offline)

| Endpoint | Method | Description |
| :--- | :--- | :--- |
| `/finance/exchange_rates/` | `GET` | Pairwise rate matrix for user's currencies (≤24 codes) from ECB zip |

---

## Support & bug intake

| Endpoint | Method | Description |
| :--- | :--- | :--- |
| `/finance/support/tickets/` | `POST` | Durable ticket queue (`BUG` / `FEATURE`); async operator notify + user confirmation email. Throttled 20/min. FEATURE gated by `BETA_FEATURE_REQUESTS_ENABLED`. |
| `/finance/bug-report/` | `POST` | **Legacy** synchronous email path (prefer support tickets for new work) |

Support payload: `report_type`, `severity`, `nature`, `comment`; secrets redacted in validation.

---

## Error codes worth knowing

| Code | Typical cause |
| :--- | :--- |
| `400` | Serializer/validator failure, bad date range, idempotency scope |
| `401` | Missing/expired JWT, invalid refresh for deleted user |
| `403` | Client `tx_id`, reserved `unknown` source, feature flag off |
| `404` | Wrong user scope (e.g. savings goal pk) |
| `409` | `CLIENT_BUILD_UNSUPPORTED` |
| `429` | Support ticket throttle |

---

## Client integration checklist

1. Obtain JWT via `/api/token/` (email or username).
2. On writes from PWA offline outbox: send `Idempotency-Key` + `X-Client-Build` on allowlisted routes.
3. After mutations, refresh dashboard from `snapshot` in response or `GET /finance/appprofile/snapshot/`.
4. Use `/api/docs/` to verify payload fields when adding new UI surfaces.
5. For CLI parity, mirror the same paths (see `cli_docs/`).

---

**[Return to Overview](00_API_Overview.md)** · **See also:** [Models Reference](03_Models_Reference.md)
