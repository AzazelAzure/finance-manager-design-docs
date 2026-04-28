# Authentication & Security

The Finance Manager API implements a multi-layered security strategy to protect user data and ensure secure access across all interfaces.

## Authentication Mechanism

The API uses **JSON Web Tokens (JWT)** via the `djangorestframework-simplejwt` package for session management.

### Token Strategy

- **Access Tokens**: Short-lived (1 day) tokens used for authorizing individual requests.
- **Refresh Tokens**: Longer-lived (7 days) tokens used to obtain new access tokens without requiring the user to re-authenticate.
- **Rotation**: Refresh tokens are rotated upon use (`ROTATE_REFRESH_TOKENS: True`) and old tokens are blacklisted to mitigate replay attacks.
- **Identifier Input**: `/api/token/` and `/api/token/auth/` accept either username or email identifier (case-insensitive lookup before credential validation).
- **Refresh Resilience**: refresh serializers map missing/deleted-user token lookups to `401 InvalidToken` instead of `500`.

### Social Login

Authentication is integrated with major providers via `dj_rest_auth` and `django-allauth`:

- **Google OAuth2**
- **GitHub OAuth2**
These flows exchange provider tokens for internal JWTs, allowing for a seamless single-sign-on (SSO) experience.

---

## Security Hardening Measures

### 1. Verification of Critical Actions

To prevent unauthorized account takeovers or accidental data loss, certain operations require verification of the user's **current password**:

- Updating the account password.
- Deleting the user account.

### 2. Cross-Origin Resource Sharing (CORS) & CSRF

The API is hardened against cross-site attacks:

- **CSRF Protection**: Enabled via `CsrfViewMiddleware`. The `CSRF_TRUSTED_ORIGINS` setting is strictly configured to allow only the trusted frontend domain (e.g., `https://financemanager.local:8443`).
- **Secure Cookies**: JWTs can be configured to be delivered via `HttpOnly` and `Secure` cookies to prevent XSS-based token theft.

### 3. Data Privacy & Account Deletion

We follow a **"Complete Wipe"** policy. When a user deletes their account, the API triggers a series of signals that permanently remove all associated:

- Transactions
- Payment Sources
- Upcoming Expenses
- App Profiles
- Snapshots
No residual financial data is retained after deletion.

---

## Configuration Reference

Key security settings in `settings.py`:

```python
SIMPLE_JWT = {
    "ACCESS_TOKEN_LIFETIME": timedelta(days=1),
    "REFRESH_TOKEN_LIFETIME": timedelta(days=7),
    "ROTATE_REFRESH_TOKENS": True,
    "BLACKLIST_AFTER_ROTATION": True,
}

CSRF_TRUSTED_ORIGINS = [
    "https://financemanager.local:8443",
    "https://api.financemanager.local:8443",
]
```

## Runtime Notes (Current)

- Token obtain route is served by `CustomTokenObtainPairView` with `CustomTokenObtainPairSerializer`.
- Token refresh route is served by `CustomTokenRefreshView` with `CustomTokenRefreshSerializer`.
- Contract verification includes a regression test ensuring email-identifier token login:
  - `finance/tests/user_tests/test_token_login_identifier.py`

---

**[Return to Overview](file:///home/pproctor/Documents/python/finance_manager/design_docs/api_docs/00_API_Overview.md)**