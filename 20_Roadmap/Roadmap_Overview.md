# Roadmap Overview

> **HISTORICAL REFERENCE ONLY (as of 2026-04-30).** The canonical roadmap is now `plans/cursor/strategic-roadmap-reframe-53be/`. The S1–S6 Phase model supersedes the `Phase 1`/`Phase 2`/Track A–E structure documented here. This file is preserved for historical context and should not be used as the source of truth for ongoing work.
>
> **Why retired:** The dual-persona "Average Joe vs Finance Bro" framing was a target list, not a strategy. Post-beta huddle 2026-04-30 reframed to PH-first wedge ("safe-to-spend for thin-margin households"), 4-Stage breakdown of S1, JS pivot completed (Reflex archived), per-product launch-state model, and revenue path narrowed (ads + user-data sales rejected). See `plans/cursor/strategic-roadmap-reframe-53be/00_strategic_context.md` §3 + §4 for full lock list.

### Where to work now (canonical)

| Need | Path |
|------|------|
| Current phase, stages, calendar estimates | `plans/cursor/strategic-roadmap-reframe-53be/README.md` |
| Locked decisions, personas, rejections | `plans/cursor/strategic-roadmap-reframe-53be/00_strategic_context.md` |
| Quantitative gates | `plans/cursor/strategic-roadmap-reframe-53be/validation_gates.md`, `kill_commit_gates.md` |
| Vocabulary (`Phase` = S1…, `Stage` = S1.A…) | `governance/glossary.md` |
| Authoring / executing tactical plans | `governance/README.md` → `plan_registry.md` |
| Resolved alignment log (2026-05-01) | `design_docs/10_Current_State/Strategic_doc_conflicts_pending_direction.md` |

This document outlines the **historical** high-level trajectory (pre-S1-reframe vocabulary), tracking evolution from local Alpha toward hosted beta. **Do not** use its Phase 1/2/Track structure for new execution planning.

## Terminology and Execution Model

- **Phase / Stage:** **Canonical** meanings are **Phase S1…S6** and **Stage S1.A…** (`governance/glossary.md`). Older prose in this vault may say “Phase 1/2”; treat that as **historical** unless the file has an explicit banner.
- **Program increment:** A large body of work made of epics, implementation plans, and validation loops—measured in weeks/months, not in a single chat turn or one commit.
- **Epic:** A major body of work inside a **stage** or legacy program block (for example, OAuth rollout, dashboard parity, CI/CD baseline).
- **Task:** A scoped implementation unit that can usually be completed in one focused work session.
- **Execution Rule:** We complete phases by iterating through many tasks and epics until exit criteria are met. Phases can overlap when dependencies permit.

## Rigid feature dependencies (API-first pipeline)

To prevent clients and the backend from drifting out of sync, **new work** follows this pipeline (see also `governance/` and workspace `finance_manager_web/README.md`):

1. **API first:** No GUI or CLI feature ships until the API contract and payloads are defined and stable for the slice.
2. **CLI verification:** Integrate and test via `finance_manager_cli` as the baseline integration path.
3. **Web UI:** Only after CLI verification, implement in **`finance_manager_web`** (React SPA). Reflex remains archived.

*If a feature requires a data model change, halt GUI work until API and CLI are updated and verified.*

---

## Legacy “Phase 1 / Phase 2 / Tracks A–E” narrative (retired in place)

The long-form sections that used to live here (Alpha → Reflex MVP → Beta hosting → parallel Tracks A–E) described **pre-2026-04-30** vocabulary. They are **not** updated line-by-line in this file so we avoid maintaining two roadmaps.

| Legacy label (this vault) | Canonical replacement |
|---------------------------|------------------------|
| Phase 1 / Alpha stabilization | **S1.A** (complete); detail in `Phase_1_Alpha_Stabilization.md` (historical banner). |
| Phase 2 / Beta prep & hosting | **S1.A** hosting slice + **S1.B** cleanup/research; detail in `Phase_2_Beta_Preparation.md` (historical banner). |
| Track A (Reflex feature expansion) | **`finance_manager_web`** feature work under API-first rules; see `API_Feature_Roadmap.md` + strategic **S1**–**S3**. |
| Track B (ZK / Rust magnum opus) | Strategic **S5**; `finance_manager_rust_middleware` scaffold only until gates open. |
| Track C (monetization / SaaS) | Strategic **S1.C** onward; `01_unit_economics_and_costs.md`; no ads / no user-data sales (locked). |
| Track D (mobile) | Strategic **S3**; Android pull-forward begins **S1.B** per `00_strategic_context.md`. |
| Track E (desktop) | **Concept** stream; see strategic plan desktop note. |

**Operational status today:** tight invite **beta** on VPS blue-green (**web** + API), **S1.B** active, distribution **blocked** until **S0** drift items close (`plans/S1/S1.B/drift-cleanup/`).

For feature depth, see [[20_Roadmap/Planned_Features|Planned Features List]]. For gate vocabulary (historical file, do not override strategic plan), see [[20_Roadmap/Phase_Execution_Triggers_and_Breakpoints|Phase Execution Triggers and Breakpoints]].