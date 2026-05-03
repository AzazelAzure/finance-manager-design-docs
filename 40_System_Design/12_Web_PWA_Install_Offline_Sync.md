# Web PWA — install, offline sync, and API alignment (flagship)

## Purpose

Summarize where the **finance_manager_web** flagship implements **installable PWA**, **service worker** caching, **offline reads**, and **outbox replay** so Android and ops docs can cross-link without duplicating the full contract.

## Canonical specification (do not fork here)

Detailed decisions (**D0–D4**, browser matrix, idempotency, `X-Client-Build`, auth/offline, seeding window, D4 smoke) live in the ecosystem parent repository under:

`plans/cursor/s1b/pwa-install-offline-sync-research/`

This submodule intentionally **links** to that tree rather than copying prose, so research lock and sprint edits stay single-sourced.

## Implementation map (web repo)

| Concern | Location |
| -------- | -------- |
| Manifest + icons | `public/manifest.webmanifest`, `public/pwa-*.png` |
| Service worker generation | `vite-plugin-pwa` in `vite.config.ts` (network-first navigations, precache hashed assets) |
| Registration + update UX | `src/registerPwa.ts`, `src/components/SwUpdateBanner.tsx` |
| IndexedDB + outbox | `src/offline/*` |
| Mutating API adapter (queue when offline) | `src/api/client.ts` |
| Operator smoke (origins, post-switch) | `finance_manager_web/docs/BLUEGREEN_INSTALLED_PWA_SMOKE.md` (parent repo) |

### Offline parity (ledger-adjacent surfaces)

Beyond transactions, the web client merges **FIFO outbox** rows into local reads for **categories, tags, sources** (`lookupsOutboxOverlay.ts` → `api/lookups.ts`), **upcoming expenses** (`upcomingOutboxOverlay.ts` → `api/upcomingExpenses.ts` + unpaid names via `listUpcomingExpenses`), **app profile** (`profileOutboxOverlay.ts` → `api/profile.ts`), and extends **snapshot `source_balances`** with queued source creates/renames/deletes. **Online** allowlisted mutators also receive **`Idempotency-Key`** from the Axios request interceptor (`api/client.ts`) so retries do not duplicate rows. See parent sprint notes `plans/cursor/s1b/pwa-implementation-branch/` and research `plans/cursor/s1b/pwa-install-offline-sync-research/D2_API_OUTBOX_CONTRACT.md`.

## API parity

Write contract and health fields are implemented in **`finance_manager_web`**’s sibling repo **`finance_manager_api`** (middleware, idempotency store, optional `CLIENT_BUILD_MIN_WRITE`). See API `CHANGELOG.md` and `README.md` env table.

## Related documents

- [08_Android_Offline_First_Sync_Architecture](./08_Android_Offline_First_Sync_Architecture.md) — native Android sync; same API authority and idempotency expectations.  
- S1.B sprint index: `plans/cursor/s1b/README.md` (parent repo), anchor **PWA sprint activation**.
