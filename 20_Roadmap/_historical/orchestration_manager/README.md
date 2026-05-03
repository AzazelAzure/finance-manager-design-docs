# Orchestration Manager Input Pack

> **Historical execution pack (pre–plan governance, pre–web flagship).** Located under `20_Roadmap/_historical/` since 2026-05-01. These artifacts targeted API + **Reflex**. Reflex is **archived**; new orchestration should anchor on `plans/cursor/<phase-stage>/<sub-plan>/` per `governance/README.md` and the strategic plan. **Read-only** reference for packet structure.

Use this folder as context when reviewing how a prior `/orchestration-manager` run was structured for the API + Reflex feature rollout.

## Files
- `execution_manifest.md`: master execution graph, sequencing, and completion contract.
- `launch_prompt.md`: copy/paste launch prompt for a new orchestration-manager agent.
- `validation_gates.md`: deterministic checks and acceptance gates by task.
- `task_packets/T01_...` through `task_packets/T05_...`: one delegation packet per plan todo.

## Launch Order
1. Open `execution_manifest.md`.
2. Launch agent with `launch_prompt.md`.
3. Instruct agent to execute task packets in numeric order, honoring dependencies and gates.
4. Require gate evidence before marking each packet complete.

## Constraints
- Do not edit `/home/pproctor/.cursor/plans/api_reflex_feature_rollout_1f0a0342.plan.md`.
- Preserve repo boundaries:
  - API: `finance_manager_api/`
  - Reflex: `finance_manager_reflex/`
  - Docs: `design_docs/`
