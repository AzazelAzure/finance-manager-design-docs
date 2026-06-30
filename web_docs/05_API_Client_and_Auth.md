# API Client & Auth

Related: [State & Data](03_State_and_Data.md) · [API Endpoints](../api_docs/01_Endpoints_and_Contracts.md)

All HTTP traffic flows through [`src/api/client.ts`](../../finance_manager_web/src/api/client.ts). This is the **single integration point** with the Django API.

## Base URL resolution

[`src/lib/apiBaseUrl.ts`](../../finance_manager_web/src/lib/apiBaseUrl.ts):

| Input | Result |
| :--- | :--- |
| `VITE_API_BASE_URL` | Default production API (no trailing slash) |
| Host `jsdevtesting.thehivemanager.com` | `VITE_STAGING_API_BASE_URL` when set |
| Local dev | `http://127.0.0.1:8000` typical in `.env.local` |

**Path prefixes on the wire:**

| Family | Example |
| :--- | :--- |
| Finance domain | `/finance/transactions/`, `/finance/appprofile/snapshot/` |
| Auth | `/api/token/`, `/api/token/refresh/`, `/api/auth/google/` |
| Health | `/api/health/` |

There is **no** `/api/finance/` prefix.

## Axios instance

```typescript
// Simplified — see client.ts for full implementation
export const api = axios.create({
  baseURL: resolveApiBaseUrl(),
  headers: { "Content-Type": "application/json" },
});
```

Custom **adapter** wraps the browser adapter to queue offline writes before network I/O.

## Request interceptor

1. Attach `Authorization: Bearer <access>` from `getEffectiveAccessTokenForSession()`.
2. If access missing but refresh present (and not refresh URL), attempt single-flight `refreshAccessToken()` first.
3. For mutating methods (`POST`, `PUT`, `PATCH`, `DELETE`):
   - Always set **`X-Client-Build`** header (`CLIENT_BUILD` from vite define).
   - On **online** allowlisted writes, generate **`Idempotency-Key`** (UUID) if not already set.

Offline queued writes store the idempotency key on the outbox row for replay.

## Response interceptor

| Condition | Action |
| :--- | :--- |
| Success | `markApiReachable()` |
| Network error on allowlisted mutating request | Retroactive enqueue (`canRetroactivelyQueue`) if applicable |
| `409` + `CLIENT_BUILD_UNSUPPORTED` | `dispatchClientBuildUnsupported` → upgrade modal |
| `401` | Single-flight refresh + retry once; else clear session + redirect `/login` |

`refreshClient.ts` is a **separate** axios instance without interceptors for `POST /api/token/refresh/` to avoid recursion.

### Post-auth-failure cleanup

Concurrent 401s share one `postAuthFailureCleanup` guard to avoid N× `queryClient.clear()`.

## Login & signup

[`api/auth.ts`](../../finance_manager_web/src/api/auth.ts):

| Action | Endpoint |
| :--- | :--- |
| Login | `POST /api/token/` — username or email + password |
| Signup | `POST /finance/user/` or registration via `/api/auth/registration/` depending on flow |

On success, `setSession({ access, refresh })` in localStorage and dispatch `fm-auth-changed`.

Signup sets onboarding marker for wizard (see [Routing](01_Routing_and_Navigation.md)).

## Typed API modules

Each module exports async functions used as TanStack `queryFn` / `mutationFn`:

- Parse API envelopes (`accepted`, `rejected`, `snapshot`).
- Apply offline cache-first branches via `preferOfflineCaches()` / PWA local-first helpers.
- Call overlay merge functions before returning to UI.

Shared types: [`api/types.ts`](../../finance_manager_web/src/api/types.ts) — keep in sync with API serializers.

## Headers reference

| Header | When | Server behavior |
| :--- | :--- | :--- |
| `Authorization` | Authenticated requests | JWT validation |
| `X-Client-Build` | All mutating methods | Compared to `CLIENT_BUILD_MIN_WRITE` → 409 if stale |
| `Idempotency-Key` | Allowlisted online writes + all outbox replays | Replay cached response; see API middleware |
| `Content-Type` | JSON bodies | `application/json` |

CORS: browser `Origin` must be in API `CORS_ALLOWED_ORIGINS`. Staging jsdevtesting origins must be explicitly allowed or login fails with network-like errors.

## CSP / connect-src

[`index.html`](../../finance_manager_web/index.html) `connect-src` allowlist includes production API, staging API, and local dev ports.

## Error surfaces in UI

| Layer | UX |
| :--- | :--- |
| Query error | `ErrorState` component on pages |
| Mutation error | Form-level or toast patterns per page |
| Global render error | `ErrorBoundary` (`main` branch) |
| Build unsupported | `ClientBuildUpgradeGate` blocking modal |

## Security notes for contributors

- Never log access/refresh tokens.
- Do not send `Idempotency-Key` on non-allowlisted paths (API returns `400 IDEMPOTENCY_SCOPE`).
- Password and account deletion bypass outbox — always hit network with fresh auth.
- Support form redacts secrets client-side before POST (server also redacts).

## API doc cross-reference

| Client concern | API doc |
| :--- | :--- |
| Endpoint paths & envelopes | [01_Endpoints_and_Contracts](../api_docs/01_Endpoints_and_Contracts.md) |
| JWT, axes, PWA middleware | [05_Authentication_and_Security](../api_docs/05_Authentication_and_Security.md) |
| Business invariants (signs, `unknown` source) | [03_Business_Rules_and_Invariants](../api_docs/03_Business_Rules_and_Invariants.md) |
| Snapshot recompute rules | [04_Business_Logic_Reference](../api_docs/04_Business_Logic_Reference.md) |

---

**[Return to Overview](00_Web_Overview.md)** · **Next:** [Pages & Features](06_Pages_and_Features.md)
