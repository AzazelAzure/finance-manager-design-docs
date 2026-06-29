# Phase Execution Triggers and Breakpoints (historical only)

> **Do not execute new work from this document.** It preserves pre–S1-reframe "Phase 1.5 / Phase 2" language. **Canonical** triggers, stage exits, and numeric validation: `strategy/strategic-roadmap-reframe-53be/validation_gates.md` and `kill_commit_gates.md`. Body text below was updated 2026-05-01 to say **web** where it previously said **Reflex** for the flagship GUI; narrative phase names remain legacy.

## Purpose
Define a repeatable roadmap planning structure with hard decision gates, phase triggers, and verification checkpoints for multi-repo rollout.

## Planning Baseline

- API-first delivery dependency remains mandatory.
- CLI is the integration verification layer before GUI implementation.
- **Web** (`finance_manager_web`) is lean; heavy aggregation stays in API (or Rust middleware later, strategic S5).

## Phase Plan

## Phase 1.5: Contract Stabilization Sprint
- Goal: Lock contracts needed for Beta-critical workflows.
- Entry criteria: current Alpha state remains stable across API and CLI core flows.
- Exit criteria: contract matrix published for prioritized features.
- Breakpoints:
  - Contract ambiguity in any critical endpoint.
  - Missing compatibility verification path in CLI.
- Triggers:
  - Trigger to Phase 2 when contracts are explicit and CLI-verifiable.
- Dependencies:
  - API endpoint ownership and response schema agreement.
- Required implementation updates:
  - API contract docs and CLI command validation coverage.
- Verification gate:
  - CLI validation passes for all stabilized contracts.
- Risks and mitigations:
  - Risk: premature web UI wiring to unstable contracts.
  - Mitigation: block web UI work until CLI gate passes.

## Phase 2: Beta Preparation and Hosting
- Goal: Deliver secure hosted environments with predictable deployment flow.
- Entry criteria: contract stabilization checkpoint passed.
- Exit criteria: test/live environments, OAuth baseline, deployment routine, and user-tagged logging in place.
- Breakpoints:
  - OAuth/token policy unresolved.
  - Environment provisioning unstable.
  - Cross-repo contract drift detected.
- Triggers:
  - Trigger to Post-Beta feature expansion when hosted stack is stable and observable.
- Dependencies:
  - Infra scripts, auth implementation, deployment workflow, and compatibility governance.
- Required implementation updates:
  - API auth/logging capabilities, CLI compatibility checks, web hosted verification.
- Verification gate:
  - End-to-end workflow passes in Test environment with production-like settings.
- Risks and mitigations:
  - Risk: infrastructure and feature work collide, causing unstable releases.
  - Mitigation: enforce milestone gates and separate rollout windows.

## Phase 2.5: Visualization Readiness Gate
- Goal: Prepare API + web client for advanced visual views without violating lean-frontend rule.
- Entry criteria: Phase 2 stable test environment available.
- Exit criteria: calendar/graph payloads validated through API and CLI before UI finalization.
- Breakpoints:
  - API aggregation performance regressions.
  - Missing day/week/month output parity.
- Triggers:
  - Trigger to Post-Beta Track A after visualization contracts are stable.
- Dependencies:
  - API aggregation endpoints and CLI verification commands.
- Required implementation updates:
  - API aggregate payloads, CLI validation paths, web client wiring.
- Verification gate:
  - Contract tests + CLI checks + web integration smoke pass.
- Risks and mitigations:
  - Risk: frontend computes heavy aggregates locally.
  - Mitigation: reject frontend-local aggregate logic in reviews.

## Post-Beta Track A: Feature Expansion
- Goal: Deliver budgeting, savings, credit tracking, and upcoming expenses improvements.
- Entry criteria: hosted beta stable and monitored.
- Exit criteria: prioritized feature set released with API-first discipline and tutorial-ready UX.
- Breakpoints:
  - Feature demand exceeds API capacity/performance budget.
  - UX complexity exceeds target user usability baseline.
- Triggers:
  - Trigger to Track D mobile when localization assets and locale behavior stabilize.
- Dependencies:
  - Stable API contracts, frontend component patterns, roadmap prioritization.
- Required implementation updates:
  - Feature contracts in API, CLI verification hooks, web user flows, docs sync.
- Verification gate:
  - Cross-repo validation and changelog/doc updates per feature increment.
- Risks and mitigations:
  - Risk: feature bloat without rollout discipline.
  - Mitigation: phase-based release slicing with explicit acceptance criteria.

## Post-Launch Beta Localization Hardening
- Goal: Validate and harden localization quality during beta operations after initial hosted launch.
- Entry criteria: Phase 2 launch gates passed and live beta telemetry available.
- Exit criteria: locale behavior is stable for priority cohorts and supported UI surfaces.
- Breakpoints:
  - Locale-specific formatting defects above accepted threshold.
  - Translation/terminology drift affecting user comprehension.
- Triggers:
  - Trigger mobile execution planning when localization assets and behavior are stable.
- Dependencies:
  - Existing currency support baseline, UI copy/format audit pass, beta feedback loop output.
- Required implementation updates:
  - Web localization adjustments, docs updates, and targeted regression checks.
- Verification gate:
  - Beta localization test checklist passes for priority locales and critical workflows.
- Risks and mitigations:
  - Risk: localization pushed too early destabilizes core beta.
  - Mitigation: keep localization hardening post-launch with controlled slices.

## Track D (Post-Beta): Mobile Execution Gate
- Goal: Start mobile work only after localization and shared design assets stabilize.
- Entry criteria: localization and i18n behavior accepted in the web flagship.
- Exit criteria: mobile architecture plan validated with offline-first sync constraints.
- Breakpoints:
  - Offline sync conflict strategy not finalized.
  - Locale/UI parity not stable across clients.
- Triggers:
  - Trigger implementation once offline sync architecture is verified and operationally feasible.
- Dependencies:
  - Locale standards, sync architecture, legal/compliance readiness.
- Required implementation updates:
  - Mobile architecture docs and sync model readiness notes.
  - SMS wallet ingestion architecture for optional transaction capture from PH wallet messages (not payment rail integration).
- Verification gate:
  - Prototype sync validation and conflict-resolution test outcomes.
- Risks and mitigations:
  - Risk: mobile starts before platform contracts are mature.
  - Mitigation: hard execution gate tied to localization completion and sync readiness.

## Far-Future Track C: Monetization and Legal/Tax Readiness
- Goal: Prepare safe monetization foundations for international SaaS operations.
- Entry criteria: product, operations, and support maturity are established.
- Exit criteria: legal/tax/payment architecture is validated for target jurisdictions before monetization launch.
- Breakpoints:
  - Unresolved jurisdictional compliance requirements.
  - Unclear cross-border tax/payment entity strategy.
- Triggers:
  - Trigger implementation only when dedicated legal/tax program outputs are approved.
- Dependencies:
  - Legal counsel guidance, tax strategy, entity/payment architecture decisions.
- Required implementation updates:
  - Billing architecture plan, compliance controls, and regional rollout constraints.
- Verification gate:
  - Formal legal/tax readiness checklist approved for initial monetized regions.
- Risks and mitigations:
  - Risk: early monetization introduces legal/tax exposure.
  - Mitigation: defer monetization until readiness program is complete.

## Ongoing Trigger Rules

- Any contract-level change triggers:
  1. `multi-repo-orchestration` routine
  2. `design-docs-sync` routine
- Any phase or milestone scope change triggers:
  1. `roadmap-rollout-planning` routine
  2. Strategic plan / validation gate refresh in `strategy/strategic-roadmap-reframe-53be/` (and `design_docs/` when behavior changes)
- Any `[Now]` runtime-dependent validation wave triggers:
  1. Runtime owner assignment in `design_docs/30_Releases/Runtime_Signup_Sheet.md`
  2. Breakpoint-based handoff logging in `design_docs/30_Releases/Runtime_Owner_Handoff_Template.md`
