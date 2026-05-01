# Beta Launch Cutline and Dashboard-Centric Transition

> **Canonical context (2026-05-01):** Phase/stage vocabulary and execution live in `plans/cursor/strategic-roadmap-reframe-53be/` and `plans/_governance/`. This document describes the **invite-beta cutline** and **dashboard IA** intent in product language; it does **not** override the strategic plan or `plan_registry.md`.

## Purpose

Define what was required to reach a **server-ready, invite-only tight beta** versus what is intentionally **post-launch / S1.B+** quality work. The flagship GUI is **`finance_manager_web`** (React/Vite). **`finance_manager_reflex` is archived** (2026-04-30) and must not appear as an execution dependency.

For owner lanes, legacy `BETA-P0` IDs, and cycle history, see [Beta Execution Board](Beta_Execution_Board.md) (superseded for *new* task IDs by `plans/cursor/s1b/`).

## Scope

- **Near-term:** stable **VPS blue-green** stack (API + **web** + proxy on **:8443**), reliable core flows, API-first sequencing.
- **UX direction:** pseudo–single-page **dashboard** with deep workflows on subpages (calendar, upcoming expenses, settings).
- **Constraint:** no heavy client-side aggregation; **API owns math** and snapshot payloads.

## Phase A: Beta launch readiness (historical “Necessary Right Now”)

- **Goal:** Deployable, supportable **invite beta** baseline on hosted infrastructure.
- **Entry (historical):** Alpha-era core flows working locally; pivot to **web** complete.
- **Exit:** Hosted test/live workflow stable; auth baseline in place; MVP journeys reliable on **web**; reproducible deploy + smoke path.
- **Breakpoints:** OAuth/token gaps; unstable deploy; critical **web** dashboard defects; API/CLI/**web** contract drift.
- **Dependencies:** API auth/logging/bug-report paths; infra + blue-green; cross-repo contract matrix ([Beta Contract Compatibility Matrix](Beta_Contract_Compatibility_Matrix.md)).
- **Verification:** End-to-end on target runtime URL (prefer **:8443** checklist in `10_Current_State/01_Runtime_Validation_Checklist.md`); no critical defects in auth, transactions, snapshot/dashboard.

### Necessary-right-now checklist (web-era)

1. **Hosting:** VPS (or equivalent) runs **api-blue/green**, **web-blue/green**, **proxy**, Postgres; no accidental mixed local+container ownership (see `30_Releases/Runtime_Signup_Sheet.md`).
2. **Security / auth:** OAuth + JWT rotation/blacklist per API roadmap; user-attributable logging where required.
3. **Operations:** Bug-report / support path configured for hosted env.
4. **Contracts:** API + CLI + **web** compatibility for beta-critical surfaces (matrix above).
5. **Web UX baseline:** Critical dashboard and CRUD flows stable on mobile and desktop viewports where in scope.
6. **Deploy:** Scripted or CI-driven path; runtime bundle flow documented where used (`deploy/SERVER_BETA_INSTALL.md`).

**Distribution note (S1.B):** scaling invites remains **blocked** until **S0** items in `plans/cursor/s1b/drift-cleanup/` close (e.g. email uniqueness).

## Phase B: Dashboard-centric consolidation (post–tight-beta QoL)

- **Goal:** Improve information architecture and polish **without** destabilizing the live color.
- **Entry:** S1.B drift cleanup on track; no open **S0** regressions for distribution.
- **Exit:** Clearer dashboard vs subpage split; core transaction + snapshot flows unchanged or improved.
- **Dependencies:** Stable API contracts for tiles and subpages; feedback from beta cohort.
- **Implementation:** **`finance_manager_web`** routing/state and layout; API summaries as needed.
- **Verification:** UX acceptance unchanged or better; no regression in core flows.

### Post-launch QoL backlog (explicitly deferred)

- Full visual redesign beyond usability-critical fixes.
- Non-essential animation and deep personalization.
- Exploratory visualizations not required for the wedge (safe-to-spend, core CRUD).

## Pseudo–single-page transition strategy

1. **On dashboard:** quick entry, snapshot summary, primary account/transaction actions.
2. **On subpages:** large tables, advanced analytics, heavy configuration.
3. **Progressive disclosure:** dashboard summarizes; subpages hold depth.
4. **API-first:** any new aggregate or snapshot field is **API-defined and tested** (CLI where applicable) **before** **web** UI work.

## Execution triggers

- Core-flow defect from beta checklist → immediate fix queue (severity per `plans/_governance/glossary.md`).
- Enhancement that does not affect invite-beta cutline → S1.B backlog / sub-plan.
- New dashboard data need → API contract task first, then **web**.
- Runtime testing → `Runtime_Signup_Sheet.md` + `Runtime_Owner_Handoff_Template.md` + `plans/_governance/execution_protocols.md` gates as applicable.
