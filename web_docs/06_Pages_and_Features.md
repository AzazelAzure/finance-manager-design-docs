# Pages & Features

Related: [Routing](01_Routing_and_Navigation.md) · [State & Data](03_State_and_Data.md)

Feature-by-feature map of authenticated and public surfaces. Implementation paths are under `src/pages/` unless noted.

---

## Public marketing & auth

### Landing (`pages/LandingPage.tsx`)

- Hero, feature showcase, roadmap/version sections (`components/landing/`).
- `react-helmet-async` for localized SEO title/description.
- Theme toggle on public shell.

### Login / Signup (`LoginPage.tsx`, `SignupPage.tsx`)

- JWT obtain on login; registration with ToS fields on signup.
- Authenticated users redirected to dashboard (no interstitial).
- Helmet metadata for `/login` and `/signup`.

### Legal (`pages/legal/*`)

- Privacy, Terms, Cookies — markdown via `react-markdown` + `remark-gfm`.
- `LegalPageShell`; **`noindex`** robots meta.

---

## Dashboard (`pages/dashboard/DashboardPage.tsx`)

**Data:** `useQuery(["snapshot"])`, balance history, transaction aggregates, widgets.

| Widget / section | Source |
| :--- | :--- |
| Safe-to-spend hero | Snapshot `safe_to_spend` |
| KPI row | Snapshot totals |
| Balance history chart | `balance-history` query (7d/30d/90d/all) |
| Flow / spend / category / tag charts | Snapshot + viz helpers |
| Source balances | Snapshot `source_balances` (hides **`unknown`**) |
| Recent transactions | Transaction query (limited) |
| Goals widget | `goals` query |
| Upcoming bills widget | `upcoming-expenses` query |

Filters: `lib/dashboardQueryParams.ts` (synced to URL where applicable).

**Invariant:** internal `unknown` source never shown in legends or user-facing balance trends.

---

## Transactions (`pages/transactions/`)

### List (`TransactionsPage.tsx`)

- Filter by month/year/source/category/tag.
- Quick add (`/app/transactions/new`) opens modal form.
- Inline edit modal; bulk-friendly patterns.
- Transfer entry: paired `XFER_OUT` + `XFER_IN` submissions.

### Calendar (`CalendarPage.tsx`)

- `GET /finance/transactions/calendar/` with granularity.
- Offline: `calendarOffline.ts` + seeded cache.
- Due events overlay from upcoming expenses.

### Deep dive (`DeepDivePage.tsx`)

- Visualization packet from `/finance/transactions/visualization/`.
- Offline: `visualizationOffline.ts`.

**Tours:** page-level Joyride; modal-form auto-start tours **disabled** (see [08](08_i18n_Theme_and_Guided_Tours.md)).

---

## Upcoming expenses / bills (`pages/upcoming/`)

### List (`UpcomingExpensesPage.tsx`)

- Bill CRUD modals; cadence selector (`lib/billCadence.ts`).
- `bill_class` rigid vs volatile; partial payment fields (F-004).
- Pay-cycle-aware quick filters when profile uses pay-cycle STS.

### Deep dive (`UpcomingDeepDivePage.tsx`)

- Analytics view on bill obligations.

**Catch-up:** API `POST .../catch-up/` exposed where UI offers overdue advance (max 24 periods server-side).

---

## Goals (`pages/goals/GoalsPage.tsx`) — F-005

- List/create/edit/delete savings goals.
- Progress vs `target_amount`; `per_cycle_required` from API.
- Offline: read from cache; **writes require network**.

---

## Data Hub (`pages/data/DataHubPage.tsx`)

Tabbed management surface:

| Tab | Function |
| :--- | :--- |
| Overview | KPI-style summary |
| Sources | CRUD payment sources |
| Categories | CRUD categories |
| Tags | Tag list management |

**Export:**

| Export | Endpoint |
| :--- | :--- |
| CSV transactions | `GET /finance/export/transactions/csv/` |
| Full JSON backup | `GET /finance/export/full/` |

Export actions gated offline. **Share-link UI removed** 2026-06-29 (F-010 privacy).

---

## Settings & profile (`pages/settings/SettingsProfilePage.tsx`)

| Section | API |
| :--- | :--- |
| Base currency, timezone, week start | `PATCH /finance/appprofile/` |
| STS window (`calendar_month` vs `pay_cycle`) | Profile pay-cycle fields |
| Spend accounts selection | `spend_accounts` JSON list |
| Data export shortcuts | Links to export endpoints |
| Password change | `PATCH /finance/user/` |
| Account deletion | `DELETE /finance/user/` (online-only) |
| Re-run setup wizard | Sets onboarding marker locally |

**Rules of Hooks:** effects that power tours must run **above** loading/error early returns (Profile tab black-screen fix on `main`).

---

## Support (`pages/support/SupportPage.tsx`)

- Bug report + feature request forms → `POST /finance/support/tickets/`.
- Feature requests hidden when `feature_requests_enabled` false on profile.
- Diagnostic log key field removed from UI (operator-side F-013 logs).

Legacy `POST /finance/bug-report/` exists on API but UI prefers ticket queue.

---

## Onboarding wizard (`pages/onboarding/OnboardingPage.tsx`)

| Step | Route | Content |
| :--- | :--- | :--- |
| 1 | `/app/onboarding` | Base currency |
| 2 | `/app/onboarding/sources` | First payment source |
| 3 | `/app/onboarding/categories` | Starter categories |
| 4 | `/app/onboarding/review` | Confirm |

Triggered only for **new signups** (`fm_onboarding_active_v1`). Existing users land on dashboard.

---

## Cross-cutting UI

| Concern | Location |
| :--- | :--- |
| Modals | `components/ui/Modal.tsx` |
| Loading / empty / error | `*State.tsx` components |
| Money display | `lib/money.ts` + locale formatters |
| Source picker | `components/transactions/SourceSelect.tsx` |
| Cookie consent | `CookieBanner.tsx` |
| SW update | `SwUpdateBanner.tsx` |

---

## Feature → API quick map

| UI feature | Primary endpoints |
| :--- | :--- |
| Dashboard snapshot | `GET /finance/appprofile/snapshot/` |
| Transaction CRUD | `/finance/transactions/` |
| Bills | `/finance/upcoming_expenses/` |
| Lookups | `/finance/sources/`, `/finance/categories/`, `/finance/tags/` |
| Goals | `/finance/savings-goals/` |
| Balance chart | `/finance/balance-history/` |
| Exchange rates (offline) | `/finance/exchange_rates/` |
| Support | `/finance/support/tickets/` |

---

**[Return to Overview](00_Web_Overview.md)** · **Next:** [Build, Deploy & Testing](07_Build_Deploy_and_Testing.md)
