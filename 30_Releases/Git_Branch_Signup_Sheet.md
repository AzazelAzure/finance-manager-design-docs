# Git Branch Signup Sheet

## Purpose
Coordinate branch ownership across multiple agents so commit/push responsibility is explicit and only the final active owner performs closeout git operations.

## Branch Session
- Session ID: `branch-cycle-1`
- Branch name: `(set per repo)`
- Current owner: `(agent name)`
- Current status: `in_progress`
- Started at: `(timestamp)`
- Last updated at: `(timestamp)`

### Repo Ownership Map
- API repo (`finance_manager_api/`)
  - branch:
  - owner:
  - status: in_progress | handoff_pending | ready_for_closeout | closed
- CLI repo (`finance_manager_cli/`)
  - branch:
  - owner:
  - status: in_progress | handoff_pending | ready_for_closeout | closed
- Reflex repo (`finance_manager_reflex/`)
  - branch:
  - owner:
  - status: in_progress | handoff_pending | ready_for_closeout | closed

### Current Users
- Owner:
- Sublet users:
  - Agent:
    scope:
    started_at:

### Queue / Waiting Agents
- Agent:
  requested_scope:
  requested_at:
  priority: P0 | P1

### Ownership Transfer
- Pending transfer to:
- Transfer condition:
- Transfer timestamp:

### PR Tracking
- PR URL:
- PR status: draft | open | in_review | approved | changes_requested | merged
- Required checks:
  - check:
    status: pending | pass | fail
- Required signoffs:
  - reviewer/agent:
    status: pending | approved | blocked

### Notes
- Blockers:
- Cross-repo dependency notes:

## Status Vocabulary

- `in_progress`: active implementation work under current owner
- `handoff_pending`: work paused, transfer details being recorded
- `ready_for_closeout`: implementation done, waiting for final owner git closeout
- `closed`: final owner completed required git closeout actions

## Active Branch Record (Single Source of Truth)

```markdown
## Branch Session
- Session ID:
- Branch name:
- Current owner:
- Current status: in_progress | handoff_pending | ready_for_closeout | closed
- Started at:
- Last updated at:

### Repo Ownership Map
- API repo (`finance_manager_api/`)
  - branch:
  - owner:
  - status:
- CLI repo (`finance_manager_cli/`)
  - branch:
  - owner:
  - status:
- Reflex repo (`finance_manager_reflex/`)
  - branch:
  - owner:
  - status:

### Current Users
- Owner:
- Sublet users:
  - Agent:
    scope:
    started_at:

### Queue / Waiting Agents
- Agent:
  requested_scope:
  requested_at:
  priority: P0 | P1

### Ownership Transfer
- Pending transfer to:
- Transfer condition:
- Transfer timestamp:

### Final Closeout Owner
- Owner:
- Expected completion checklist:
  - [ ] `git status --short` reviewed per touched repo
  - [ ] `git diff` reviewed per touched repo
  - [ ] `git log --oneline -n 10` checked for message style
  - [ ] commit(s) created in correct repo boundary
  - [ ] branch pushed to remote
  - [ ] PR opened and linked in this record
  - [ ] required checks green
  - [ ] required signoffs/approvals recorded
  - [ ] post-push `git status` clean/expected
```

## Protocol

1. First agent working a branch becomes owner and creates/updates this sheet.
2. Non-owner agents do not finalize commit/push unless explicitly transferred ownership.
3. Every ownership transfer must include a git handoff record in:
   - `design_docs/30_Releases/Git_Owner_Handoff_Template.md`
4. Final owner is the only agent authorized to run closeout commit/push flow.
5. Final owner opens/updates PR metadata and verifies required checks/signoffs.
6. Final owner marks session `closed` only after merge readiness is satisfied.

## Guardrails

- Respect sub-repo boundaries; never mix repo commits.
- Keep handoffs short and actionable; include blockers before transfer.
- If another agent is active on `main`, defer push/merge actions until branch ownership is clear.
