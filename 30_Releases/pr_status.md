# PR Status Ledger

## Purpose

Optional ledger for PR lifecycle notes. Primary review authorization now comes from Slack automation replies in `#pull-requests`, reconciled with live GitHub PR state.

## Status Vocabulary

- `opened` - local agent opened PR and is waiting for review.
- `reviewing` - CLI agent acknowledged and is actively monitoring.
- `approved` - review approved but not yet merged.
- `merged` - PR merged to target branch.
- `changes_requested` - reviewer requested changes.
- `blocked` - automation/review process failed or cannot continue.

## Entry Format

Use append-only lines with this exact pattern:

`<timestamp_iso> | <repo>/<branch> | <status> | <actor> | <notes>`

Example:

`2026-04-27T14:20:00+08:00 | finance_manager_reflex/fix/empty-state-var-truthiness | opened | local-agent | pr=https://github.com/.../pull/3`

## Coordination Flow

1. Agent commits, pushes, opens PR.
2. Agent posts to Slack `#pull-requests` with repo, branch, and PR URL.
3. Agent waits/reads Slack automation replies for authorization state.
4. Agent verifies live GitHub mergeability/check/signoff state.
5. If Slack says approved but GitHub is `CONFLICTING` or `DIRTY`, mark as `blocked` and resolve conflicts before merge.
6. Merge only when Slack authorization and GitHub gates both pass.
7. Use this file as an optional audit trail when needed.

## Active Entries (Optional)



## Quick Entry Macros

Replace placeholders:

- `<ts>` ISO timestamp (example: `2026-04-27T14:30:00+08:00`)
- `<repo>` one of `finance_manager_api`, `finance_manager_reflex`, `finance_manager_cli`
- `<branch>` branch name
- `<actor>` `local-agent` or `cli-agent`
- `<notes>` short context (PR URL, reviewer, blocker, etc.)

```text
# opened (local agent right after PR create)
<ts> | <repo>/<branch> | opened | <actor> | <notes>

# reviewing (CLI agent acknowledged and watching)
<ts> | <repo>/<branch> | reviewing | <actor> | <notes>

# approved (review approved, may not be merged yet)
<ts> | <repo>/<branch> | approved | <actor> | <notes>

# merged (PR merged)
<ts> | <repo>/<branch> | merged | <actor> | <notes>

# changes requested (reviewer requested updates)
<ts> | <repo>/<branch> | changes_requested | <actor> | <notes>

# blocked (automation or review pipeline issue)
<ts> | <repo>/<branch> | blocked | <actor> | <notes>
```

