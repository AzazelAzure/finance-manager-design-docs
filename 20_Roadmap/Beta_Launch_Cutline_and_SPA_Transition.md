# Beta Launch Cutline and Dashboard-Centric Transition

## Purpose
Define what is required to be server-ready for Beta launch versus what should be intentionally deferred as post-launch quality improvements. This plan assumes ongoing Reflex bugfix/UI polish work and enforces API-first delivery sequencing.

For owner lanes and parallel execution priorities, see [Beta Execution Board](file:///home/pproctor/Documents/python/finance_manager/design_docs/20_Roadmap/Beta_Execution_Board.md).

## Scope

- Primary near-term deliverable: stable hosted Beta with reliable core user workflows.
- Current UI direction: pseudo-single-page dashboard where core actions live on the dashboard and advanced workflows move to focused subpages.
- Constraint: no frontend-heavy aggregation logic; API owns heavy computation and aggregates.

## Phase A: Beta Launch Readiness (Necessary Right Now)
- Goal: Reach a stable, deployable, supportable hosted Beta baseline.
- Entry criteria: Alpha core flows are functional; active Reflex bugfix cycle in progress.
- Exit criteria: hosted test/live workflow is stable, secure auth is in place, and MVP user journeys are reliable.
- Breakpoints:
  - OAuth/token policy is incomplete.
  - Test server deployment is unstable.
  - Critical dashboard UX bugs block core flows.
  - API/CLI/Reflex contract mismatch appears.
- Triggers:
  - Move to Phase B when all "Necessary Right Now" items are complete and confidence checks pass.
- Dependencies:
  - API auth/logging readiness
  - Infra/test-live provisioning
  - Cross-repo contract checks
  - Reflex MVP bugfix stabilization
- Required implementation updates:
  - API: OAuth + token policy + user-tagged logging baseline
  - CLI: verification path for changed API contracts
  - Reflex: dashboard-first UX stabilization for core CRUD and snapshot visibility
  - Docs: update current-state and release workflow docs
- Verification gate:
  - End-to-end test-server pass for key user flows (auth, transaction create/edit, snapshot/dashboard load, error handling path)
  - No critical severity defects in core flows
  - Deployment routine reproducible
- Risks and mitigations:
  - Risk: shipping while UI overhaul is still evolving.
  - Mitigation: lock a Beta cutline and defer non-critical redesign items.

### Necessary Right Now Checklist

1. Hosting/Test Infrastructure
   - Provision and validate Test server first, then Live server.
   - Confirm containerized API + Reflex deployment on Test.
2. Security/Auth Baseline
   - OAuth integration complete.
   - JWT rotation/token blacklist policy enforced.
3. Operational Readiness
   - User-tagged logging enabled.
   - Basic bug-report email pipeline operational.
4. Contract Governance
   - API/CLI/Reflex compatibility matrix for Beta-critical endpoints.
   - No unresolved contract ambiguity in core workflows.
5. Reflex Beta UX Baseline
   - Resolve critical and high-severity dashboard UX/UI bugs.
   - Ensure core dashboard workflows are deterministic and usable.
6. Deployment and Verification
   - CI/CD or scripted deploy path tested.
   - Beta smoke checklist written and repeatable.

## Phase B: Dashboard-Centric UX Consolidation (Post-Launch QoL)
- Goal: Evolve toward pseudo-single-page dashboard architecture without destabilizing live operations.
- Entry criteria: Beta launch baseline is stable in production-like operations.
- Exit criteria: dashboard information architecture is clearer, with advanced features split into dedicated subpages.
- Breakpoints:
  - Core performance regressions from UI restructuring.
  - Navigation complexity increases user friction.
  - Feature scope expansion threatens reliability cadence.
- Triggers:
  - Start each QoL increment only after previous increment has no critical regressions.
- Dependencies:
  - Stable API contracts for dashboard tiles and advanced views.
  - Instrumented UX feedback from Beta cohort.
- Required implementation updates:
  - Reflex route/state architecture updates for dashboard-centric flow.
  - API endpoint support for dashboard summaries and advanced view data.
  - Documentation updates for IA and user workflow changes.
- Verification gate:
  - UX acceptance metrics improve or remain stable.
  - No degradation in core transaction and snapshot workflows.
- Risks and mitigations:
  - Risk: redesign churn causes release instability.
  - Mitigation: ship in thin slices with rollback-ready changes.

### Post-Launch QoL Backlog (Explicitly Deferred)

- Full visual overhaul beyond usability-critical fixes.
- Advanced dashboard interactivity and power-user data manipulation controls.
- Non-essential animation/transitions.
- Deep personalization/theming passes.
- Non-critical information architecture refinements.
- Extended exploratory visualizations not required for Beta core workflows.

## Pseudo-Single-Page Transition Strategy

1. Keep core actions on dashboard:
   - quick entry
   - latest snapshot summary
   - core account/transaction actions
2. Move complexity to subpages:
   - advanced analytics
   - large data tables
   - heavy configuration and niche workflows
3. Use progressive disclosure:
   - dashboard presents key summaries and directs to subpages for deep operations
4. Keep API-first alignment:
   - any new dashboard aggregate requirement must be API-provided before Reflex integration

## Execution Triggers

- Any core-flow defect found in Beta checklist -> route to immediate fix queue (Necessary Now).
- Any enhancement request that does not impact Beta launch criteria -> queue under Post-Launch QoL.
- Any new dashboard data requirement needing aggregation -> create upstream API contract task before Reflex work.
- Runtime-dependent testing waves must use shared runtime ownership tracking and handoff:
  - `design_docs/30_Releases/Runtime_Signup_Sheet.md`
  - `design_docs/30_Releases/Runtime_Owner_Handoff_Template.md`
