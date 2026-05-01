# Strategic alignment — conflict resolutions (2026-05-01)

**Canonical strategy:** `plans/cursor/strategic-roadmap-reframe-53be/` + `plans/_governance/glossary.md`.

The table in the 2026-05-01 design-docs sync that listed “pending HitM direction” has been **resolved** using the suggested options. This file is now a short **audit trail** only.

| Original conflict | Resolution applied |
|-------------------|-------------------|
| `40_System_Design/07_Server_Runtime_and_Scaling.md` US-first §2 | **Rewritten** to PH-primary rollout, passive US / P-6; decoupled hosting mentions **web**; ZK paragraph references **S5**. |
| `20_Roadmap/Roadmap_Overview.md` golden rule (Reflex step 3) | **Replaced** with API → CLI → **web** pipeline + pointer to `finance_manager_web/README.md` (workspace path). |
| `30_Releases/Git_and_Workflow_Strategy.md` pipeline | Already web-centric; **trimmed** residual “historical docs may say Reflex” caveat. |
| `20_Roadmap/Phase_Execution_Triggers_and_Breakpoints.md` | **Marked historical-only** in title/banner; **Reflex → web** in gate text; roadmap refresh bullet points at strategic plan. |
| `20_Roadmap/orchestration_manager/*` + `Calendar_Phase1_*.md` | **`git mv`** to `20_Roadmap/_historical/` + **`_historical/README.md`**; `launch_prompt.md` paths updated; calendar packet banner added. |
| `API_Feature_Roadmap.md` / `Reflex_Feature_Roadmap.md` present tense | **Post-beta API** section uses **web**; Phase 3 / Rust sections **cross-link S5**; Reflex philosophy framed as historical with web rule. |

Further edits to strategy belong in the strategic plan and registry, not by re-expanding this list.
