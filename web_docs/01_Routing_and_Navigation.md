# Routing & Navigation

Related: [Web Overview](00_Web_Overview.md) · [Application Architecture](02_Application_Architecture.md)

Route table lives in [`src/App.tsx`](../../finance_manager_web/src/App.tsx). React Router 7 (`react-router-dom`) with two layout shells and a single auth guard.

## Route map

### Public shell (`PublicShell`)

Marketing and auth pages — no sidebar, public header/footer.

| Path | Page | SEO |
| :--- | :--- | :--- |
| `/` | `LandingPage` | Helmet title/description + JSON-LD in `index.html` |
| `/login` | `LoginPage` | Helmet + canonical |
| `/signup` | `SignupPage` | Helmet + canonical |
| `/privacy` | `PrivacyPage` | Markdown legal; **`noindex`** |
| `/terms` | `TermsPage` | Markdown legal; **`noindex`** |
| `/cookies` | `CookiesPage` | Markdown legal; **`noindex`** |

`public/robots.txt` allows `/`, `/login`, `/signup`; disallows `/app/*`. `public/sitemap.xml` lists public entry routes.

### Protected shell (`/app/*`)

Wrapped by [`RequireAuth`](../../finance_manager_web/src/routes/RequireAuth.tsx) → [`TourProvider`](../../finance_manager_web/src/components/tours/TourProvider.tsx) → [`ProtectedShell`](../../finance_manager_web/src/layout/ProtectedShell.tsx).

| Path | Page | Notes |
| :--- | :--- | :--- |
| `/app` (index) | redirect → `/app/dashboard` | |
| `/app/dashboard` | `DashboardPage` | STS hero, KPIs, charts, widgets |
| `/app/transactions` | `TransactionsPage` | List + filters; editor modal |
| `/app/transactions/new` | `TransactionsPage` | Opens quick-add flow |
| `/app/transactions/calendar` | `CalendarPage` | Heatmap + due events |
| `/app/transactions/deep-dive` | `DeepDivePage` | Visualization charts |
| `/app/upcoming-expenses` | `UpcomingExpensesPage` | Bills list + editor |
| `/app/upcoming-expenses/deep-dive` | `UpcomingDeepDivePage` | Bill analytics |
| `/app/data` | `DataHubPage` | Sources/categories/tags + export |
| `/app/goals` | `GoalsPage` | Savings goals (F-005) |
| `/app/settings/profile` | `SettingsProfilePage` | Profile, pay cycle, password, delete |
| `/app/support` | `SupportPage` | Bug / feature tickets |
| `/app/onboarding` | `OnboardingPage` step 1 | Currency |
| `/app/onboarding/sources` | step 2 | First source |
| `/app/onboarding/categories` | step 3 | Categories |
| `/app/onboarding/review` | step 4 | Review |

### Wildcard

| Path | Behavior |
| :--- | :--- |
| `*` | Authenticated → `/app/dashboard`; else → `/` |

## Auth guard behavior

[`RequireAuth`](../../finance_manager_web/src/routes/RequireAuth.tsx):

1. **Unauthenticated** → redirect `/login` with `location.state.from` preserved.
2. **Onboarding active** (`state/onboarding.ts` + `fm_onboarding_active_v1` local marker) and incomplete steps → redirect to `earliestIncompleteOnboardingPath()`.
3. Otherwise render child routes.

### Product invariants (routing UX)

| Rule | Implementation |
| :--- | :--- |
| Already logged in on `/login` | Redirect straight to dashboard (no interstitial) |
| Onboarding wizard | Only for **new account** creation path — not existing users on login |
| Brand / home link while authenticated | Navigates to `/` without logging out; public header offers "Return to Dashboard" |

## Layout chrome

### `ProtectedShell`

- Desktop: fixed sidebar + scrollable main + sticky header.
- Tablet/mobile: bottom tab bar + "More" drawer (breakpoints in `lib/breakpoints.ts`).
- Theme toggle, help mode entry, logout.

### Sync chrome (`App.tsx` → `SyncChrome`)

| Mode | UI |
| :--- | :--- |
| Installed PWA (`isPwaStandaloneDisplay()`) | `SyncProgressOverlay` + `SyncStatusBar` |
| Browser tab on `/app/*` | `SyncStatusBar` only |
| Public routes (`/`, `/login`, …) | No sync overlay (avoids background sync on marketing pages) |

## Error recovery routing

On production `main`, [`ErrorBoundary`](../../finance_manager_web/src/components/ErrorBoundary.tsx) wraps `<App />` in `main.tsx` and may wrap authenticated route content — recoverable error UI (retry, reload, link to dashboard) instead of blank screen.

401 after failed token refresh → session cleared → `window.location.replace("/login")` (except on `/`, `/login`, `/signup`).

409 `CLIENT_BUILD_UNSUPPORTED` → `ClientBuildUpgradeGate` modal (not a route change).

## Deep linking & offline

- SPA fallback: service worker `navigateFallback: /index.html` (Vite PWA plugin) with denylist for `/api/*`.
- Offline installed PWA: client-side routes still resolve; data from Dexie caches + overlays until drain completes.

## Adding a new route (checklist)

1. Create page under `src/pages/<feature>/`.
2. Register route in `App.tsx` under correct shell (public vs `/app`).
3. Add nav link in `ProtectedShell` (and mobile drawer if needed).
4. If data-backed: add `api/` module + query keys + tests.
5. If mutating offline: extend `offline/allowlist.ts` and overlay merge if reads must reflect queued writes.
6. Extend guided tour steps if the surface is user-facing ledger (see [i18n, Theme & Tours](08_i18n_Theme_and_Guided_Tours.md)).
7. Update OpenAPI-aligned types in `api/types.ts` when API contract changes.

---

**[Return to Overview](00_Web_Overview.md)** · **Next:** [State & Data](03_State_and_Data.md)
