# Runtime Signup Sheet

> **Example session below is historical (2026-04-27).** Update this sheet whenever a human or agent **owns** the shared runtime. Container stacks today are **API + web + proxy** (Reflex removed from architecture 2026-04-30).

## Purpose
Coordinate a single shared runtime across multiple agents during testing windows, minimizing redundant shutdown/startup cycles.

## Related Coordination Docs

- Git branch ownership and closeout tracking:
  - `design_docs/30_Releases/Git_Branch_Signup_Sheet.md`
- Git ownership transfer template:
  - `design_docs/30_Releases/Git_Owner_Handoff_Template.md`

## Runtime Session
- Session ID: `beta-f012-f014-blue-promotion-2026-06-26`
- Current owner: `Cursor (cur/s1b/chore/f012-f014-governance)`
- Runtime mode: `containerized` (VPS blue-green, fm-beta)
- Current status: `live`
- Started at: `2026-06-26T11:37:00+08:00`
- Last updated at: `2026-06-26T14:31:00+08:00`

### Current Users
- Owner: `Cursor (cur/s1b/chore/f012-f014-governance)` (scope: **S1.B** / F-012+F-013+F-014 API merge `6cdb7d0` promoted to active blue; green is rollback color)
- Sublet users:
  - Agent: _(none yet)_
    scope: _(n/a)_
    started_at: _(n/a)_

### Lifecycle Commands (script-only)
- Last command: `./scripts/fm_server_beta.sh rebuild-color blue && ./scripts/fm_server_beta.sh smoke --color blue && ./scripts/fm_server_beta.sh switch --to blue` (VPS)
- Last status check:
  - `scripts/fm_server_beta.sh status`: active `blue`, inactive `green`; api-blue `healthy`; `celery-worker` and `celery-beat` up
  - migration `0010_usage_monitoring_f014` shows `[X]` applied on shared DB (entrypoint auto-migrate)
  - smoke `--color blue`: passed before switch; active API/web return HTTP 200
  - `celery inspect registered`: `notify_operator`, `send_weekly_feature_requests_email`, and `rollup_daily_usage` all registered (`missing=none`)

### Queue / Waiting Agents
- Agent: _(none)_
  requested_scope: _(n/a)_
  requested_at: _(n/a)_
  priority: `P0`

### Ownership Transfer
- Pending transfer to: _(none)_
- Transfer condition: _(n/a)_
- Transfer timestamp: _(n/a)_

### Notes
- **Celery worker + single beat now LIVE** on VPS (`fm-beta_celery-worker_1`, `fm-beta_celery-beat_1`) and included in `fm_server_beta.sh rebuild-color` after parent PR #67. Worker connected to `redis://redis:6379/0`, `celery@... ready`.
- **F-014 task registration is deployed:** API PR #38 (`6cdb7d0`) is live on active blue. Worker registers `notify_operator`, `rollup_daily_usage`, and `send_weekly_feature_requests_email`.
- **Compose-output redaction hotfix is merged/deployed:** parent PR #68 (`8c7bcc4`) is merged and already deployed to VPS. This prevents future `podman-compose up` runs from printing interpolated `.secrets/server.env` values.
- **Live SMTP proof sent:** queued `notify_operator` event `SMTP_LIVE_TEST` through Celery; worker completed task `47fbd4a7-dc0d-4c13-9a8f-7d945a6efd74` with result `sent:SMTP_LIVE_TEST` in ~4.7s.
  - SMTP send path = Proton SMTP submission token (not headless Bridge). VPS `.secrets/server.env` now includes SMTP settings for `support@thehivemanager.com` (`EMAIL_HOST=smtp.protonmail.ch`, `EMAIL_PORT=587`, TLS enabled, password present). `celery-worker`/`celery-beat` were recreated after env wiring.
  - Current state: bug-report POSTs succeed, `notify_operator` sends through SMTP, rollup/digest beat tasks are registered. Migration `0010` is additive and safe for rollback to green.
- Rollback target is green via `./scripts/fm_server_beta.sh rollback` if needed.
- Mixed runtime allowed? `no`

## Status Vocabulary

- `loading`: owner is starting or rebuilding runtime
- `live`: runtime is healthy and available for shared testing
- `sublet`: non-owner agent is actively using owner's live runtime
- `shutting_down`: owner is ending testing window and performing teardown
- `offline`: runtime is not active

## Active Runtime Record (Single Source of Truth)

```markdown
## Runtime Session
- Session ID:
- Current owner:
- Runtime mode: containerized | local-services
- Current status: loading | live | sublet | shutting_down | offline
- Started at:
- Last updated at:

### Current Users
- Owner:
- Sublet users:
  - Agent:
    scope:
    started_at:

### Lifecycle Commands (script-only)
- Last command:
- Last status check:
  - `scripts/fm_docker.sh status`:
  - `scripts/fm_services.sh status`:

### Queue / Waiting Agents
- Agent:
  requested_scope:
  requested_at:
  priority: P0 | P1

### Ownership Transfer
- Pending transfer to:
- Transfer condition:
- Transfer timestamp:

### Notes
- Blockers:
- Mixed runtime allowed? no (default) / yes (must justify):
```

## Protocol

1. First agent needing runtime creates/updates current session and becomes owner.
2. Owner sets status to `loading` then `live` after successful status checks.
3. Additional agents do not start runtime; they add themselves under `Sublet users`.
4. If owner finishes first and another sublet user still needs runtime, transfer ownership in the sheet before owner exits.
5. Last active owner performs teardown (if needed), sets status to `offline`, and records final status checks.

## Guardrails

- Shared runtime is preferred over repeated up/down cycles.
- Non-owner agents must not run start/stop/rebuild/clean commands.
- Every ownership transfer must be paired with a runtime handoff record in:
  - `design_docs/30_Releases/Runtime_Owner_Handoff_Template.md`
