# Cursor CLI ↔ Slack ↔ Cloud Agent Bridge

## Purpose

Single manifest for **GitHub cloud agents**, **CI/automation**, and **humans** describing how to task the headless **Cursor CLI agent** over Slack, which workflows to run, how to stay aligned with **git/PR policy**, and how to choose a **runtime** (local workstation vs future server). Cloud agents should treat this file as the operational contract; pin a short summary in `#cli-interface` and link here.

**Canonical path (this repo):** `design_docs/40_System_Design/12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md`

**Headless Slack intermediary (preferred — Cursor PA):** `~/CursorAgent/headless-cursor-agent/` — Slack Bolt **Socket Mode** (`scripts/cursor_slack_runner.py`) + `bin/agent-daemon` / `bin/agent-prompt`; tokens in `**~/.secrets/mma_companion/slack.env`**. In Slack the app is shown as **Cursor PA**. This is HitM’s **primary** path for Cursor-backed task intake and **threaded** `chat.postMessage` replies (replaces the older poll-only bridge below for that role).

**Supplementary / optional poller:** `scripts/cursor_headless_slack_agent.py` (in this ecosystem repo) — HTTP polling; useful for `#pull-requests` tailing or legacy setups; **not** the default for threaded `@` / DM agent work when Cursor PA is online.

**Rigid local boundary:** what may and may not be sent through PA (local hardware + headless Cursor) lives in `**~/CursorAgent/headless-cursor-agent/docs/CURSOR_PA_AGREEMENT.md`** — pins in Slack should reference that path (or your hosted mirror if you add one).

> **⚠️ Pipeline vs Ad-Hoc (2026-05-04):** This document covers **Cursor PA ad-hoc tasking** via `#cli-interface`. For the governed **sprint pipeline** (four-agent model with `#sprint-queue` → `#review-queue` → `#hitm-gate`), see `14_Inter_Agent_Message_Relay_and_Ownership_Contract.md` and `governance/agent_workspace_isolation.md`. Cursor PA remains available for quick unplanned work outside the pipeline.

## Slack channels


| Channel                                              | Role                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `#cli-interface`                                     | **Cursor PA (default):** `@Cursor PA` in channel queues work; PA posts **threaded** replies via Socket Mode (`~/CursorAgent/headless-cursor-agent/`). DMs follow PA rules (`CURSOR_PA_DM_PREFIX_*`, etc.—see that README). **Do not** confuse with the legacy finance_manager **poll** runner (`scripts/cursor_headless_slack_agent.py`), which used `CURSOR_SLACK_PREFIX` / mention—only relevant if you still run that script **instead of or beside** PA. |
| `#pull-requests`                                     | **Required** for PR lifecycle: announce PRs (repo, branch, URL), read automation authorization, **reconcile with live GitHub** mergeability and checks. See `design_docs/30_Releases/Slack_PR_Protocol_Compliance_Matrix.md`.                                                                                                                                                                                                                                |
| `#beta-incidents` *(recommended; create when ready)* | **Bug / incident intake:** one top-level post per `incident_id` (or digest + thread), normalized fields from `15_Beta_Week_Incident_Triage_and_Human_Gated_Autofix_Contract.md`. Keeps triage out of `#cli-interface` and PR noise out of incidents. Automation may post here before in-app queue ships.                                                                                                                                                     |


**Monitoring today:** Cursor PA (Socket) listens where the Slack app is invited; **Cursor PA + human** should treat `#cli-interface` as task intake and `#pull-requests` as PR lifecycle. The optional finance_manager **poller** can still watch both channels if you run it for PR log tailing—see supplementary row above. When `#beta-incidents` exists, treat it as **human-first** until webhook wiring lands; do not merge PR authorization with incident flood.

**Workspace hygiene (HitM):** Retire or archive stale channels, align bot posting permissions, pin the “pinned message” block at the end of this doc in `#cli-interface`, and ensure PR announcements actually land in `#pull-requests` per `Slack_PR_Protocol_Compliance_Matrix.md`.

### `#cli-interface` — single pin, no absolute paths in history

**Target:** The channel should expose **no** host fingerprints (`/home/...`, Windows `C:\Users\...`, etc.) in **message history**. **One** canonical **pinned** item carries the policy (copy from § “Pin for `#cli-interface`” below). Day-to-day PA work happens in **threads** (or new `@Cursor PA` top-levels), not in a pile of static policy posts.

**Cleaning up Slack (operator, not PA):**

1. **Delete** old messages that contain absolute paths (Slack UI per message, or bulk tools if your tier supports them).
2. If history is too noisy to sanitize, **archive** the old channel, **create a new `#cli-interface`**, invite **Cursor PA** + team, add **only** the single pin from this doc, and leave the channel top-level empty except for live tasks.
3. **Re-pin** whenever this doc’s pin text changes; remove superseded pins so **one** pin remains.

**Do not** use Cursor PA to “wipe” Slack history—use Slack’s own controls.

## Message format (task prompt to the CLI agent)

Slack is lossy for structure; use a **consistent, copy-paste block** so the agent (and you) can parse intent. Recommended fields (omit unknowns, never invent repo names):

```text
@<bot> !cursor
REPO: <finance_manager_api | finance_manager_web | finance_manager_cli | finance_manager_android>
WORKSPACE_PATH: <tilde path — see below; do not paste /home/... in Slack>
RUNTIME: <local | server>
RUNTIME_NOTES: <e.g. "container owner: team X" or "no docker until approved" — free text>
SKILLS: roadmap-rollout-planning → orchestration-manager → design-docs-sync; pr-ops-merge-readiness; multi-repo-orchestration (if cross-repo)
PLAN_ROOT: <after planning: plans/<Phase>/<Stage>/<sub-plan>/ — must match governed plan layout; see governance/README.md>
TASK: |
  <What to achieve; constraints; out of scope.>
```

**WORKSPACE_PATH in Slack (privacy + portability)**

- **Do not** post real machine paths such as `/home/<username>/...` in Slack. Treat the channel as **not fully trusted** for filesystem fingerprinting.
- **Do** use **tilde paths** relative to the operator account on the machine where the headless agent runs, for example:
  - Ecosystem parent: `**~/Documents/python/finance_manager`**
  - Sub-repo checkout (typical): `**~/Documents/python/finance_manager/finance_manager_web**`, `**.../finance_manager_api**`, etc.
- The headless agent / Cursor session is expected to resolve `**~**` the same way your login shell does on that workstation, so docs and repos under those paths remain discoverable **without** exposing `/home/...` in chat.

**Notes**

- **REPO** must match the **sub-repo** you intend to commit in (one PR scope per repo; split work per `design_docs/30_Releases/Git_Policy_Quickstart.md` and workspace rules).
- **SKILLS** are **Cursor skill names** (folders under `.cursor/skills/`), not Slack slash commands. In headless mode the agent should **open and follow** the matching `SKILL.md` files. “`/roadmap-rollout-planning`” in chat means: execute the **roadmap-rollout-planning** skill; same for **orchestration-manager** and **design-docs-sync**.

## Standard workflow (required order)

1. **roadmap-rollout-planning**
  - Produce an **execution-ready** plan under `plans/<Phase>/<Stage>/<sub-plan>/` (one directory per batch; git branch follows `governance/branching_guidelines.md`).  
  - Include phases, checkpoints, validation gates, and explicit **exit criteria** before implementation continues; register in `governance/plan_registry.md` when status advances beyond `draft`.
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


| Concern                      | Local (today)                                                                                                                                                                   | Server (future)                                                                            |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| **WORKSPACE_PATH**           | In Slack use `**~/Documents/python/finance_manager`** or `**~/Documents/python/finance_manager/<sub_repo>**` — not `/home/...`. On the runner host, `~` is the operator’s home. | Deployed server: same convention with the appropriate home-relative path in each task.     |
| **Cursor CLI / `agent`**     | Installed on the machine running the script                                                                                                                                     | Same, on the server host or in an approved runner image.                                   |
| **Containers / services**    | Follow `scripts/fm_docker.sh` / `scripts/fm_services.sh` and **single runtime owner** in `design_docs/30_Releases/Runtime_Signup_Sheet.md`                                      | Same policy; do not mix modes without documentation.                                       |
| **Secrets**                  | `CURSOR_API_KEY`, `SLACK_BOT_TOKEN` in env or secure store                                                                                                                      | Same; prefer secret manager on server.                                                     |
| **Runtime bundle migration** | Build artifacts with `scripts/server/create_runtime_bundle.sh`; optional single-step push with `scripts/server/push_runtime_bundle.sh` for VPS handoff testing.                 | Prefer artifact push + `scripts/server/verify_release_manifest.sh` over ad-hoc host edits. |


State **RUNTIME** and **RUNTIME_NOTES** in every task so automations can branch (e.g. server-only heavy tests, no local GPU).

### Control plane vs execution plane

Use cloud agents as the **control plane** (planning/orchestration) and a host-local runner as the **execution plane** for runtime operations. This avoids depending on direct cloud-agent SSH into live hosts while keeping auditability and scripted safety gates.

Reference contract: `design_docs/40_System_Design/13_Server_Runtime_Agent_Operations_Contract.md`.
Server install/runbook details: `deploy/SERVER_BETA_INSTALL.md`.

### Local runtime baseline (recommended default today)

Use this as the standard local launcher profile so the agent reliably watches both channels and keeps a machine-readable PR log:

```bash
export CURSOR_API_KEY=...
export SLACK_BOT_TOKEN=xoxb-...
export SLACK_TASK_CHANNEL='#cli-interface'
export SLACK_PR_CHANNEL='#pull-requests'
export CURSOR_AGENT_WORKSPACE="$HOME/Documents/python/finance_manager"
export CURSOR_AGENT_FORCE=1
export CURSOR_SLACK_TASK_MODE=both
export CURSOR_SLACK_PREFIX='!cursor'
python3 scripts/cursor_headless_slack_agent.py --workspace "$CURSOR_AGENT_WORKSPACE"
```

Runtime artifacts (local defaults):

- Slack cursors and tracked PR URLs: `./.cursor_slack_agent_state.json`
- PR event log JSONL: `./.cursor_pr_status_log.jsonl`
- Last full agent run (stdout/stderr, not posted to Slack): `./.cursor_slack_agent_last_run.log` (ignored by `*.log` in the parent gitignore)

**Long output:** the runner posts a short header in the task thread, then **splits** agent stdout/stderr into multiple thread messages (default `CURSOR_SLACK_MAX_MESSAGE_CHARS=2400`) so Slack does not clip mid-status. Set `CURSOR_SLACK_THREAD_TASKS=1` to allow `!cursor` / mention tasks in **thread replies** (default: only top-level channel messages start tasks).

### Thread task reliability notes

Slack thread replies are polled from both channel history and explicit thread-reply endpoints to avoid missing in-thread task prompts. Keep:

- `CURSOR_SLACK_TOP_LEVEL=1`
- `CURSOR_SLACK_THREAD_TASKS=1`

for top-level-first behavior plus explicit thread task support.

## Addendum: Concurrent Cursor work (single `agent-daemon`)

**Intent:** Operators sometimes want **Slack-driven PA** (`bin/agent-prompt` → `headless-cursor-agent` daemon) **and** ad-hoc **CLI `cursor-agent` runs** at the same time, **without** standing up a second daemon or second socket.

### How the headless daemon behaves (truth path)

- The workstation install under `~/CursorAgent/headless-cursor-agent/` keeps **one** Unix socket and **one** persisted Cursor chat id (see `daemon/agentd.py` + `bin/agent-daemon`).
- Requests through **`agent-prompt` are handled serially** (one blocking `cursor-agent` child at a time per daemon process). Heavy CLI jobs do not “multiplex” through that socket—they **queue behind** the current Slack-invoked turn.

### Running concurrent work **without** a second daemon

You **may** overlap work by letting Slack keep using **`agent-prompt`** while a human or script invokes **`cursor-agent` directly** in a terminal:

- **Different concern / repo / branch:** safest overlap—e.g. PA on `headless-cursor-agent` workspace while CLI targets `~/Documents/python/finance_manager/finance_manager_web` on a feature branch.
- **Same checkout / same branch:** treat as **high collision risk** (two agents editing the same files, migrations, formatter fights, `git` lock races). Prefer **serialization** (wait for PA to finish) or **isolation** below.

### Isolation patterns (pick one when overlap is unavoidable)

| Pattern | When to use |
| --------|------------|
| **`cursor-agent --worktree`** | Need parallel edits in the **same repo** without sharing a working tree; Cursor creates an isolated git worktree under `~/.cursor/worktrees/...` per agent docs. |
| **Separate clone / path** | Long-lived second workspace (e.g. `finance_manager_web` vs `finance_manager_web-2`) when worktrees are awkward. |
| **Different sub-repos** | Natural split for HitM (`finance_manager_api` vs `finance_manager_web`)—still avoid touching the **same** generated paths from two agents simultaneously. |

### Shared limits (even with isolation)

- **Same `CURSOR_API_KEY`:** concurrent `cursor-agent` processes **share account quota and rate limits**; bursts can queue or fail at the API layer—plan retries and backoff in runners.
- **Slack + CLI both “driving” one task:** add explicit **human ownership** in the Slack thread (who runs the CLI turn) so two agents do not undo each other’s intent.

### What **not** to assume

- **`cursor --chat`** is a **UI launcher** (“standalone chat window”), **not** a scriptable substitute for `cursor-agent --print` / `antigravity chat`—do not document automation around `--chat` for model/prompt flags unless Cursor later exposes them in `cursor --help`.
- A **second systemd unit** for another socket is **optional complexity**—only add it if you truly need **two long-lived Slack-attached sessions** with separate chat history; for “CLI burst while PA is idle or on another repo,” **direct `cursor-agent`** is enough.

## Logs and cross-agent handoff

- **Upload or paste logs** in the **Slack thread** tied to the task (or a linked channel if files are large), with a one-line **summary** and **repo/path** so cloud agents can route work.  
- **Redirect by repo:** if the failure is in `finance_manager_api` vs `finance_manager_web` (flagship; Reflex archived 2026-04-30), say so explicitly; use **multi-repo-orchestration** when the fix spans repos and record dependencies in the plan.  
- Prefer **small, labeled artifacts** (snippet + path) over entire log dumps in chat.

## Related documents

- `design_docs/40_System_Design/14_Inter_Agent_Message_Relay_and_Ownership_Contract.md` — **versioned relay envelope**, `correlation_id`, four-entity ownership, fail-closed parsing across Slack/GitHub/models  
- `design_docs/30_Releases/Slack_PR_Protocol_Compliance_Matrix.md`  
- `design_docs/30_Releases/pr_status.md`  
- `design_docs/10_Current_State/02_Documentation_Sync_Protocol.md`  
- `design_docs/30_Releases/Runtime_Signup_Sheet.md`  
- `.cursor/skills/roadmap-rollout-planning/SKILL.md`  
- `.cursor/skills/orchestration-manager/SKILL.md`  
- `.cursor/skills/design-docs-sync/SKILL.md`

---

## Slack pinned messages (copy-paste)

Pin **one block per channel**. Use `**~`-relative paths** in pin text (same home layout assumption as in prompts). **Hard boundary** for PA (allowed / forbidden on local hardware):

`~/CursorAgent/headless-cursor-agent/docs/CURSOR_PA_AGREEMENT.md`

**Why not use the parent `finance-manager-ecosystem` URL for PA-only files?** The PA agreement and runner README live on the **workstation** under `~/CursorAgent/` (not necessarily a public GitHub URL). **Pins and Slack prompts** should use `**~`-style paths only** (e.g. `~/CursorAgent/...`, `~/Documents/python/finance_manager/...`) — not `/home/...` — so Slack history does not fingerprint the machine.

**Design bridge (ecosystem-wide workflow):** still use the design-docs URL below when you need the full GitHub ↔ Slack ↔ agent workflow narrative.

[https://github.com/AzazelAzure/finance-manager-design-docs/blob/main/40_System_Design/12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md](https://github.com/AzazelAzure/finance-manager-design-docs/blob/main/40_System_Design/12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md)

---

### Pin for `#cli-interface` (task intake — Cursor PA)

**This block is the only long-lived policy post** for the channel—keep top-level history otherwise free of path dumps and duplicate policy. Use **threads** for `@Cursor PA` work.

```text
*#cli-interface — Cursor PA*

PA runs on *this* machine: `~/CursorAgent/headless-cursor-agent/` (Socket Mode). Uses your Cursor quota and local hardware.

*Agreement (read first)* — `~/CursorAgent/headless-cursor-agent/docs/CURSOR_PA_AGREEMENT.md`

*How to task PA*
• **Channels:** `@Cursor PA` only — plain top-level lines do not queue work.
• **DMs:** every line queues unless `CURSOR_PA_DM_PREFIX_REQUIRED=1` (then lines must start `!pa `).
• **Structured block** (Slack — **tilde paths only**, never `/home/...`):

REPO: <finance_manager_api | finance_manager_web | …>
WORKSPACE_PATH: ~/Documents/python/finance_manager  *(or ~/Documents/python/finance_manager/<sub_repo>)*
RUNTIME: local
TASK: <what you want>

*Noise control* — put `#pull-requests` channel id in `CURSOR_PA_BLOCK_CHANNEL_IDS` on the runner (agreement §5).

*Do / don’t* — scoped repo work yes; secrets, `sudo`, prod deploy, disk wipes, exfil: **no** (agreement §3).

Docs: `~/CursorAgent/headless-cursor-agent/README.md` · GitHub bridge: `12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md` (design-docs repo)
```

---

### Pin for `#pull-requests` (PR lifecycle — human + GitHub truth)

```text
*#pull-requests — PRs, not generic PA coding*

Post: repo, branch, full PR URL. Slack automation is *not* merge authority.

*Gates*
• GitHub mergeability + required checks + review — must be green / reconciled before merge.
• Treat `CONFLICTING` / dirty as *blocked* until fixed.

PA may *summarize* or *draft* in a **thread** only if someone `@Cursor PA` here — still rare; prefer `#cli-interface`. If you do, use **tilde** `WORKSPACE_PATH` values only (see § message format). Recommended: put this channel’s id in **`CURSOR_PA_BLOCK_CHANNEL_IDS`** on the runner host so automated PR traffic never accidentally invokes PA.

Agreement for what PA must never do: `~/CursorAgent/headless-cursor-agent/docs/CURSOR_PA_AGREEMENT.md`
Workflow detail: design-docs `12_Cursor_CLI_Slack_Cloud_Agent_Bridge.md`
```

---

### Pin for `#beta-incidents` (optional — bug / incident intake)

```text
*#beta-incidents — incident posts*

One top-level post per incident (or digest + thread). Include: severity, surface, repro, pseudonymous user id if available — *no* ledger dumps or secrets.

Cursor PA is *not* the primary intake path here unless HitM explicitly asks; PA follows the same *local hardware* agreement:

`~/CursorAgent/headless-cursor-agent/docs/CURSOR_PA_AGREEMENT.md`

Triage contract: design-docs `40_System_Design/15_Beta_Week_Incident_Triage_and_Human_Gated_Autofix_Contract.md`
```

**Private repo:** GitHub links require login and access or they may **404** in some clients.