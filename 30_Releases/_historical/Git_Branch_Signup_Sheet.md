# Git Branch Signup Sheet

## Purpose
Coordinate branch ownership across multiple agents so commit/push responsibility is explicit and only the final active owner performs closeout git operations.

## Branch Session
- Session ID: `f011-landing-reflect-roadmap-2026-06-28`
- Branch name: `cur/s1b/feat/f011-landing-reflect-roadmap`
- Current owner: _(released — merged + promoted)_
- Current status: `closed`
- Started at: `2026-06-28T16:10:00+08:00`
- Last updated at: `2026-06-28T16:50:00+08:00`

### Repo Ownership Map
- Web repo (`finance_manager_web/`)
  - branch: `cur/s1b/feat/f011-landing-reflect-roadmap`
  - owner: `Cursor (F011)`
  - status: closed
- CLI repo (`finance_manager_cli/`)
  - branch:
  - owner:
  - status: in_progress | handoff_pending | ready_for_closeout | closed
- Reflex repo (`finance_manager_reflex/`)
  - branch:
  - owner:
  - status: in_progress | handoff_pending | ready_for_closeout | closed

### Current Users
- Owner: _(released — unassigned)_
- Sublet users:
  - _(none)_

### Queue / Waiting Agents
- Agent: _(none)_
  requested_scope: _(n/a)_
  requested_at: _(n/a)_
  priority: P1

### Ownership Transfer
- Pending transfer to: _(none)_
- Transfer condition: _(n/a)_
- Transfer timestamp: `2026-06-28T11:00:00+08:00` (received from F004 closeout)

### PR Tracking
- PR URL: Web https://github.com/AzazelAzure/finance-manager-web/pull/90
- PR status: merged (`8c117ee`); promoted to production active green 2026-06-28
- Slack channel: `#pull-requests`
- Slack authorization state: merged
- Required checks:
  - check: API CI / Web CI
    status: pass
- Required signoffs:
  - reviewer/agent: HitM + agent review
    status: approved

### Notes
- **F004 complete:** merged to `main` (migrations through `0013_upcomingexpense_bill_realism_fields`). F001 resumes with migration `0014_*` or later.
- **F001 complete:** API/Web PRs merged, active blue deploy completed, runtime released. Follow-ups tracked in F001 `runtime_handoff.md`.

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
6. Final owner confirms Slack `#pull-requests` authorization and reconciles with GitHub mergeability state before merge.
7. Final owner marks session `closed` only after merge readiness is satisfied.

## Guardrails

- Respect sub-repo boundaries; never mix repo commits.
- Keep handoffs short and actionable; include blockers before transfer.
- If another agent is active on `main`, defer push/merge actions until branch ownership is clear.
