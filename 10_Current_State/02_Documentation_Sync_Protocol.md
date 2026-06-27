# Documentation Sync Protocol

## Purpose
Keep `design_docs` aligned with implementation work so planning, architecture, and release decisions remain trustworthy.

**Roadmap and phase truth** live in `strategy/strategic-roadmap-reframe-53be/` (multi-year model, S1 stages, validation gates). **Plan operations** (authoring, lifecycle, registry, deployment) live in `governance/`. When sync touches sequencing or "what is current," update those plans first or cite them; use `design_docs/20_Roadmap/` for deep historical context and feature lists unless a file is explicitly refreshed.

## Trigger Conditions

Run documentation sync when any of the following occur:

- API contract changes (payload, endpoint behavior, auth expectations) — `finance_manager_api`
- React/Vite component behavior or route changes — `finance_manager_web`
- Django Celery task registration or beat schedule changes
- Phase scope, milestone order, or rollout assumptions change
- Cross-repo dependency or blocker status changes
- PR governance/protocol changes (agent roles, approval gates, mergeability rules)
- New governance doc added to `governance/` that supersedes a design_doc reference

## Sync Routine

1. **Capture Change Set**
   - What changed in code behavior
   - Which repo(s) changed
   - Which branch/PR(s) carry the change
   - What risks/assumptions shifted
2. **Map to Docs**
   - Current state: `design_docs/10_Current_State/`
   - Architecture: `design_docs/api_docs/`, `design_docs/reflex_docs/`, `design_docs/cli_docs/`
   - Roadmap and phases: **canonical** `strategy/strategic-roadmap-reframe-53be/`; **historical / feature backlog context** `design_docs/20_Roadmap/`
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

## Run-Through Example (Legal Pages + Signup Clickwrap, 2026-06-27)

- Change summary: API adds `tos_version`/`tos_accepted_at` fields on `AppProfile` (migration `0011`); Web adds `/privacy`, `/terms`, `/cookies` routes and signup clickwrap checkbox; Celery confirmation email task added.
- Repos touched: `finance_manager_api` (PR #42), `finance_manager_web` (PRs #66, #67).
- Branch/PRs: `agy/s1b/feat/legal-pages`, `agy/s1b/feat/signup-clickwrap`.
- Docs updated:
  - `design_docs/30_Releases/Runtime_Signup_Sheet.md` — new session opened post-deploy
  - `governance/plan_registry.md` — `PLAN_CROSS_LEGAL_PAGES_2026-06-27` and `PLAN_CROSS_SIGNUP_CLICKWRAP_2026-06-27` moved to Recently Completed
  - `strategy/current_status.md` §5 — status rows updated to ✅
- Remaining doc gaps:
  - API architecture doc for `AppProfile` model fields not updated.
- Follow-up trigger:
  - Run again if clickwrap version field (`tos_version` value) changes in production.
