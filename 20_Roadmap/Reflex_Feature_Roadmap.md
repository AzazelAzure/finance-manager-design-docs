# Reflex Feature Roadmap (Path to v1.0)

> **ARCHIVED PRODUCT (2026-04-30).** Reflex is no longer in production; **`finance_manager_web`** is the flagship GUI. Keep this file as **historical UX/parity reference** only. Current sequencing: `plans/cursor/strategic-roadmap-reframe-53be/`.

**Context (historical):** The API and CLI are considered v1.0. This roadmap tracked bringing the Reflex frontend toward v1.0; that effort was superseded by the JS pivot documented in the strategic plan.
**Roadmap semantics:** Each phase is a substantial frontend program spanning multiple epics, validation passes, and UX iterations. A phase is complete only when its outcome criteria are satisfied.

## 🧭 Core Design Philosophy

**The Frontend Must Be Lean:** The Reflex application should be as lightweight as possible. It is responsible for routing, state synchronization, and rendering. **All heavy lifting, math, and data aggregation MUST be offloaded to the API (and eventually Rust middleware).** 

---

## 📌 Phase 1: Alpha Stabilization (Intuitive Input MVP - Completed)

**Goal:** Get the app to a "Ready for User Testing" state where core CRUD operations are frictionless, aiming for a hosted Beta launch within one month.

- **Quick Entry:** Develop a highly intuitive, keyboard-friendly transaction input form.
- **Digestible Views:** Ensure the return packets (Financial Snapshots) are rendered in a clean, easily digestible dashboard.
- **Onboarding & Education:** 
  - Smooth profile and account setup flow via an **Onboarding Wizard**.
  - Provide basic tutorials during onboarding.
  - Ensure every feature (existing and new) includes a built-in tutorial/explainer so users understand its utility and mechanics (no "good luck" features).

## 📌 Phase 2: Beta Preparation (Advanced Visualizations - Target v1.0)

**Goal:** Implement the complex data views that make a GUI superior to a CLI.

- **Graphing:** Implement charting components (e.g., spending trends, asset growth over time).
- **Transaction Calendar View:** 
  - A full calendar interface displaying up-to-date daily transaction totals.
  - **Interactivity:** Clicking a specific day pulls up a detailed view of all transactions and information for that date.
  - **Aggregations:** Display weekly totals at the end of each row and a grand monthly total on the view. *(Note: The math for these aggregations will be handled by the API, not Reflex).*
- **Dashboard-Centric UX Transition:** Move toward a pseudo-single-page model where core actions remain on dashboard and advanced manipulation flows move to dedicated subpages. Prioritize only launch-critical UX fixes for Beta and defer non-critical redesign work.
- **Execution Priority Note:** In this phase, calendar and transaction/upcoming-expense visualizations are prioritized before broader post-beta UX expansion.

## 📌 Post-Beta: Localization & Internationalization

**Goal:** Expand accessibility beyond the initial US/PH beta cohort to accommodate non-tech-literate and international users.

- **i18n Implementation:** Support multiple languages (e.g., English, Tagalog) and locale-specific formatting (currency, dates).
- **Accessibility (a11y):** Ensure the UI/UX is robust for power users but simple and accessible enough for the least tech-savvy users.

## 📌 Phase 3: Rust Middleware Integration (Performance)

**Goal:** Supercharge the UI responsiveness.

- **WASM / Rust Calculators:** As the Rust middleware is developed, integrate specialized calculators to speed up complex UI state calculations (if any remain on the client) or to handle rapid client-side encryption before payloads are sent to the API.
- **Dependency Note:** Rust integration milestones assume Rust repos are fully initialized only after Track B/Phase 3 gate opens and owner learning ramp is complete.