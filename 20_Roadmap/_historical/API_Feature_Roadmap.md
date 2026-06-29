# API Feature Roadmap (Post-v1.0 Evolution)

> **2026-05-01:** Strategic phase map and PH-first constraints are **canonical** in `strategy/strategic-roadmap-reframe-53be/`. This file retains **API capability sketches**; treat "Reflex" mentions in body text as **historical** unless explicitly updated — flagship client is **`finance_manager_web`**.

**Context:** The API is currently at v1.0 and functional. However, as the **web** frontend and hosted beta scale, the API must evolve to support new requirements, offload client math, and harden security.
**Roadmap semantics:** "Phase" entries here represent major backend programs containing multiple epics and many implementation tasks over time.

## 📌 Phase 2: Beta Preparation (Hosting & Hardening)

**Goal:** Harden the API for a live, multi-user hosted environment.

- **Authentication Overhaul:** 
  - Implement full **OAuth** support to simplify user login.
  - Enforce strict token blacklist timeframes and JWT rotation policies.
- **Infrastructure Cleanup:** Clean up and finalize the API's Docker instances for production deployment.
- **Operational Logging:** 
  - Standardize logging using `loguru`.
  - Implement **user-tagged logs** so errors can be tracked and isolated per user in a multi-tenant environment.
- **Email Services:** Implement an email service integration to pipe automated bug reports (with attached user logs) and feature requests directly to the admin's personal email address.
- **Release Governance Link:** Coordinate public/private publication boundaries via [License and Release Gates](file:///home/pproctor/Documents/python/finance_manager/design_docs/20_Roadmap/License_and_Release_Gates.md).

## 📌 Post-Beta: Advanced Endpoints

**Goal:** Keep the **web** frontend lean by providing pre-calculated data structures for advanced visualizations.

- **Calendar Endpoints:** Create new endpoints (or expand existing Snapshot endpoints) to return pre-aggregated data specifically formatted for the **web** transaction calendar (and related views).
- **Payload Structure:** The API must calculate and return:
  - Daily transaction totals.
  - Weekly aggregated totals.
  - Monthly grand totals.
- **Performance:** Ensure these endpoints are optimized to return digestible packets quickly.
- **Near-Term Rollout Note:** Calendar payload contracts and visualization-oriented transaction/expense aggregates should be treated as first post-beta API feature slice for frontend parity.

## 📌 Phase 3: Rust Middleware Integration (strategic plan **S5**)

**Goal:** Offload the heaviest calculations and secure the data.

- **Strategic timing:** Serious production ZK / Rust middleware work is **gated to S5** in `strategy/strategic-roadmap-reframe-53be/` (not an implied “next quarter” from this legacy heading).
- **Rust Calculators:** Integrate the custom Rust calculators (developed in parallel) into the Django API's calculation pipeline to speed up heavy math.
- **Data Migration Tooling:** Support the Celery/Redis (or Rust-native) tools required to migrate and encrypt existing user data once the `finance_manager_crypto` layer goes live.
- **Repo and learning:** AI-orchestrated Rust implementation and audit milestones per strategic context §3.2; defer detailed scheduling to `phases/S5_*.md` when that phase approaches.
- **Security Program Link:** Middleware publication and adversarial validation planning are tracked in [Licensing and Publication Strategy](file:///home/pproctor/Documents/python/finance_manager/design_docs/40_System_Design/09_Licensing_and_Publication_Strategy.md), [Bounty Sandbox Architecture](file:///home/pproctor/Documents/python/finance_manager/design_docs/40_System_Design/10_Bounty_Sandbox_Architecture.md), and [Security Disclosure and Bounty Program](file:///home/pproctor/Documents/python/finance_manager/design_docs/40_System_Design/11_Security_Disclosure_and_Bounty_Program.md).