# Authentication & Security

Related: [API Overview](00_API_Overview.md) · [Endpoints](01_Endpoints_and_Contracts.md)

Security configuration is centralized in [`finance_api/settings.py`](../../finance_manager_api/finance_api/settings.py) with view-level exceptions noted below.

---

## Authentication overview

| Mechanism | Package | Usage |
| :--- | :--- | :--- |
| JWT access + refresh | `djangorestframework-simplejwt` | Primary API auth (`Authorization: Bearer`) |
| Session / registration | `dj-rest-auth` + `django-allauth` | `/api/auth/*`, social providers |
| Social OAuth | Google, GitHub | `/api/auth/google/`, `/api/auth/github/` |
| Lockout | `django-axes` | Brute-force protection on login |

**Default DRF classes:** `JWTAuthentication` + `IsAuthenticated` on finance routes.

Custom token views live inline in [`finance_api/urls.py`](../../finance_manager_api/finance_api/urls.py) (`CustomTokenObtainPairView`, `CustomTokenRefreshView`).

---

## JWT policy

```python
# settings.py (representative)
SIMPLE_JWT = {
    "ACCESS_TOKEN_LIFETIME": timedelta(days=1),
    "REFRESH_TOKEN_LIFETIME": timedelta(days=7),
    "ROTATE_REFRESH_TOKENS": True,
    "BLACKLIST_AFTER_ROTATION": True,
}
```

| Behavior | Detail |
| :--- | :--- |
| Login identifier | Username **or** email (case-insensitive lookup) on `/api/token/` and `/api/token/auth/` |
| Refresh hardening | Deleted user on refresh → `401 InvalidToken` (patched in `apps.py`) |
| Token blacklist | Enabled via `rest_framework_simplejwt.token_blacklist` |

### Social login flow

1. Client obtains provider token (Google/GitHub).
2. `POST` to `/api/auth/google/` or `/api/auth/github/` (`auth_views.GoogleLogin` / `GitHubLogin`).
3. allauth adapter validates; API returns JWT pair.
4. Callback URL from `OAUTH_CALLBACK_URL` env.

---

## Password policy

| Control | Setting |
| :--- | :--- |
| Primary hasher | Argon2 |
| Minimum length | 12 |
| Complexity | Custom `ComplexPasswordValidator` (upper, lower, digit, special) |
| Change password | `PATCH /finance/user/` with `old_password` |
| Delete account | `DELETE /finance/user/` with `password` verification |

django-axes: **5** failures → lockout, **1 hour** cooloff, keyed on `(username, ip_address)`. Proxy-aware via `AXES_IPWARE_PROXY_COUNT`.

---

## Authorization model

There are **no per-object DRF permission classes** on most finance views. Isolation is enforced by:

1. `request.user.appprofile.user_id` → `uid` string.
2. Every queryset filtered with `.for_user(uid)`.
3. Cross-user resource access returns **404** where applicable (e.g. savings goals).

Exceptions:

| View | Permission |
| :--- | :--- |
| `UserView` | `IsAuthenticatedOrCreateOnly` (open signup POST) |
| Health / schema / docs | Public |
| Exchange rates | Explicit `IsAuthenticated` (redundant with default) |

---

## CORS, CSRF, cookies

- `corsheaders` first in middleware stack.
- Custom allowed headers: `x-client-build`, `idempotency-key`.
- `CSRF_TRUSTED_ORIGINS` includes production hostnames (`thehivemanager.com`, `api.thehivemanager.com`, jsdevtesting) + local HTTPS sim.
- JWT may be delivered via HttpOnly cookies (`finance-auth`, `finance-refresh-token`) when using dj-rest-auth cookie mode (`USE_JWT=True`).

Production TLS hardening (env-gated): `SECURE_SSL_REDIRECT`, HSTS, secure cookies, `X_FRAME_OPTIONS=DENY`, `SECURE_CONTENT_TYPE_NOSNIFF`.

---

## Account deletion (complete wipe)

`pre_delete` on `User` removes all finance-domain rows keyed by `uid` because decoupled models lack CASCADE FKs:

- Transactions, sources, expenses, categories, tags, snapshot, etc.

No residual ledger data after successful delete.

---

## PWA write contract (security-relevant)

[`PwaWriteContractMiddleware`](../../finance_manager_api/finance/middleware/pwa_write_contract.py):

- Replays mutating requests with same `Idempotency-Key` + body hash.
- Rejects stale client builds when `CLIENT_BUILD_MIN_WRITE` configured.
- Stores responses in `IdempotencyRecord` with retention (`IDEMPOTENCY_RETENTION_DAYS`).

Prevents duplicate offline sync writes and forces client refresh past end-of-support builds.

---

## Observability (PII-safe)

[`ObservabilityMiddleware`](../../finance_manager_api/finance/middleware/observability.py):

- Redis counters keyed by **resolved route pattern** (bounded keyspace).
- Salted IP hashes (`LOG_IP_HASH_SALT`).
- User-agent classification.
- Feeds Celery `security_alerts` and `analytics` rollups.

Support intake (F-013) writes per-UUID diagnostic files under `logs/incidents/` on VPS — operator grep only, not user-facing.

---

## Terms of service (clickwrap)

Shared helper: `finance/api_tools/tos.py`

- `ALLOWED_TOS_VERSIONS` (e.g. `{"1.0"}`).
- Server sets canonical `tos_accepted_at`; client timestamp is not authoritative.
- Required on `/finance/user/` POST and `/api/auth/registration/`.

---

## Key environment variables (security-related)

| Variable | Purpose |
| :--- | :--- |
| `SECRET_KEY` | Django secret |
| `DEBUG` | Must be false in production |
| `ALLOWED_HOSTS`, `CSRF_TRUSTED_ORIGINS`, `CORS_ALLOWED_ORIGINS` | Host/origin allowlists |
| `CLIENT_BUILD_MIN_WRITE`, `API_SERVER_BUILD` | PWA write gate + health reporting |
| `LOG_IP_HASH_SALT` | Observability hashing |
| `SEC_THRESHOLD_*` | Security alert thresholds |
| `BETA_FEATURE_REQUESTS_ENABLED` | Gates FEATURE support tickets |
| `OAUTH_CALLBACK_URL` | Social auth callback |

See `.env.example` for local dev baseline (minimal).

---

## Verification tests (orientation)

| Area | Test module |
| :--- | :--- |
| Email/username token login | `finance/tests/user_tests/test_token_login_identifier.py` |
| Refresh deleted user | user auth security tests |
| Password complexity | user tests |
| PWA write contract | dedicated middleware tests |
| Signup clickwrap | user/registration tests |
| Support redaction | support adversarial tests |

Run: `pytest finance/tests/user_tests/ finance/tests/test_pwa_write_contract.py` (paths illustrative).

---

**[Return to Overview](00_API_Overview.md)**
