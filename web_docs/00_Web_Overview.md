# Finance Manager Web Documentation

Welcome to the **flagship web client** documentation. This vault section is the onboarding guide and reference for `finance_manager_web` — the React 19 + TypeScript + Vite 8 SPA that ships as an installable PWA.

**Implementation home:** `finance_manager_web/` (`package.json`, `src/`, `public/`).

## Quick start for new contributors

1. **Clone & run** — see [`finance_manager_web/README.md`](../../finance_manager_web/README.md) for `npm ci`, `.env.local` (`VITE_API_BASE_URL`), and `npm run dev` (port 5173).
2. **Read architecture** — [Application Architecture](02_Application_Architecture.md) (provider tree, folder layout, data layers).
3. **Learn routing** — [Routing & Navigation](01_Routing_and_Navigation.md) (public vs `/app` shell, auth guards).
4. **State & offline** — [State & Data](03_State_and_Data.md) + [PWA & Offline Sync](04_PWA_Offline_Sync.md).
5. **API wiring** — [API Client & Auth](05_API_Client_and_Auth.md) (Axios, JWT, idempotency, `X-Client-Build`).
6. **Feature surfaces** — [Pages & Features](06_Pages_and_Features.md).
7. **Ship & verify** — [Build, Deploy & Testing](07_Build_Deploy_and_Testing.md).

Run tests: `npm test` (Vitest). CI: `tsc -b` + Vitest on Node 22 (`.github/workflows/web-ci.yml`).

## Documentation map

| Doc | Purpose |
| :--- | :--- |
| [02_Application_Architecture](02_Application_Architecture.md) | `src/` layout, provider tree, conventions |
| [01_Routing_and_Navigation](01_Routing_and_Navigation.md) | Routes, shells, auth redirects, onboarding gate |
| [03_State_and_Data](03_State_and_Data.md) | TanStack Query, session context, query keys |
| [04_PWA_Offline_Sync](04_PWA_Offline_Sync.md) | Dexie outbox, overlays, seed, sync UX (links system design) |
| [05_API_Client_and_Auth](05_API_Client_and_Auth.md) | Axios adapter, tokens, headers, error handling |
| [06_Pages_and_Features](06_Pages_and_Features.md) | Dashboard, ledger, bills, goals, settings, support |
| [07_Build_Deploy_and_Testing](07_Build_Deploy_and_Testing.md) | Vite build, Docker/nginx, env, CI, hostnames |
| [08_i18n_Theme_and_Guided_Tours](08_i18n_Theme_and_Guided_Tours.md) | Locales, theming, Joyride tours, help mode |

## What this client is

| Trait | Detail |
| :--- | :--- |
| **Role** | Primary user-facing client (Tight Beta); CLI is secondary; Reflex is **archived** |
| **Rendering** | Client-side SPA; public marketing/auth pages + authenticated `/app/*` shell |
| **Server state** | TanStack Query with `networkMode: "always"` (offline handled in Axios + Dexie, not RQ pause) |
| **Offline** | IndexedDB (Dexie) outbox + read caches; FIFO replay with API idempotency contract |
| **API prefix** | Finance calls use `/finance/...`; auth uses `/api/token/...` — see [api_docs](../api_docs/00_API_Overview.md) |

## Provider tree (boot order)

From [`src/main.tsx`](../../finance_manager_web/src/main.tsx) on `main`:

```
StrictMode
  QueryClientProvider
    SessionProvider
      HelmetProvider
        BrowserRouter
          LazyMotion
            SwUpdateBanner
            OfflineRoot
            ErrorBoundary          ← production main (PR #97)
              App
```

`App` mounts sync chrome (`SyncStatusBar` / `SyncProgressOverlay`), `ClientBuildUpgradeGate`, `CookieBanner`, and the route table.

## Production hostnames

| Surface | Host |
| :--- | :--- |
| Web (production) | `thehivemanager.com` |
| API (production) | `api.thehivemanager.com` |
| Web (staging / jsdevtesting) | `jsdevtesting.thehivemanager.com` |
| API (staging) | `api-jsdevtesting.thehivemanager.com` |

Public HTTPS terminates at the ecosystem blue-green proxy on host **`:8443`** in VPS deploys. API base URL is **baked at build time** via `VITE_API_BASE_URL`.

## Cross-vault links

| Topic | Vault path |
| :--- | :--- |
| API contracts & PWA write middleware | [`api_docs/`](../api_docs/00_API_Overview.md) |
| PWA system-design summary | [`40_System_Design/12_Web_PWA_Install_Offline_Sync.md`](../40_System_Design/12_Web_PWA_Install_Offline_Sync.md) |
| Android offline sibling | [`40_System_Design/08_Android_Offline_First_Sync_Architecture.md`](../40_System_Design/08_Android_Offline_First_Sync_Architecture.md) |
| Deployment topology | [`40_System_Design/05_Deployment_Strategy.md`](../40_System_Design/05_Deployment_Strategy.md) |

## Parent workspace (private — not wikilinked)

When the full ecosystem is cloned locally:

| Topic | Path |
| :--- | :--- |
| Runtime port ownership | `governance/Runtime_Signup_Sheet.md` |
| Deploy / color flip protocol | `governance/deployment_protocol.md` |
| Blue-green switchover notes | `deploy/BLUEGREEN_SWITCHOVER.md` (parent monorepo) |

Do **not** reference removed public vault paths (`design_docs/30_Releases/`, `design_docs/20_Roadmap/`) — those moved to the private parent during the 2026-06-30 scope cleanup.

## Recent orientation (tight beta)

| Area | Status |
| :--- | :--- |
| F-010 export share UI | **Removed** 2026-06-29; CSV + JSON backup retained in Data Hub |
| Bill cadence UI | `lib/billCadence.ts` mirrors API cadence engine |
| Balance history widget | Dashboard 7d/30d/90d/all via `/finance/balance-history/` |
| Savings goals | `/app/goals` CRUD (offline read cache; writes require network) |
| ErrorBoundary | App root + route-level recovery on `main` |
| Modal-form Joyride auto-tours | **Disabled** (spotlight behind modal); page tours + help mode remain |
| SEO public routes | `react-helmet-async` on landing/login/signup; legal pages `noindex` |

---

**Next:** [Application Architecture](02_Application_Architecture.md)
