# State & Data

Related: [Application Architecture](02_Application_Architecture.md) · [API Client & Auth](05_API_Client_and_Auth.md)

The web client uses **three separate stores** for server data, session identity, and offline persistence. Confusing them causes subtle offline bugs.

## 1. TanStack Query (server cache)

Config: [`src/lib/queryClient.ts`](../../finance_manager_web/src/lib/queryClient.ts).

| Default | Value | Why |
| :--- | :--- | :--- |
| `staleTime` | 30s | Balance freshness vs request churn |
| `gcTime` | 10 min | Keep inactive cache for back-navigation |
| `retry` | 1 | Fast failure; user can retry explicitly |
| `refetchOnWindowFocus` | false | Avoid surprise refetch on tab focus |
| **`networkMode`** | **`"always"`** | **Critical** — lets `queryFn` run when `navigator.onLine === false` so Dexie cache-first paths execute |

Without `networkMode: "always"`, React Query pauses queries offline and the offline engine never runs.

### Standard query keys

Use these tuples consistently (invalidate subsets after mutations):

| Query key | Data |
| :--- | :--- |
| `["snapshot"]` | Dashboard `FinancialSnapshot` |
| `["app-profile"]` | Raw profile settings |
| `["profile"]` | Profile alias used in some screens |
| `["transactions", …filters]` | Transaction list |
| `["transactions-calendar", …]` | Calendar heat aggregates |
| `["transactions-viz", …]` | Deep-dive chart packet |
| `["sources", "all"]` | Payment sources |
| `["categories", "all"]` | Categories |
| `["tags", "all"]` | Tags |
| `["upcoming-expenses"]` | Bills list |
| `["balance-history", …]` | F-001 series |
| `["goals"]` | Savings goals |

Mutation success or offline queue events should invalidate affected keys (see `client.ts` adapter invalidation list).

### Read strategies (in `api/*` modules)

| Condition | Strategy |
| :--- | :--- |
| Offline / unreachable (`preferOfflineCaches()`) | Read Dexie → apply outbox overlay → optional rebuild from cached lists |
| Installed PWA online (`preferPwaLocalFirstReads()`) | Serve cache first; background revalidate if stale (`pwaLocalFirstBg.ts`, 30s) |
| Browser online (default) | Network-first; write-through to Dexie on success |
| Post-mutation bypass | `pwaReadBypass.ts` 8s window or `meta.forceNetwork` on query |

**Authoritative server KPIs:** `safe_to_spend`, `total_remaining_expenses`, and related snapshot fields are **not** fully recomputed client-side — offline UI merges outbox into reads but may show stale snapshot until drain + refetch.

## 2. Session state (`state/`)

### Token storage — [`state/auth.ts`](../../finance_manager_web/src/state/auth.ts)

| Key | Storage |
| :--- | :--- |
| `fm_access_token` | localStorage |
| `fm_refresh_token` | localStorage |

- JWT expiry parsed client-side with 30s skew.
- `hasOfflineSession()` — refresh token present ⇒ treat as signed in on cold offline reload (avoids bouncing to `/login` without network).

### React context — [`state/SessionContext.tsx`](../../finance_manager_web/src/state/SessionContext.tsx)

`useSession()` → `{ isAuthenticated, accessToken, logout }`.

- Implemented with `useSyncExternalStore` over localStorage + custom `fm-auth-changed` event.
- **Cross-tab logout** — storage event clears session consistently.

`logout()` clears tokens, query cache, and redirects as needed.

## 3. Onboarding flags — [`state/onboarding.ts`](../../finance_manager_web/src/state/onboarding.ts)

| Marker | Purpose |
| :--- | :--- |
| `fm_onboarding_active_v1` | New-user wizard in progress |
| Step completion | Derived from profile + local step state |

`RequireAuth` redirects incomplete onboarding to `/app/onboarding/*`. **Only set on new signup** — existing users skip wizard on login.

## 4. Offline store (Dexie)

Database: `finance_manager_offline` v1 ([`offline/db.ts`](../../finance_manager_web/src/offline/db.ts)).

| Table | Contents |
| :--- | :--- |
| `outbox` | Pending mutating requests (`method`, `url`, `body`, `idempotencyKey`, `createdAt`) |
| `caches` | Serialized GET payloads keyed by stable param hash |
| `meta` | Seed markers (`offline_seed_v4`, etc.) |

### Outbox lifecycle

1. **Enqueue** — Axios adapter when `shouldQueueOfflineWrite(config)` (offline, unreachable, or retroactive network failure on allowlisted path).
2. **Synthetic response** — `202 { offline_queued: true, idempotency_key }` to caller.
3. **Overlay reads** — `*OutboxOverlay.ts` merges pending rows so UI matches queued state (synthetic tx ids `pending:<key>[:index]`).
4. **Drain** — `drainOutbox()` on reachability recovery (FIFO); replays with stored `Idempotency-Key`; removes on success; handles 401 / 409 / 4xx / 5xx per [PWA doc](04_PWA_Offline_Sync.md).

### Events

| Event | Consumers |
| :--- | :--- |
| `fm-offline-queued` | Sync UI, query invalidation |
| `fm-sync-state` (`syncEvents.ts`) | Progress overlays |
| `fm-sw-need-refresh` | `SwUpdateBanner` |

## Form state

Route-level forms use **react-hook-form** + **zod** via `components/Form/`. Form state is local to pages/modals — not global context.

## Theming

[`lib/theme.ts`](../../finance_manager_web/src/lib/theme.ts) — `data-theme` on `document.documentElement`, persisted in localStorage; initialized in `main.tsx` before paint.

## Locale

[`lib/i18n.ts`](../../finance_manager_web/src/lib/i18n.ts) — in-file catalogs (`en-US`, `tl-PH`); `tr()` / `trFmt()` helpers. Not react-i18next — lightweight custom catalog (see [08_i18n](08_i18n_Theme_and_Guided_Tours.md)).

## Anti-patterns

| Don't | Do instead |
| :--- | :--- |
| `fetch()` in components | `api/*` + `useQuery` |
| `useState` for server lists | TanStack Query |
| Duplicate outbox merge logic in pages | Extend overlay module |
| Gate queries on `navigator.onLine` alone | Use connectivity probe + cache-first `queryFn` |
| Store secrets in Dexie | Tokens only in localStorage via `auth.ts` |

---

**[Return to Overview](00_Web_Overview.md)** · **Next:** [PWA & Offline Sync](04_PWA_Offline_Sync.md)
