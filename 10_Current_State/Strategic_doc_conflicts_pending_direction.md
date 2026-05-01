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
| 2026-05-01 integration pass (post-huddle drift) | **Applied:** `Beta_Execution_Board.md` (S1.B supersession + Web lanes + Cycle 7), `Roadmap_Overview.md` (collapsed legacy Track narrative), `00_Dashboard.md` (S1.B + web pointers), `01_Runtime_Validation_Checklist.md` (web container logs + rollback), `Beta_Contract_Compatibility_Matrix.md` (web verification column), `08_Android_Offline_First_Sync_Architecture.md` (gate on web), `License_and_Release_Gates.md` Stream A wording, `api_docs/02_Data_Flow_and_Architecture.md`, `30_Releases/Runtime_Signup_Sheet.md` + `pr_status.md`, `05_Deployment_Strategy.md` (inactive-color workflow). |

Further edits to strategy belong in the strategic plan and registry, not by re-expanding this list.
