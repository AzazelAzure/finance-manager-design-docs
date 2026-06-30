# Finance Manager — Design Docs Dashboard

Welcome to the **active system design** vault. This repository documents how the built system works — API contracts, CLI architecture, Rust middleware plans, and cross-cutting system design.

**Out of scope here (private parent workspace):** roadmap sequencing (`strategy/`), plan operations (`governance/`, `plans/`), runtime signup sheets, and agent handoff templates.

## Vault structure

### Guidelines & encryption

- [[00_Coding_Guidelines|Coding Guidelines]]
- [[00_Encryption_Strategy|Encryption Strategy (Zero-Knowledge)]]

### Component architectures

Deep-dives into each system component's structure, contracts, and behavior.

- [[api_docs/00_API_Overview|API Documentation]]
- [[web_docs/00_Web_Overview|Web Documentation (flagship SPA)]]
- [[cli_docs/00_CLI_Overview|CLI Documentation]]
- [[rust_docs/00_Rust_Overview|Rust Middleware (planned)]]
- **Implementation README:** `finance_manager_web/README.md` (env, lanes, Docker — HTTPS via proxy **:8443** when deployed)

### System design (`40_System_Design/`)

Cross-cutting architecture and operational design.

- [[40_System_Design/Authentication|Authentication]]
- [[40_System_Design/05_Deployment_Strategy|Deployment Strategy]]
- [[40_System_Design/06_Logging_and_Support|Logging and Support]]
- [[40_System_Design/07_Server_Runtime_and_Scaling|Server Runtime and Scaling]]
- [[40_System_Design/08_Android_Offline_First_Sync_Architecture|Android Offline-First Sync]]
- [[40_System_Design/12_Web_PWA_Install_Offline_Sync|Web PWA Install & Offline Sync]]
- [[40_System_Design/Beta_Security_Posture_And_Rust_Middleware_Seam|Beta Security Posture & Rust Middleware Seam]]

## Parent workspace pointers (not wikilinked — private repos)

When the full ecosystem is cloned locally, these live alongside this vault:

| Topic | Parent path |
| :--- | :--- |
| Strategic roadmap (S1–S6) | `strategy/strategic-roadmap-reframe-53be/README.md` |
| Governance vocabulary & plan registry | `governance/glossary.md`, `governance/plan_registry.md` |
| Runtime signup sheet & handoffs | `governance/Runtime_Signup_Sheet.md`, `governance/*_Owner_Handoff_Template.md` |
| Active stage execution (S1.B) | `plans/S1/S1.B/README.md` |

---
*Maintained jointly by Gemini (Architect) and Cursor (Implementer).*
