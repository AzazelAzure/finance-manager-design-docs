# i18n, Theme & Guided Tours

Related: [Pages & Features](06_Pages_and_Features.md) · [Routing](01_Routing_and_Navigation.md)

UX systems that span all routes: localization, light/dark theme, onboarding tours (F-007), and contextual help mode.

## Internationalization (`lib/i18n.ts`)

**Approach:** lightweight in-repo catalog — **not** react-i18next.

| Locale | Code | Coverage |
| :--- | :--- | :--- |
| English (US) | `en-US` | Primary / complete |
| Tagalog (Philippines) | `tl-PH` | Partial parity; expand with feature work |

### API

| Function | Purpose |
| :--- | :--- |
| `getLocale()` | Active locale (storage + browser fallback) |
| `setLocale(code)` | Persist user choice |
| `tr(key)` | String lookup |
| `trFmt(key, vars)` | Interpolation |

Message keys are flat namespaced strings (`dashboard.sts.label`, `tour.transactions.step1`, etc.).

### SEO strings

Public routes use dedicated keys:

- `landing.seo.title` / `landing.seo.desc`
- Login and signup Helmet keys (added with public-page SEO pass)

**Contributor rule:** new user-visible strings require **both** locales when merging feature work (workspace Definition of Done includes i18n).

---

## Theme (`lib/theme.ts`)

| Concern | Implementation |
| :--- | :--- |
| Modes | `light` / `dark` via `data-theme` on `<html>` |
| Persistence | localStorage |
| Init | `initDataThemeFromStorage()` in `main.tsx` before render |
| Toggle | `ThemeToggle` in public + protected shells |

Design tokens live in CSS (`src/styles/tokens.css` imported from `index.css`). Prefer tokens over hard-coded colors in new components.

Charts (Recharts) inherit theme colors from CSS variables where possible.

---

## Guided tours (F-007)

Stack: **`react-joyride`** inside [`TourProvider`](../../finance_manager_web/src/components/tours/TourProvider.tsx).

### Persistence

Completed tour ids saved to **`AppProfile.completed_tours`** (JSON list) via profile PATCH — syncs across devices when online.

### Tour types

| Type | Trigger | Status |
| :--- | :--- | :--- |
| **Welcome modal** | First dashboard visit | Active |
| **Page tours** | Auto after data loads on major routes | Active |
| **Modal-form tours** | Quick-add / tx editor / bill editor | **Disabled** — Joyride spotlight renders behind modal z-index |
| **Help mode** | User toggles help in shell | Active — `HelpModeWrapper` contextual notes per field |

### Step definitions

Co-located files under `components/tours/`:

- `*TourSteps.ts` per route (dashboard, transactions, upcoming, data hub, settings, …)
- Step `target` uses `data-tour` attributes on DOM nodes — add attributes when introducing new UI.

### Product rules (from workspace invariants)

- Extend guided tour / help mode to **every new feature, view, and form** (including quick-add modals) as part of done.
- Modal-form auto-start remains off until z-index/stacking fix lands (parent anomaly may exist — do not re-enable without visual QA).

### Implementation checklist for new surfaces

1. Add `data-tour="..."` hooks to key elements.
2. Create `SomethingTourSteps.ts` with Joyride steps + i18n strings.
3. Register in `TourProvider` route map.
4. Add help-mode snippets via `HelpModeWrapper`.
5. Update `completed_tours` id list documentation in tour config.

---

## Motion

[`motion/react`](../../finance_manager_web/package.json) `LazyMotion` + `domAnimation` in `main.tsx` — tree-shaken animations for landing/marketing components. Keep motion optional on hot paths to protect INP.

---

## Accessibility notes

- Joyride steps should include readable `content` strings (localized).
- Modal focus trap handled by `Modal` component — conflicts with Joyride spotlight motivated disabling modal-form tours.
- Error boundary recovery actions must be keyboard reachable (`ErrorBoundary` on `main`).

---

## Cookies & consent

[`CookieBanner.tsx`](../../finance_manager_web/src/components/CookieBanner.tsx) — consent gate for analytics/cookies per legal pages (`/cookies`).

---

## Cross-references

| Topic | Doc |
| :--- | :--- |
| Onboarding wizard (setup, not Joyride) | [Routing § Onboarding](01_Routing_and_Navigation.md) |
| Profile `completed_tours` field | [API Models § AppProfile](../api_docs/03_Models_Reference.md) |
| Public SEO pages | [Pages § Public](06_Pages_and_Features.md) |

---

**[Return to Overview](00_Web_Overview.md)**
