# License and Release Gates (Draft v1)

## Purpose

Define roadmap gates that coordinate:

- what is private vs public,
- when middleware can be published,
- when bounty sandbox can be opened,
- and how release operations adapt without destabilizing core product delivery.

## Streams

## Stream A: Proprietary Product (API + **Web**)

- Full-feature product remains private/proprietary.
- Release gating prioritizes hosted beta reliability, UX parity, and operational stability.

## Stream B: Open Middleware

- Middleware publication and hardening is a distinct stream with independent gates.
- Must remain API-agnostic, low-overhead, and integration-friendly.

## Stream C: Semi-Public Bounty Sandbox

- Reduced/skeleton stack for adversarial testing and coordinated disclosures.
- Non-commercial use direction pending legal finalization.

## Gate Roadmap

## Gate G1: Boundary Lock

- Lock code ownership boundaries between private product and public/security surfaces.
- Confirm no accidental dependency inversion from private code into public middleware.

## Gate G2: Middleware Public-Readiness

- Choose middleware license.
- Publish contribution policy (`CLA`/`DCO`) and `SECURITY.md`.
- Establish initial compatibility targets and performance baseline.

## Gate G3: Sandbox Internal Readiness

- Sandbox architecture implemented with synthetic data only.
- Scope and abuse controls tested internally.
- Reset and incident controls validated.

## Gate G4: Sandbox External Pilot

- Invite-only tester cohort.
- Triage and response workflow validated against real submissions.
- Critical findings triaged without impacting private production operations.

## Gate G5: Broader Security Program

- Public policy, scope, and disclosure model stable.
- Payout and legal operations defined.
- Program cadence integrated into release management.

## Dependencies and Triggers

- Stream A reliability milestones influence capacity, but do not redefine Stream B/C governance.
- Stream B/C may proceed in parallel when security ownership and operational bandwidth exist.
- Any critical middleware finding can block related release lanes until resolved.

## Rollback/Mitigation Paths

- If sandbox leakage risk appears, pause external access and revert to internal-only mode.
- If legal/terms are incomplete, block external bounty launch but continue internal security validation.
- If middleware release quality drops, freeze publication and focus on compatibility/performance remediation.

## Immediate Next Actions

- Approve v1 boundary map (private vs public vs semi-public).
- Draft final license decision matrix for middleware.
- Draft initial sandbox scope table and reporting workflow owner list.