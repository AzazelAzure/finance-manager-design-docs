# Finance Manager - Design Docs Dashboard

Welcome to the Finance Manager architecture and design vault. This vault holds **durable technical truth** — how the built system works. Roadmap sequencing lives in `strategy/`; plan operations live in `governance/`.

## Canonical planning and roadmap (read first)

- **Strategic truth (phases S1–S6, stages, triggers):** `strategy/strategic-roadmap-reframe-53be/README.md` — start here for what is active, what shipped, and what gates what.
- **Vocabulary and plan lifecycle:** `governance/glossary.md`, then `governance/README.md`.
- **Tactical plan portfolio:** `governance/plan_registry.md`.
- **Active stage execution (S1.B):** `plans/S1/S1.B/README.md` (parent workspace; not mirrored on VPS)

Historical `design_docs/20_Roadmap/_historical/` and `_historical/reflex_docs/` are **archive context only**; they do not override the strategic plan.

## 🧭 Vault Structure

### 1. 🏗️ Current State (tight beta era, S1)
Documentation on what currently exists, highlighting operational components and areas needing work. **Active stage: S1.B** — stage map and gates: `strategy/strategic-roadmap-reframe-53be/README.md`.
- [[10_Current_State/01_Runtime_Validation_Checklist|Runtime Validation Checklist]]
- [[10_Current_State/02_Documentation_Sync_Protocol|Documentation Sync Protocol]]
- [[_historical/10_Current_State/00_Alpha_Overview|Alpha Overview (historical archive)]]

### 2. 🧩 Component Architectures (The "How")
Deep-dives into the specific workings, structures, and payloads of each system component.
- [[api_docs/00_API_Overview|API Documentation]]
- [[cli_docs/00_CLI_Overview|CLI Documentation]]
- **Web (flagship):** implementation and env contract live in workspace `finance_manager_web/README.md` (sub-repo; HTTPS via proxy **:8443** per ecosystem `AGENTS.md`).
- [[_historical/reflex_docs/00_Reflex_Overview|Reflex Documentation (archived product — historical)]]
- [[rust_docs/00_Rust_Overview|Rust Middleware (Planned)]]

### 3. 🗺️ Roadmap (historical context)
Canonical rollout is **S1–S6** in the parent `strategy/strategic-roadmap-reframe-53be/` tree. Vault roadmap files are archived under `20_Roadmap/_historical/`.
- [[20_Roadmap/_historical/Roadmap_Overview|Roadmap Overview (historical frame + pointers)]]
- [[20_Roadmap/_historical/API_Feature_Roadmap|API Feature Roadmap (historical)]]

### 4. 🔒 Security & encryption
- [[00_Encryption_Strategy|Encryption Strategy (Zero-Knowledge)]]

### 5. 📦 Release operations (live ops artifacts)
VPS checkout sheet and handoff templates. Git/workflow **rules** are canonical in `governance/branching_guidelines.md` and `governance/deployment_protocol.md` (historical copies under `30_Releases/_historical/`).
- [[30_Releases/Runtime_Signup_Sheet|Runtime Signup Sheet]]
- [[30_Releases/Runtime_Owner_Handoff_Template|Runtime Owner Handoff Template]]
- [[30_Releases/Git_Owner_Handoff_Template|Git Owner Handoff Template]]

### 6. 📚 Research & durable specs
- [[50_Research_Backlog|Research Backlog]] — pointer; active research queue lives in `strategy/research/`
- [[00_Coding_Guidelines|Coding Guidelines]]
- [[01_Business_Vision|Business Vision]]
- [[02_Future_Risks|Future Risks]]
- [[03_Localization_Strategy|Localization Strategy]]

---
*Maintained jointly by Gemini (Architect) and Cursor (Implementer).*
