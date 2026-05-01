# Strategic alignment — conflicts pending HitM direction

**Created:** 2026-05-01 (design-docs sync).  
**Canonical strategy:** `plans/cursor/strategic-roadmap-reframe-53be/` + vocabulary `plans/_governance/glossary.md`.

The items below are **intentionally not auto-rewritten** because they require a product/architecture decision (delete vs rewrite vs keep as marked history).

| Doc | Conflict | Suggested resolution options |
|-----|----------|------------------------------|
| `design_docs/40_System_Design/07_Server_Runtime_and_Scaling.md` §2 "Rollout Strategy" | States **US launch first, then PH**. Strategic context locks **PH-first** acquisition and US deferral behind P-6 (`00_strategic_context.md` §3.8). | Rewrite regional section to PH-primary + passive US; or replace section with a pointer to strategic plan only; or keep verbatim with a large "superseded" banner. |
| `design_docs/20_Roadmap/Roadmap_Overview.md` § "Rigid Feature Dependencies" | Three-step pipeline ends in **Reflex**; Reflex is **archived**; flagship GUI is **`finance_manager_web`**. | Replace step 3 with `web` + link `finance_manager_web/README.md`; or collapse section to "see governance API-first" only. |
| `design_docs/30_Releases/Git_and_Workflow_Strategy.md` § "Feature Rollout Pipeline" | Steps 4–6 are Reflex-centric; same as above. | Same as row above. |
| `design_docs/20_Roadmap/Phase_Execution_Triggers_and_Breakpoints.md` | Entire doc uses legacy Phase 1.5 / Phase 2 language and "block Reflex work" gates. | Mark file historical-only **or** refactor triggers onto S1-stage language + `strategic-roadmap-reframe-53be/validation_gates.md`. |
| `design_docs/20_Roadmap/orchestration_manager/*` + `Calendar_Phase1_Contract_and_UI.md` | Execution packets assume Reflex + legacy plan roots. | Archive subtree to `design_docs/20_Roadmap/_historical/` **or** add single README at `orchestration_manager/` stating read-only historical mirror. |
| `design_docs/20_Roadmap/API_Feature_Roadmap.md` + `Reflex_Feature_Roadmap.md` body | Present tense assumes Reflex scales to v1.0 as primary GUI. | Add banner + trim present tense **or** full rewrite against S1–S6 (larger effort). |

When you decide per row, delete the row from this file or move outcome to `02_Documentation_Sync_Protocol.md` run log.
