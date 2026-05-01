# Security Disclosure and Bounty Program (Draft v1)

## Purpose

Define a predictable intake, triage, and response model for security reports affecting:

- open middleware components,
- sandbox integration layers,
- scoped security-critical API adapters.

This draft is operational guidance and not legal advice.

## Reporting Channels

- Primary: security contact email (to be defined).
- Secondary: issue template path for non-sensitive disclosures.
- Emergency escalation path for actively exploitable criticals (to be defined).

## Disclosure Model

- Coordinated disclosure by default.
- Reporter must provide reproducible steps and impact description.
- Program acknowledges receipt within target SLA window (TBD).
- Public disclosure window opens after fix/deadline policy (TBD).

## Scope (Initial Draft)

## In Scope

- Middleware cryptographic misuse resistance.
- Key handling, encryption/decryption safety boundaries.
- Sandbox integration vulnerabilities affecting middleware assurance.

## Out of Scope

- Denial-of-service from unrealistic traffic floods unless specifically declared.
- Social engineering/phishing against maintainers.
- Attacks requiring non-default unsupported local setup.

## Severity and Triage

- Severity levels: Critical / High / Medium / Low.
- Prioritization dimensions:
  - exploitability,
  - confidentiality/integrity impact,
  - blast radius,
  - reproducibility.
- Critical/High findings become release blockers for impacted middleware versions.

## Safe Harbor (Draft Principles)

- Good-faith research in scoped systems is permitted.
- No data exfiltration attempts from non-sandbox/prod systems.
- No persistence attempts outside agreed testing boundaries.
- Researchers must stop and report on first confirmed critical path.

## Response Workflow

1. Intake and acknowledgment.
2. Scope validation and severity assignment.
3. Reproduction and ownership routing.
4. Fix, regression verification, and release decision.
5. Researcher communication and optional public advisory.

## Bounty Operations (Draft)

- Bounty eligibility tied to in-scope findings only.
- Payout tiers mapped to severity and report quality (TBD).
- Duplicate findings handled by first valid report timestamp.
- Program may pause during major architecture migrations.

## Required Companion Artifacts

- `SECURITY.md` in public middleware repo.
- Public policy page for scope + safe harbor + reporting steps.
- Internal triage runbook and incident ownership roster.

## Deferred Items

- Specific SLA numbers.
- Exact payout table.
- Jurisdiction/legal language and tax/payment operations.