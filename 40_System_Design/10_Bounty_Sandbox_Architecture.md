# Bounty Sandbox Architecture (Draft v1)

## Purpose

Define a dedicated semi-public environment for security and breach testing that:
- uses synthetic/spoofed data only,
- validates middleware and integration security behavior,
- excludes proprietary full-feature product behavior.

## Environment Topology

## Sandbox Runtime
- Separate server/runtime from production and internal staging.
- Isolated credentials, network boundaries, and storage.
- Disposable environment patterns (scheduled reset/refresh).

## Data Policy
- Synthetic or generated data only.
- No ingestion of real customer production data.
- Repeatable seed datasets for deterministic bug reproduction.

## Traffic and Access
- Auth required for tester access.
- Rate limiting and abuse controls enabled by default.
- Endpoint and route allowlist to prevent accidental feature creep.

## Feature Surface Matrix (v1 draft)

## In Scope
- Middleware integration paths.
- Core auth/session lifecycle relevant to middleware.
- Security-sensitive API request/response paths in reduced form.
- Pseudo frontend interactions needed to exercise middleware behavior.

## Out of Scope
- Full commercial feature set.
- Internal administration workflows.
- Private integrations and proprietary automation tooling.
- Production observability/control planes.

## Deployment Model

- `sandbox-api`: reduced API profile with mock/safe adapters.
- `sandbox-frontend`: pseudo frontend for exercise and reproduction.
- `sandbox-middleware`: target security middleware build under test.
- `sandbox-db`: isolated seeded datastore with periodic reset.

## Operational Controls

- Environment reset cadence (daily/weekly) with fresh seeds.
- Audit logs for tester actions and exploit reproduction paths.
- Kill switch / maintenance mode for active incident triage.
- Explicit version labels tied to middleware and sandbox adapter versions.

## Security Boundaries

- No shared secrets with production.
- No direct network trust path to production systems.
- No direct deployment coupling with private product pipelines.
- High-risk findings in sandbox treated as blockers for middleware release gates.

## Release Gates

## Gate S1: Internal Dry Run
- Internal red-team pass on sandbox architecture and reset controls.

## Gate S2: Limited External Cohort
- Controlled tester group validates scope and submission workflow.

## Gate S3: Broader Bounty Availability
- Scope, terms, and triage process stable.
- Known high-risk false positives documented.

## Deferred Items

- Exact hosting provider and autoscaling policy.
- Final feature matrix by endpoint/component list.
- Payout-severity mapping integration into public program docs.
