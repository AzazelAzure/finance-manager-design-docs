# Git Policy Quickstart

## Purpose

One-page operating checklist for agents and humans to keep repo hygiene consistent across API, CLI, Reflex, and docs workstreams.

## Default Policy

1. Use a feature branch for work intended for shared history.
2. Do not implement feature work directly on `main`/`master`.
3. Keep commits scoped to one repo boundary and one coherent intent.
4. Push branch, open PR, and track PR URL/status.
5. Merge only after required checks and required signoffs/approvals pass.

## Local GitHub CLI

- `gh` is installed locally and should be used from terminal for GitHub operations.
- Local agents may use `gh` to create PRs, inspect checks, review comments, and manage merge flow.
- Basic checks:
  - `gh --version`
  - `gh auth status`

## Pre-Work Checklist

- [ ] Confirm active repo (`finance_manager_api/`, `finance_manager_cli/`, `finance_manager_reflex/`, or docs repo).
- [ ] Confirm current branch is correct for this task.
- [ ] Confirm scope boundary (single repo unless explicitly approved for multi-repo orchestration).

## Implementation Checklist

- [ ] Keep commit scope small and message clear (why-oriented when possible).
- [ ] Update changelog for behavior changes in touched repo.
- [ ] Update design docs when contracts, behavior, or rollout assumptions change.
- [ ] Record cross-repo dependencies as explicit handoffs/follow-ups.

## PR Checklist

- [ ] Branch is pushed and remote tracking is healthy.
- [ ] PR title states intent and target repo context.
- [ ] PR body includes summary, validation run, and risks/follow-ups.
- [ ] PR is announced in Slack `#pull-requests` (repo, branch, PR URL).
- [ ] Slack automation authorization state is observed (`approved`/`merged` or explicit blocker).
- [ ] Required checks are green.
- [ ] Required signoffs/approvals are present.
- [ ] Slack authorization and GitHub mergeability are reconciled (no `CONFLICTING`/`DIRTY` mismatch).
- [ ] No unresolved high-severity findings remain.

## Merge Readiness Gate

A change is merge-ready only when:

- branch/PR metadata is complete,
- required checks pass,
- required signoffs are present,
- changelog/docs obligations are satisfied.

## Multi-Repo Rule

When a task spans repos, execute upstream contract repos first (typically API), then downstream consumers (CLI/Reflex), and keep separate branches/PRs per repo.

## Related Runbooks

- `design_docs/30_Releases/Git_and_Workflow_Strategy.md`
- `design_docs/30_Releases/Git_Branch_Signup_Sheet.md`
- `design_docs/30_Releases/Git_Owner_Handoff_Template.md`
- `design_docs/30_Releases/Subagent_Multi_Repo_Routine.md`
