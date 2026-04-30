# Phase 2: Beta Preparation & Hosting Infrastructure

> **HISTORICAL REFERENCE ONLY (as of 2026-04-30).** This document refers to the pre-huddle "Phase 2" structure (Beta Preparation + Hosting Infrastructure) and is superseded by the canonical Strategic Plan at `plans/cursor/strategic-roadmap-reframe-53be/`. The work described here was effectively completed during S1.A in the canonical model — beta launched 2026-04-29 on VPS with web flagship + blue-green proxy + API. The original "Phase 2" is now distributed across canonical Stages S1.A (completed), S1.B (research/cleanup, active), and S1.C (Founding Beta, pending).
>
> Preserved for context and audit trail; do not use as the source of truth for ongoing work.

**Status:** `Historical` (was `Planned` until 2026-04-30; effectively executed as canonical S1.A → entering S1.B at huddle close)
**Objective (historical):** Transition from a purely local tool to a hosted application with distinct environments for testing and production.
**Execution semantics:** This was a long-running program phase composed of multiple epics and rollout loops.

## Horizon Labels

- **[Now]** required for Phase 2 hosted beta readiness.
- **[Post-Launch Beta]** executed after initial beta launch while still in beta.
- **[Far Future]** deferred until substantially later strategic phases.

## Phase Workstreams

- Hosting and environment provisioning.
- Security/auth hardening.
- Deployment automation and contract governance.
- Regional/mobile readiness and beta feedback instrumentation.

## Step 1: Server Provisioning

- **[Now] Provision Live Server:** Set up the production infrastructure.
- **[Now] Provision Test Server:** Set up a sandbox environment to test Reflex containerization and API communication before pushing to Live.
- **[Now] Container Deployment:** Ensure `docker-compose` can successfully mount the `finance_manager_reflex` container alongside the API on the Test Server (resolving the local daemon blocker from Phase 1).

## Step 2: Backend Hardening (API)

- **[Now] OAuth Integration:** Implement full OAuth support to simplify user login.
- **[Now] Token Policies:** Enforce strict token blacklist timeframes and JWT rotation policies.
- **[Now] User-Tagged Logging:** Implement `loguru` to standardize logging and tag errors by user for safe multi-tenant debugging.
- **[Now] Email Service Integration:** Implement automated bug reports and feature requests piped to the admin's email.

## Step 3: CI/CD Pipelines

- **[Now] Automate Deployments:** Setup basic CI/CD (e.g., GitHub Actions or local deployment bash scripts) to securely deploy the API, CLI packages, and Reflex frontend to their respective test environments.
- **[Now] Cross-Repo Contracts:** Formalize API schemas and compatibility matrices to ensure the UI and CLI do not drift from the API version deployed.

## Step 4: PH Test Bed & Mobile Optimization

- **[Now] Mobile Responsiveness Audit:** Thoroughly test the Reflex frontend on mobile viewports to ensure all charts, tables, and forms are fully functional on small screens.
- **[Post-Launch Beta] PH Beta UX Validation (No New Currency Core Work):** Validate PH user experience, input flows, and formatting behavior in hosted beta. *(Currency support already exists in API and does not block Beta Preparation.)*
- **[Post-Launch Beta] Deferred Wallet SMS Ingestion Discovery:** Define the future architecture for optional SMS-based transaction ingestion (e.g., GCash/Maya message parsing). *(This is a post-launch feature and not a payment processing integration for Phase 2.)*
- **[Now] Beta Feedback Loop:** Deploy a specialized "Feedback Widget" to gather real-time input from the PH low-tech test bed.

## Explicit Deferrals (Out of Phase 2 Scope)

- **[Far Future]** Direct payment-system integrations and payment processing rails are deferred beyond current beta-launch work.
- **[Far Future]** SaaS monetization and subscription rails are deferred to a far-future business/legal phase after product and operations maturity.
- **[Far Future]** International tax/legal/compliance monetization structure (cross-border entity/payment/tax handling) is deferred and should be treated as a dedicated strategic program later.

## Exit Criteria for Phase 2

- Test and Live servers are provisioned and actively hosting the application.
- Reflex and API communicate securely via OAuth over HTTPS.
- Operational logs are centralized and tagged per user.
- Code changes can be deployed predictably via CI/CD pipelines.
- PH beta validation is complete without requiring new currency-core implementation.

## Beta Cutline Reference

For explicit "Necessary Right Now" vs "Post-Launch QoL" prioritization and dashboard-centric transition planning, see [Beta Launch Cutline and Dashboard-Centric Transition](file:///home/pproctor/Documents/python/finance_manager/design_docs/20_Roadmap/Beta_Launch_Cutline_and_SPA_Transition.md).
For owner lanes and execution sequencing of `[Now]` launch work, see [Beta Execution Board](file:///home/pproctor/Documents/python/finance_manager/design_docs/20_Roadmap/Beta_Execution_Board.md).
For runtime coordination during testing windows, use [Runtime Signup Sheet](file:///home/pproctor/Documents/python/finance_manager/design_docs/30_Releases/Runtime_Signup_Sheet.md) and [Runtime Owner Handoff Template](file:///home/pproctor/Documents/python/finance_manager/design_docs/30_Releases/Runtime_Owner_Handoff_Template.md).