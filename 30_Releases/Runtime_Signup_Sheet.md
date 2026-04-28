# Runtime Signup Sheet

## Purpose
Coordinate a single shared runtime across multiple agents during testing windows, minimizing redundant shutdown/startup cycles.

## Related Coordination Docs

- Git branch ownership and closeout tracking:
  - `design_docs/30_Releases/Git_Branch_Signup_Sheet.md`
- Git ownership transfer template:
  - `design_docs/30_Releases/Git_Owner_Handoff_Template.md`

## Runtime Session
- Session ID: `beta-now-cycle-1`
- Current owner: `Plan Orchestration Manager (Codex)`
- Runtime mode: `containerized`
- Current status: `live`
- Started at: `2026-04-27T04:34:00+08:00`
- Last updated at: `2026-04-27T22:10:00+08:00`

### Current Users
- Owner: `Plan Orchestration Manager (Codex)` (scope: Phase 2 `[Now]` Breakpoint A/B/C orchestration)
- Sublet users:
  - Agent: _(none yet)_
    scope: _(n/a)_
    started_at: _(n/a)_

### Lifecycle Commands (script-only)
- Last command: `./scripts/fm_services.sh stop && ./scripts/fm_docker.sh start --build && ./scripts/fm_docker.sh status && ./scripts/fm_services.sh status`
- Last status check:
  - `scripts/fm_docker.sh status`: `db/api/reflex/proxy all running; api healthy`
  - `scripts/fm_services.sh status`: `API stopped, Reflex stopped`

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
- Blockers: _No startup disconnect reproduced in containerized mode; observed data-source mismatch between local SQLite and container Postgres._
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
