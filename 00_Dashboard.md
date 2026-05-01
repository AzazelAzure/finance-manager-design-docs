# Finance Manager - Design Docs Dashboard

Welcome to the Finance Manager architecture and design vault. This vault acts as the single source of truth for the system's current state, roadmap, versioning, and technical specifications.

## Canonical planning and roadmap (read first)

- **Strategic truth (phases S1–S6, stages, triggers):** `plans/cursor/strategic-roadmap-reframe-53be/README.md` — start here for what is active, what shipped, and what gates what.
- **Vocabulary and plan lifecycle:** `plans/_governance/glossary.md`, then `plans/_governance/README.md`.
- **Tactical plan portfolio:** `plans/_governance/plan_registry.md`.
- **Strategic alignment audit (resolved 2026-05-01):** `design_docs/10_Current_State/Strategic_doc_conflicts_pending_direction.md`
- **Active stage execution (S1.B):** `plans/cursor/s1b/README.md` (parent workspace; not mirrored on VPS)

Historical `design_docs/20_Roadmap/*` Phase 1 / Phase 2 / Track docs remain as **archive context** unless a file header says otherwise; they do not override the strategic plan.

## 🧭 Vault Structure

### 1. 🏗️ Current State (tight beta era, S1)
Documentation on what currently exists, highlighting operational components and areas needing work. **Active stage: S1.B** — stage map and gates: `plans/cursor/strategic-roadmap-reframe-53be/README.md`.
- [[10_Current_State/00_Alpha_Overview|System Architecture & Alpha Status]]

### 2. 🧩 Component Architectures (The "How")
Deep-dives into the specific workings, structures, and payloads of each system component.
- [[api_docs/00_API_Overview|API Documentation]]
- [[cli_docs/00_CLI_Overview|CLI Documentation]]
- **Web (flagship):** implementation and env contract live in workspace `finance_manager_web/README.md` (sub-repo; HTTPS via proxy **:8443** per ecosystem `AGENTS.md`).
- [[reflex_docs/00_Reflex_Overview|Reflex Documentation (archived product — historical)]]
- [[reflex_docs/01_Implementation_Plan|Reflex Implementation Plan (historical)]]
- [[rust_docs/00_Rust_Overview|Rust Middleware (Planned)]]

### 3. 🗺️ Roadmap & Phases
Canonical rollout is **S1–S6** in the parent `plans/` tree. Vault links below are context + historical phase docs.
- [[20_Roadmap/Roadmap_Overview|Roadmap Overview (historical frame + pointers)]]
- [[20_Roadmap/Phase_1_Alpha_Stabilization|Phase 1: Alpha Stabilization (historical)]]
- [[20_Roadmap/Reflex_Feature_Roadmap|Reflex Feature Roadmap (historical UX reference)]]
- [[20_Roadmap/API_Feature_Roadmap|API Feature Roadmap (Post-v1.0 Evolution)]]

### 4. 🔒 Security Architecture
- [[30_Security/00_Encryption_Strategy|Encryption Strategy (Zero-Knowledge)]]

### 5. 📦 Releases & Dependencies
Guidelines for cross-repo contracts, dependency locking, and versioning.
- [[30_Releases/Versioning_and_Contracts|Versioning and Contracts]]
- [[30_Releases/Dependency_Management|Dependency Management]]
- [[30_Releases/Git_and_Workflow_Strategy|Git and Workflow Strategy]]

### 6. 🤖 CLI / Slack / cloud agent bridge
How GitHub cloud agents and the headless Cursor CLI agent coordinate over Slack, PR gates, and runtimes.
- [[40_System_Design/12_Cursor_CLI_Slack_Cloud_Agent_Bridge|Cursor CLI, Slack, and Cloud Agent Bridge]]

---
*Maintained jointly by Gemini (Architect) and Cursor (Implementer).*