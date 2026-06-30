# Finance Manager API Documentation

Welcome to the Finance Manager API documentation. This vault section is the **onboarding guide and reference** for the Django REST backend (`finance_manager_api`). A developer who reads these pages in order should understand how the API is structured, how data flows, and where to change behavior safely.

**Implementation home:** `finance_manager_api/` (Python ≥3.12, Django 6, DRF 3.17, `uv` + `pyproject.toml`).

## Quick start for new contributors

1. **Clone & run** — see `finance_manager_api/README.md` for `uv sync`, migrations, and local server (`manage.py runserver` or project Docker scripts).
2. **Read architecture** — [Data Flow & Architecture](02_Data_Flow_and_Architecture.md) (request pipeline, decoupled `uid` model, signals).
3. **Skim endpoints** — [Endpoints & Contracts](01_Endpoints_and_Contracts.md) (all routes; finance mounts at `/finance/`, **not** `/api/finance/`).
4. **Learn the domain** — [Models Reference](03_Models_Reference.md) + [Business Rules & Invariants](03_Business_Rules_and_Invariants.md).
5. **Change calculations** — [Business Logic Reference](04_Business_Logic_Reference.md) (`Calculator`, `Updater`, bill recurrence, pay-cycle STS).
6. **Auth & hardening** — [Authentication & Security](05_Authentication_and_Security.md).
7. **Live contract** — Swagger UI at `/api/docs/`, OpenAPI at `/api/schema/` (drf-spectacular).

Run tests: `pytest` from `finance_manager_api/` (see `finance/tests/` tree). CI uses `uv sync --frozen` and `makemigrations --check`.

## Documentation map

| Doc | Purpose |
| :--- | :--- |
| [02_Data_Flow_and_Architecture](02_Data_Flow_and_Architecture.md) | Layered pipeline, middleware, Celery, signals, OpenAPI |
| [01_Endpoints_and_Contracts](01_Endpoints_and_Contracts.md) | Full HTTP reference (auth, health, finance domain) |
| [03_Models_Reference](03_Models_Reference.md) | Every model, fields, constraints, managers |
| [03_Business_Rules_and_Invariants](03_Business_Rules_and_Invariants.md) | Cross-cutting rules clients and servers must honor |
| [04_Business_Logic_Reference](04_Business_Logic_Reference.md) | `fincalc`, `updaters`, recurrence, snapshots, currency |
| [05_Authentication_and_Security](05_Authentication_and_Security.md) | JWT, social auth, axes, passwords, PWA write contract |
| [06_Data_Access_Layer](06_Data_Access_Layer.md) | Custom managers, `for_user`, query optimization |
| [07_Validators_and_Tools](07_Validators_and_Tools.md) | Validators, serializers, signals, shared utilities |

## What this API is (and is not)

The Finance Manager API is **not** a generic DRF ViewSet CRUD app. Important traits:

- **Hand-written `APIView` classes** — no routers; each endpoint is explicit in `finance_api/urls.py`.
- **Decoupled ownership** — most models store `uid` (the user's `AppProfile.user_id` as a string) instead of Django foreign keys. Integrity is enforced in validators, services, and signals.
- **Write path → snapshot** — mutating transactions, sources, bills, or profile budget fields triggers `FinancialSnapshot` recomputation via `Updater`.
- **Multi-client** — flagship **Web SPA** (PWA offline outbox), **CLI**, and internal tools share the same contracts. Reflex is archived; do not treat it as the primary client.

## Core responsibilities

- **Ledger** — transactions, payment sources, categories, tags, upcoming expenses (bills).
- **Financial intelligence** — denormalized `FinancialSnapshot` (safe-to-spend, assets, monthly spend, remaining bills), calendar/visualization aggregates, F-001 balance history.
- **User lifecycle** — signup with ToS clickwrap, profile/pay-cycle settings, password change, account deletion (complete wipe).
- **Operations** — support ticket intake, export (CSV + full JSON backup), exchange-rate matrix for offline PWA, health/build probes, observability counters.

## URL layout (high level)

All routes are defined in [`finance_api/urls.py`](../../finance_manager_api/finance_api/urls.py).

| Prefix | Examples | Auth |
| :--- | :--- | :--- |
| `admin/` | Django admin | Staff |
| `api/health/` | Liveness + build metadata | Public |
| `api/schema/`, `api/docs/` | OpenAPI + Swagger | Public (schema) |
| `api/token/*`, `api/auth/*` | JWT + dj-rest-auth + Google/GitHub | Mixed |
| `finance/*` | Domain API | `IsAuthenticated` (default) |

Production host: `https://api.thehivemanager.com` (TLS via blue-green proxy on host `:8443` in ecosystem deploys).

## Technical stack

| Layer | Choice |
| :--- | :--- |
| Framework | Django 6 + Django REST Framework |
| Database | PostgreSQL (production) / SQLite (local dev & CI) |
| Cache / broker | Redis (production); LocMem in pytest |
| Auth | SimpleJWT + dj-rest-auth + django-allauth (Google, GitHub) |
| Lockout | django-axes |
| Tasks | Celery (beat schedule in settings) |
| API docs | drf-spectacular (OpenAPI 3) |
| Currency | `currency_converter` + ECB rates in `finance/data/exchange_rates.zip` |
| Logging | loguru (per-user context middleware) |

## Recent feature areas (orientation)

| Area | Models / routes | Notes |
| :--- | :--- | :--- |
| Bill recurrence engine | `UpcomingExpense.cadence`, `catch-up/` | Replaces start/due-delta stopgap; semimonthly 1st/15th |
| F-001 balance history | `BalanceSnapshot`, `/finance/balance-history/` | Nightly Celery capture + backfill command |
| F-004 pay cycle | `AppProfile.sts_window_mode`, `pay_cycle_*` | STS window vs calendar month |
| F-005 savings goals | `SavingsGoal`, `/finance/savings-goals/` | Manual view parsing (no serializer) |
| F-010 export | CSV + full JSON backup | **Share tokens removed 2026-06-29** (privacy); model retained for audit |
| PWA D2 | `IdempotencyRecord`, write middleware | Idempotency-Key + `X-Client-Build` on allowlisted mutators |
| F-012/F-013 support | `SupportTicket`, `/finance/support/tickets/` | Async operator notify + incident logs |
| F-014 usage | `DailyUsageSnapshot`, Celery rollup | DAU/MAU operator alerts |

## Related docs outside this vault

| Topic | Location |
| :--- | :--- |
| Web client / PWA | `finance_manager_web/README.md`, `40_System_Design/12_Web_PWA_Install_Offline_Sync.md` |
| CLI | `cli_docs/00_CLI_Overview.md` |
| Deployment | `40_System_Design/05_Deployment_Strategy.md`, parent `governance/deployment_protocol.md` |
| Runtime ownership | Parent `governance/Runtime_Signup_Sheet.md` |

---

**Next:** [Data Flow & Architecture](02_Data_Flow_and_Architecture.md)
