# Roadmap Overview

> **HISTORICAL REFERENCE ONLY (as of 2026-04-30).** The canonical roadmap is now `plans/cursor/strategic-roadmap-reframe-53be/`. The S1–S6 Phase model supersedes the `Phase 1`/`Phase 2`/Track A–E structure documented here. This file is preserved for historical context and should not be used as the source of truth for ongoing work.
>
> **Why retired:** The dual-persona "Average Joe vs Finance Bro" framing was a target list, not a strategy. Post-beta huddle 2026-04-30 reframed to PH-first wedge ("safe-to-spend for thin-margin households"), 4-Stage breakdown of S1, JS pivot completed (Reflex archived), per-product launch-state model, and revenue path narrowed (ads + user-data sales rejected). See `plans/cursor/strategic-roadmap-reframe-53be/00_strategic_context.md` §3 + §4 for full lock list.

This document outlines the high-level trajectory of the Finance Manager project, tracking its evolution from a local, loosely coupled Alpha state to a fully hosted, secure, and production-ready system.

## Terminology and Execution Model

- **Phase:** A large program increment made up of multiple epics, implementation plans, and validation loops. A phase is measured in weeks/months, not in a single chat turn or one commit.
- **Epic:** A major body of work inside a phase (for example, OAuth rollout, dashboard parity, CI/CD baseline).
- **Task:** A scoped implementation unit that can usually be completed in one focused work session.
- **Execution Rule:** We complete phases by iterating through many tasks and epics until exit criteria are met. Phases can overlap when dependencies permit.

## 🛑 Rigid Feature Dependencies (The Golden Rule)

To prevent the frontend and backend from drifting out of sync, **all development MUST follow a strict, rigid pipeline:**

1. **API First:** No UI or CLI feature can be developed until the underlying API endpoint and data structure are fully finalized and returning the exact required payload.
2. **CLI Verification:** Once the API is built, the feature MUST be integrated and tested via the CLI. The CLI acts as our baseline integration test.
3. **Reflex Implementation:** Only after the CLI verifies the API contract is the Reflex frontend allowed to implement the UI for the feature.

*If a feature requires a change in the data model, development halts on the frontend until the API and CLI are updated to match.*

---

## 📌 Phase 1: Alpha Stabilization (Current Focus)

**Goal:** Solidify the foundation, enforce reproducible builds, and establish a reliable local development workflow before scaling up.
**Scale:** Multi-epic foundational program (not a single delivery cycle).

- **Dependency Management:** Implement strict lockfiles (`uv.lock`) across all Python projects (API, CLI, Reflex) to prevent supply-chain drift.
- **Version Control & Git Tooling:** Define the repository structure (whether keeping it as a monorepo or splitting into a workspace with separate git histories). Develop bash scripts to streamline local Git workflows across the components.
- **Reflex MVP:** Wire the existing Reflex frontend to the Django API to achieve a Minimum Viable Product (MVP) state for the web GUI.

## 📌 Phase 2: Beta Preparation & Hosting Infrastructure

**Goal:** Transition from a purely local tool to a hosted application with distinct environments for testing and production.
**Scale:** Multi-epic hosting and operations program.

- **Server Provisioning:** Develop bash scripts and infrastructure plans for automatically provisioning a Live Server and a separate Test Server.
- **Blue-Green Deployment Strategy:** Implement a "High-Availability" architecture using duplicate Docker images (Runtime vs. Testing/Update). Use orchestration tools (Redis/Celery or Nginx) to divert traffic between versions while pointing to a shared database.
- **Server Runtime & Scaling:** Plan for regional deployment (US then PH), evaluate API/Frontend server splitting, and research DB edge-caching (Neon/Supabase) along with Agent-driven hotfixes. *See [Server Runtime & Scaling](file:///home/pproctor/Documents/python/finance_manager/design_docs/40_System_Design/07_Server_Runtime_and_Scaling.md) for details.*
- **Cross-Repo Contracts:** Formalize API schemas and compatibility matrices (e.g., CLI/Reflex must match API versions).
- **Deployment Pipelines:** Setup basic CI/CD to deploy the API, CLI packages, and Reflex frontend to their respective test environments.
- **Server-Side Logging:** Transition from local Loguru to a production-grade, per-user logging system. This must support critical bug reporting and direct user feedback loops (Email/Support tickets).
- **Beta Launch:** Achieving a stable, hosted environment ready for initial users with real data.

## 📌 Post-Beta: Dual-Track Development

Upon successful Beta launch, the project trajectory splits into two parallel, highly coordinated tracks:

### Track A: Feature Expansion (Frontend & UX)

- Building out the full suite of planned financial management features in the Reflex frontend.
- **Budgeting & Savings Goals:** Implement sophisticated budgeting tools and progress-tracking for long-term saving goals.
- **Credit Card Tracking:** Add specialized tracking for credit card balances, interest rates, and payoff strategies.
- **Localization:** Implement a global i18n system to support multiple languages and regional financial conventions (date formats, currency separators).
- **Advanced Upcoming Expenses:** Develop a robust system for handling recurring bills and cash-flow forecasting.
- Refining user flows, dashboard visualizations, and data entry mechanisms.
- Maintaining the "API-First" golden rule for all new capabilities.

### Track D: Mobile Development (Post-Beta)

- **Execution Gate:** Android implementation starts only after post-launch beta localization hardening is complete and shared locale/design assets are stable.
- **Mobile Roadmap:** Develop rollouts for Android and iOS versions to bring the Finance Manager ecosystem to mobile users.
- **Offline-First Sync:** Implement robust, atomic synchronization between local SQLite (mobile) and cloud PostgreSQL, ensuring data integrity across devices.
- **Native Integrations:** Utilize device-specific features (SMS parsing in PH wallets/messages, Biometrics) for automated transaction capture and security.
- **Legal & Compliance:** Tackle app store legal requirements, data privacy (GDPR/CCPA), and financial tool disclosures.
- **Repo Initialization Note:** Android repository scaffolding can exist locally, but full remote/release initialization is deferred until Track D execution gate is formally opened.

### Track E: Desktop Standalone Version

- **Portable Runtime:** Develop a standalone bundle containing both the Django API and the frontend, allowing for a fully local, single-binary experience on Linux, Windows, and macOS.
- **Self-Contained Data:** Enable users to run the entire stack locally with a local SQLite database, with optional sync to the cloud.

### Track C: Business & Monetization (SaaS)

- **SaaS Paymodel (Far Future):** Develop subscription infrastructure only after product-market stabilization, operational maturity, and legal/tax execution readiness.
- **User Personas:** Design the system to accommodate both the "Average Joe" (intuitive, simple overview) and the "Finance Bro" (deep-dive analytics, power-user features).
- **Cross-Border Legal/Tax Program:** Treat international monetization structure as a standalone strategic program due to multi-jurisdiction complexity.
- **License and Publication Coordination:** Tie commercialization and public-release boundaries to explicit gates. See [License and Release Gates](file:///home/pproctor/Documents/python/finance_manager/design_docs/20_Roadmap/License_and_Release_Gates.md).

### Track B: Phase 3 - Security Hardening (Long-Term)

- **Zero-Knowledge (ZK) Implementation:** Development of the Rust middleware (`finance_manager_crypto`) as a generic Django extension.
- **Security Parity:** Ensuring the encryption layer does not break existing features from Track A.
- **Live Data Migration:** Developing tools to seamlessly encrypt existing user data for current Beta/Production users.
- **Rust Repo Timing:** Dedicated Rust repositories (middleware and Rust tooling) are intentionally deferred from full remote initialization until Track B entry criteria are met.
- **Learning and Ownership:** Rust implementation is owner-led and hands-on; roadmap sequencing should reserve explicit learning/prototyping windows before production milestones.
- **Publication Strategy Inputs:** See [Licensing and Publication Strategy](file:///home/pproctor/Documents/python/finance_manager/design_docs/40_System_Design/09_Licensing_and_Publication_Strategy.md), [Bounty Sandbox Architecture](file:///home/pproctor/Documents/python/finance_manager/design_docs/40_System_Design/10_Bounty_Sandbox_Architecture.md), and [Security Disclosure and Bounty Program](file:///home/pproctor/Documents/python/finance_manager/design_docs/40_System_Design/11_Security_Disclosure_and_Bounty_Program.md).
- *Note: This track is a multi-year effort and is NOT required for the initial Beta launch.*

---

*Note: This roadmap overview provides the macro-level trajectory. For a detailed list of feature requirements and future capabilities, see the [Planned Features List](file:///home/pproctor/Documents/python/finance_manager/design_docs/20_Roadmap/Planned_Features.md).*
For execution gates, breakpoints, and trigger-driven phase transitions, see [Phase Execution Triggers and Breakpoints](file:///home/pproctor/Documents/python/finance_manager/design_docs/20_Roadmap/Phase_Execution_Triggers_and_Breakpoints.md).