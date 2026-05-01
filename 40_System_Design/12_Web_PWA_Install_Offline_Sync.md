# Web PWA — Install, Offline, Resync (Flagship)

## Purpose

Record how the **flagship `finance_manager_web` installable PWA** (Advanced tier: offline writes, IndexedDB outbox, resync, forced client upgrade window) relates to **system design** and to **Android offline-first** vocabulary. Authoritative **decisions, rationale, and specs** live in the **ecosystem parent** repository under `plans/` (this vault is a submodule; paths below are relative to the parent checkout).

## Canonical plan material (parent repo)

| Topic | Path (parent `finance_manager/`) |
|-------|-----------------------------------|
| S1.B sprint handoff table | `plans/cursor/s1b/README.md` — section **Sprint activation index — PWA** (anchor `#pwa-sprint-activation-index`) |
| Sub-plan hub + §1.1–§1.7 decisions, §6 production bar | `plans/cursor/s1b/pwa-install-offline-sync-research/README.md` |
| Artifact index | `plans/cursor/s1b/pwa-install-offline-sync-research/RESEARCH_ARTIFACTS.md` |
| Browser matrix (D0) | `plans/cursor/s1b/pwa-install-offline-sync-research/D0_BROWSER_MATRIX.md` |
| API / outbox contract (D2) | `plans/cursor/s1b/pwa-install-offline-sync-research/D2_API_OUTBOX_CONTRACT.md` |
| Auth + offline session (D3) | `plans/cursor/s1b/pwa-install-offline-sync-research/D3_AUTH_OFFLINE_SESSION.md` |
| Smoke checklist + ADR stub (D4 / D4-exec) | `plans/cursor/s1b/pwa-install-offline-sync-research/D4_SMOKE_CHECKLIST_AND_ADR.md` |
| Install seed ~3mo, offline disclaimers, atomicity | `plans/cursor/s1b/pwa-install-offline-sync-research/SEEDING_OFFLINE_WINDOW_AND_ATOMICITY.md` |
| Client/API version window | `plans/cursor/s1b/pwa-install-offline-sync-research/API_VERSION_AND_CLIENT_WINDOW.md` |

## Strategic gates

- **S1.B exit** and **S1.C entry** language: `plans/cursor/strategic-roadmap-reframe-53be/validation_gates.md` (PWA bullet), `plans/cursor/strategic-roadmap-reframe-53be/phases/S1_public_beta_position.md` (W2 / W3 / W6 + exit criteria).
- **Portfolio row:** `plans/_governance/plan_registry.md` — `PLAN_RESEARCH_PWA_INSTALL_OFFLINE_SYNC_2026-05-01`.

## Alignment with Android sync (this vault)

Shared concepts: **outbox**, **idempotent replay**, **server authority**, **local durable state**. See [Android Offline-First Sync Architecture](./08_Android_Offline_First_Sync_Architecture.md). Web PWA implementation should **not** fork conflicting semantics; Android pull-forward (W6) references the same D2/D3 vocabulary as in the table above.

## Implementation status

**Research locks** (D0–D4 research + product notes such as seeding) are captured in the parent `plans/` tree; **execution** (API idempotency, web SW + IndexedDB, smoke on deployed `:8443`) is tracked there and in component repos when CPPR lands. Update this note when `design_docs` gains web-specific sequence diagrams or when behavior ships (then also use each subrepo `CHANGELOG.md` per CPPRD).
