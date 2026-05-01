# Documentation Sync Protocol

## Purpose
Keep `design_docs` aligned with implementation work so planning, architecture, and release decisions remain trustworthy.

**Roadmap and phase truth** live in `plans/cursor/strategic-roadmap-reframe-53be/` (multi-year model, S1 stages, validation gates). **Plan operations** (authoring, lifecycle, registry, deployment) live in `plans/_governance/`. When sync touches sequencing or "what is current," update those plans first or cite them; use `design_docs/20_Roadmap/` for deep historical context and feature lists unless a file is explicitly refreshed.

## Trigger Conditions

Run documentation sync when any of the following occur:

- API contract changes (payload, endpoint behavior, auth expectations)
- Reflex flow/state behavior changes
- CLI command semantics or output behavior changes
- Phase scope, milestone order, or rollout assumptions change
- Cross-repo dependency or blocker status changes
- PR governance/protocol changes (Slack automation routing, approval gates, mergeability rules)

## Sync Routine

1. **Capture Change Set**
   - What changed in code behavior
   - Which repo(s) changed
   - Which branch/PR(s) carry the change
   - What risks/assumptions shifted
2. **Map to Docs**
   - Current state: `design_docs/10_Current_State/`
   - Architecture: `design_docs/api_docs/`, `design_docs/reflex_docs/`, `design_docs/cli_docs/`
   - Roadmap and phases: **canonical** `plans/cursor/strategic-roadmap-reframe-53be/`; **historical / feature backlog context** `design_docs/20_Roadmap/`
   - Release operations: `design_docs/30_Releases/`
3. **Apply Focused Edits**
   - Update existing files first.
   - Add new file only when no existing document is a good fit.
4. **Cross-Check**
   - Ensure docs describe current behavior, not intended behavior.
   - Ensure dependency order remains consistent with API-first rule.

## Required Output Template

```markdown
## Documentation Sync Report
- Change summary:
- Repos touched:
- Docs updated:
- Remaining doc gaps:
- Follow-up trigger:
```

## Run-Through Example (Transaction Calendar Program)

- Change summary: API introduces calendar aggregates; CLI adds verification path; Reflex calendar consumes contract.
- Repos touched: API, CLI, Reflex.
- Branch/PRs: `feature/...` branches with linked per-repo PRs.
- Docs updated:
  - `design_docs/30_Releases/Subagent_Multi_Repo_Routine.md`
  - `design_docs/20_Roadmap/Phase_Execution_Triggers_and_Breakpoints.md`
- Remaining doc gaps:
  - Endpoint-level payload examples in API architecture docs.
- Follow-up trigger:
  - Run doc sync again when endpoint contracts are finalized in code.
