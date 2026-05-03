# Current state overview (design_docs mirror of strategic S1)

**Strategic source of truth:** `plans/cursor/strategic-roadmap-reframe-53be/` (Phase **S1**, Stage **S1.B** as of 2026-05-01). This page summarizes how `design_docs` describes the live system; where they diverge, the strategic plan and `governance/glossary.md` win.

The product is in **tight beta** on a **blue-green** VPS stack; flagship client is the **React web** app, not the archived Reflex tree.

## Component Status Summary

| Component | Status | Location | Notes |
| :--- | :--- | :--- | :--- |
| **API** | Operational (tight beta) | `finance_manager_api/` | Core logic, PostgreSQL, REST contracts; pairs with web on blue/green deploy. |
| **CLI** | Operational | `finance_manager_cli/` | Integration verification and scripting; still the baseline contract checker for many flows. |
| **Web (flagship)** | Tight beta / active | `finance_manager_web/` | React + TypeScript + Vite SPA; primary authenticated GUI; HTTPS via workspace proxy pattern on `:8443` (see web README + `design_docs/40_System_Design/05_Deployment_Strategy.md`). |
| **Reflex** | Archived (historical) | `finance_manager_reflex/` | Removed from production architecture 2026-04-30; repo retained for evidence only (`plans/cursor/strategic-roadmap-reframe-53be/00_strategic_context.md` §3.1). Reflex-specific docs under `design_docs/reflex_docs/` are historical. |
| **Rust Middleware** | Scaffold / deferred | `finance_manager_rust_middleware/` (and related design docs) | Serious ZK middleware work gated to **S5** in the strategic plan. |

## High-Level System Architecture (Current)

1. **The Core (API)**: Django REST backend serving JSON; business logic and persistence.
2. **The Clients (CLI / Web)**: CLI for automation and contract verification; **web** for end-user GUI. New UI work targets `finance_manager_web` under the API-first rules in `governance/` and workspace git rules.

## Deep Dives

For detailed architectural breakdowns of each component, refer to their specific documentation:

- [[../api_docs/00_API_Overview|API Architecture & Data Models]]
- [[../cli_docs/00_CLI_Overview|CLI Architecture & Commands]]
- [[../reflex_docs/00_Reflex_Overview|Reflex Architecture & UI State (archived product; historical)]]
- [[../rust_docs/00_Rust_Overview|Rust Middleware (strategic plan S5)]]