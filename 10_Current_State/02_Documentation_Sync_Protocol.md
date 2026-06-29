# Documentation Sync Protocol

## Purpose
Keep `design_docs` aligned with implementation work so architecture and technical state remain trustworthy.

## Boundary (post-2026-06-29 restructure)

| Area | Canonical home | design_docs role |
|------|----------------|------------------|
| Roadmap / phases / gates | `strategy/strategic-roadmap-reframe-53be/` | Historical context only (`20_Roadmap/_historical/`) |
| Plan ops / deployment rules | `governance/` (`plan_registry.md`, `branching_guidelines.md`, `deployment_protocol.md`) | No duplicate rule docs; retired copies under `30_Releases/_historical/` |
| Living strategy state | `strategy/` (`current_status.md`, `anomalies/`, `research/`, `projections/`, etc.) | Cross-link when sync touches sequencing |
| How the system works | `design_docs/` (this vault) | API/Web/CLI/Rust architecture, current-state checklists, release ops templates |
| VPS runtime checkout | `design_docs/30_Releases/Runtime_Signup_Sheet.md` | Human log; **live** VPS state comes from `scripts/vps_state.sh` (not this file) |

## Trigger Conditions

Run documentation sync when any of the following occur:

- Django API endpoint or serializer contract changes (`finance_manager_api`)
- React/Vite component behavior or route changes (`finance_manager_web`)
- Celery task registration or beat schedule changes
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
   - Architecture: `design_docs/api_docs/`, `design_docs/cli_docs/`, `design_docs/rust_docs/`
   - Archived Reflex UX reference: `design_docs/_historical/reflex_docs/`
   - Roadmap and phases: **canonical** `strategy/strategic-roadmap-reframe-53be/`; **historical** `design_docs/20_Roadmap/_historical/`
   - Release operations: `design_docs/30_Releases/` (signup sheet + handoff templates)
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
