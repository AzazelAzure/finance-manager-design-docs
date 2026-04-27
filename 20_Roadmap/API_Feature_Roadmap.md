# API Feature Roadmap (Post-v1.0 Evolution)

**Context:** The API is currently at v1.0 and functional. However, as the Reflex frontend scales to v1.0 and the system prepares for a hosted Beta launch, the API must evolve to support new requirements, offload client math, and harden security.
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

**Goal:** Keep the Reflex frontend lean by providing pre-calculated data structures for advanced visualizations.

- **Calendar Endpoints:** Create new endpoints (or expand existing Snapshot endpoints) to return pre-aggregated data specifically formatted for the Reflex Transaction Calendar View.
- **Payload Structure:** The API must calculate and return:
  - Daily transaction totals.
  - Weekly aggregated totals.
  - Monthly grand totals.
- **Performance:** Ensure these endpoints are optimized to return digestible packets quickly.
- **Near-Term Rollout Note:** Calendar payload contracts and visualization-oriented transaction/expense aggregates should be treated as first post-beta API feature slice for frontend parity.

## 📌 Phase 3: Rust Middleware Integration

**Goal:** Offload the heaviest calculations and secure the data.

- **Rust Calculators:** Integrate the custom Rust calculators (developed in parallel) into the Django API's calculation pipeline to speed up heavy math.
- **Data Migration Tooling:** Support the Celery/Redis (or Rust-native) tools required to migrate and encrypt existing user data once the `finance_manager_crypto` layer goes live.
- **Repo and Learning Gate:** Production Rust integration work begins only after Rust repos are fully initialized and owner learning milestones complete baseline proficiency for hands-on middleware development.
- **Security Program Link:** Middleware publication and adversarial validation planning are tracked in [Licensing and Publication Strategy](file:///home/pproctor/Documents/python/finance_manager/design_docs/40_System_Design/09_Licensing_and_Publication_Strategy.md), [Bounty Sandbox Architecture](file:///home/pproctor/Documents/python/finance_manager/design_docs/40_System_Design/10_Bounty_Sandbox_Architecture.md), and [Security Disclosure and Bounty Program](file:///home/pproctor/Documents/python/finance_manager/design_docs/40_System_Design/11_Security_Disclosure_and_Bounty_Program.md).