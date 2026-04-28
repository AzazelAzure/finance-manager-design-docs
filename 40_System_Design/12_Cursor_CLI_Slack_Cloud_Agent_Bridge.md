# Cursor CLI ↔ Slack ↔ Cloud Agent Bridge

## Purpose

Single manifest for **GitHub cloud agents**, **CI/automation**, and **humans** describing how to task the headless **Cursor CLI agent** over Slack, which workflows to run, how to stay aligned with **git/PR policy**, and how to choose a **runtime** (local workstation vs future server). Cloud agents should treat this file as the operational contract; pin a short summary in `#cli-interface` and link here.

**Canonical path (this repo):** `design_docs/40_System_Design/12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md`

**Headless runner (implementation):** `scripts/cursor_headless_slack_agent.py`

## Slack channels

| Channel | Role |
|--------|------|
| `#cli-interface` | Task intake: mention the bot or use the task prefix (see runner env e.g. `CURSOR_SLACK_PREFIX`); the runner posts **threaded** results here. |
| `#pull-requests` | **Required** for PR lifecycle: announce PRs (repo, branch, URL), read automation authorization, **reconcile with live GitHub** mergeability and checks. See `design_docs/30_Releases/Slack_PR_Protocol_Compliance_Matrix.md`. |

The CLI agent process should **monitor both** channels: tasks in `#cli-interface`, authorization and merge state in `#pull-requests`.

## Message format (task prompt to the CLI agent)

Slack is lossy for structure; use a **consistent, copy-paste block** so the agent (and you) can parse intent. Recommended fields (omit unknowns, never invent repo names):

```text
@<bot> !cursor
REPO: <finance_manager_api | finance_manager_reflex | finance_manager_cli>
WORKSPACE_PATH: <absolute path on the machine running the headless agent; on a future server, the deployed workspace root>
RUNTIME: <local | server>
RUNTIME_NOTES: <e.g. "container owner: team X" or "no docker until approved" — free text>
SKILLS: roadmap-rollout-planning → orchestration-manager → design-docs-sync; pr-ops-merge-readiness; multi-repo-orchestration (if cross-repo)
PLAN_ROOT: <after planning: plans/<proposed-git-branch-name>/ — must match the feature branch you will use>
TASK: |
  <What to achieve; constraints; out of scope.>
```

**Notes**

- **REPO** must match the **sub-repo** you intend to commit in (one PR scope per repo; split work per `design_docs/30_Releases/Git_Policy_Quickstart.md` and workspace rules).
- **SKILLS** are **Cursor skill names** (folders under `.cursor/skills/`), not Slack slash commands. In headless mode the agent should **open and follow** the matching `SKILL.md` files. “`/roadmap-rollout-planning`” in chat means: execute the **roadmap-rollout-planning** skill; same for **orchestration-manager** and **design-docs-sync**.

## Standard workflow (required order)

1. **roadmap-rollout-planning**  
   - Produce an **execution-ready** plan under `plans/<proposed-git-branch-name>/` (one directory per batch; branch name should match your intended git feature branch).  
   - Include phases, checkpoints, validation gates, and explicit **exit criteria** before implementation continues.

2. **orchestration-manager**  
   - Use the plan root above as the **canonical plan root**.  
   - Enforce breakpoint validation, delegate to the right skills, and do not mark complete until readiness checks pass (see `.cursor/skills/orchestration-manager/SKILL.md`).

3. **Feature implementation** (in the target sub-repo)  
   - On a **feature branch**, not `main`/`master`.  
   - Changelog in the same sub-repo when behavior changes.

4. **pr-ops-merge-readiness**  
   - Open the PR, ensure description and checks are merge-ready, follow repo-local validation.

5. **design-docs-sync**  
   - After behavior or governance changes, update the appropriate files under `design_docs/` (see `design_docs/10_Current_State/02_Documentation_Sync_Protocol.md`).

6. **Slack + GitHub reconciliation**  
   - Post the PR to `#pull-requests` (repo, branch, URL).  
   - Wait for automation messages; **never merge on Slack approval alone** if GitHub reports `CONFLICTING` or dirty merge state.  
   - Document blockers and handoffs per `design_docs/30_Releases/Subagent_Multi_Repo_Routine.md` and workspace rules.

## PR and merge monitoring

- **Primary gate:** Slack automation in `#pull-requests` **plus** GitHub PR state (checks, mergeability, review).  
- **Optional audit trail:** append lines to `design_docs/30_Releases/pr_status.md` using the format there (`opened`, `reviewing`, `approved`, `merged`, `changes_requested`, `blocked`).  
- The acting agent should keep a **running log** (thread in `#cli-interface` or entries in `pr_status.md`) of: PR URL, branch, repo, last known automation state, and last known GitHub state.  
- **Escalation:** if merge is blocked, needs human action, or you **manually merge**, the human should **reply in the relevant Slack thread** (or ping the bot) with: what happened, new state, and any follow-up tasks. The CLI agent should **re-read** `#pull-requests` and the task thread before continuing work.

## Runtime: local vs server (flexible)

| Concern | Local (today) | Server (future) |
|--------|-----------------|-----------------|
| **WORKSPACE_PATH** | Your clone path | Deployed single root or per-repo paths; set explicitly in each task. |
| **Cursor CLI / `agent`** | Installed on the machine running the script | Same, on the server host or in an approved runner image. |
| **Containers / services** | Follow `scripts/fm_docker.sh` / `scripts/fm_services.sh` and **single runtime owner** in `design_docs/30_Releases/Runtime_Signup_Sheet.md` | Same policy; do not mix modes without documentation. |
| **Secrets** | `CURSOR_API_KEY`, `SLACK_BOT_TOKEN` in env or secure store | Same; prefer secret manager on server. |

State **RUNTIME** and **RUNTIME_NOTES** in every task so automations can branch (e.g. server-only heavy tests, no local GPU).

### Local runtime baseline (recommended default today)

Use this as the standard local launcher profile so the agent reliably watches both channels and keeps a machine-readable PR log:

```bash
export CURSOR_API_KEY=...
export SLACK_BOT_TOKEN=xoxb-...
export SLACK_TASK_CHANNEL='#cli-interface'
export SLACK_PR_CHANNEL='#pull-requests'
export CURSOR_AGENT_WORKSPACE='/home/<user>/Documents/python/finance_manager'
export CURSOR_AGENT_FORCE=1
export CURSOR_SLACK_TASK_MODE=both
export CURSOR_SLACK_PREFIX='!cursor'
python3 scripts/cursor_headless_slack_agent.py --workspace "$CURSOR_AGENT_WORKSPACE"
```

Runtime artifacts (local defaults):

- Slack cursors and tracked PR URLs: `./.cursor_slack_agent_state.json`
- PR event log JSONL: `./.cursor_pr_status_log.jsonl`

## Logs and cross-agent handoff

- **Upload or paste logs** in the **Slack thread** tied to the task (or a linked channel if files are large), with a one-line **summary** and **repo/path** so cloud agents can route work.  
- **Redirect by repo:** if the failure is in `finance_manager_api` vs `finance_manager_reflex`, say so explicitly; use **multi-repo-orchestration** when the fix spans repos and record dependencies in the plan.  
- Prefer **small, labeled artifacts** (snippet + path) over entire log dumps in chat.

## Related documents

- `design_docs/30_Releases/Slack_PR_Protocol_Compliance_Matrix.md`  
- `design_docs/30_Releases/pr_status.md`  
- `design_docs/10_Current_State/02_Documentation_Sync_Protocol.md`  
- `design_docs/30_Releases/Runtime_Signup_Sheet.md`  
- `.cursor/skills/roadmap-rollout-planning/SKILL.md`  
- `.cursor/skills/orchestration-manager/SKILL.md`  
- `.cursor/skills/design-docs-sync/SKILL.md`  

---

## Slack pinned message (copy-paste)

Use a short pin in `#cli-interface` with a link to this file in your default branch (adjust URL host/org/repo as needed):

```text
*Cursor CLI bridge — how to task the agent*

Full contract (read this first):
https://github.com/<org>/<repo>/blob/<default-branch>/design_docs/40_System_Design/12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md

*Quick rules*
• Task the bot with a mention or `!cursor` (see runner config).
• Say which *sub-repo* and *workspace path*; state *local* vs *server* runtime.
• Workflow: *roadmap-rollout-planning* → plan under `plans/<branch-name>/` → *orchestration-manager* → implement on a *feature branch* → PR → *design-docs-sync*.
• Post every PR to *#pull-requests*; follow Slack *and* GitHub; treat conflict/dirty as *blocked* until fixed.
• Optional PR log: `design_docs/30_Releases/pr_status.md`
```

Replace `<org>`, `<repo>`, and `<default-branch>` with your GitHub coordinates when you pin.
