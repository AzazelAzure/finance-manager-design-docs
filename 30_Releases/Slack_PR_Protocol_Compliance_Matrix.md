# Slack PR Protocol Compliance Matrix

## Purpose

Track where the workspace enforces the Slack-first PR authorization protocol so automation coverage can be expanded across all sub-repos and the parent repo consistently.

## Required Gates

1. Feature-branch-only implementation (no direct feature work on `main`/`master`).
2. PR announcement to Slack `#pull-requests` with repo/branch/PR URL.
3. Wait/read Slack automation authorization before merge actions.
4. Reconcile Slack authorization with live GitHub mergeability/check/signoff state.
5. Treat Slack-approved but GitHub `CONFLICTING`/`DIRTY` as blocked.
6. Completion criteria include both Slack authorization and GitHub merge gates.
7. Handoff output includes Slack authorization and mergeability reconciliation state.

## Enforcement Matrix

| Gate | Enforced In |
|---|---|
| 1) Feature-branch-only implementation | `.cursor/rules/core-standards.mdc`, `.cursor/rules/git-repo-workflow.mdc`, `plans/templates/GEMINI_PLAN_TEMPLATE.md`, `plans/templates/GEMINI_PLAN_TEMPLATE_V2.md`, `plans/templates/GEMINI_PLAN_TEMPLATE_QUICK.md` |
| 2) Slack PR announcement | `.cursor/rules/git-repo-workflow.mdc`, `.cursor/skills/pr-ops-merge-readiness/SKILL.md`, `.cursor/skills/multi-repo-orchestration/SKILL.md`, `.cursor/rules/agent-delegation.mdc`, `design_docs/30_Releases/Git_Policy_Quickstart.md`, `design_docs/30_Releases/Subagent_Multi_Repo_Routine.md` |
| 3) Wait/read Slack authorization | `.cursor/skills/orchestration-manager/SKILL.md`, `.cursor/skills/pr-ops-merge-readiness/SKILL.md`, `.cursor/skills/multi-repo-orchestration/SKILL.md`, `.cursor/rules/agent-delegation.mdc`, `.cursor/skills/orchestration-manager/AGENT_PROMPT_TEMPLATE.md` |
| 4) Slack vs GitHub reconciliation | `.cursor/rules/core-standards.mdc`, `.cursor/rules/git-repo-workflow.mdc`, `.cursor/skills/orchestration-manager/SKILL.md`, `.cursor/skills/pr-ops-merge-readiness/SKILL.md`, `.cursor/skills/multi-repo-orchestration/SKILL.md`, `design_docs/30_Releases/Git_and_Workflow_Strategy.md`, `design_docs/30_Releases/Git_Policy_Quickstart.md`, `design_docs/30_Releases/Subagent_Multi_Repo_Routine.md`, `design_docs/hive_finances_manager/00_Hive_Protocol.md` |
| 5) Conflict mismatch => blocked | `.cursor/skills/orchestration-manager/SKILL.md`, `.cursor/skills/pr-ops-merge-readiness/SKILL.md`, `.cursor/rules/git-repo-workflow.mdc`, `design_docs/30_Releases/pr_status.md`, `design_docs/hive_finances_manager/00_Hive_Protocol.md`, `design_docs/30_Releases/Git_and_Workflow_Strategy.md` |
| 6) Completion criteria updated | `plans/templates/GEMINI_PLAN_TEMPLATE.md`, `plans/templates/GEMINI_PLAN_TEMPLATE_V2.md`, `plans/templates/GEMINI_PLAN_TEMPLATE_QUICK.md` |
| 7) Handoff contract updated | `.cursor/skills/shared-subagent-handoff/SKILL.md`, `design_docs/30_Releases/Git_Branch_Signup_Sheet.md` |

## Operational Notes

- Current Slack automation responses were observed in `#all-hive-financial-manager`; protocol target is now `#pull-requests`.
- `design_docs/30_Releases/pr_status.md` is retained as an optional audit ledger, not the primary authorization gate.
- Merge actions should proceed only when both signals agree:
  - Slack automation authorizes, and
  - GitHub reports mergeable/clean with required checks/signoffs passing.
- Parent workspace repo is temporarily on hold for protocol push/PR actions; continue protocol execution for linked sub-repos and `design_docs` updates only until hold is lifted.

## Automation Expansion Checklist

- Add/confirm automation watchers for:
  - `finance_manager_api`
  - `finance_manager_reflex`
  - `finance_manager_cli`
  - parent workspace/meta repo
  - any additional sub-repos in active use
- Ensure Slack posts for PR events route to `#pull-requests`.
- Ensure automation output includes explicit terminal state (`approved`, `merged`, `changes_requested`, `blocked`).
- Ensure merge-conflict state (`CONFLICTING`/`DIRTY`) is surfaced as `blocked` until resolved.
