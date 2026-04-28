# Finance Manager - Design Docs Dashboard

Welcome to the Finance Manager architecture and design vault. This vault acts as the single source of truth for the system's current state, roadmap, versioning, and technical specifications.

## 🧭 Vault Structure

### 1. 🏗️ Current State (Alpha)
Documentation on what currently exists, highlighting operational components and areas needing work.
- [[10_Current_State/00_Alpha_Overview|System Architecture & Alpha Status]]

### 2. 🧩 Component Architectures (The "How")
Deep-dives into the specific workings, structures, and payloads of each system component.
- [[api_docs/00_API_Overview|API Documentation]]
- [[cli_docs/00_CLI_Overview|CLI Documentation]]
- [[reflex_docs/00_Reflex_Overview|Reflex Documentation]]
- [[reflex_docs/01_Implementation_Plan|Reflex Implementation Plan (ZK Integration)]]
- [[rust_docs/00_Rust_Overview|Rust Middleware (Planned)]]

### 3. 🗺️ Roadmap & Phases
The step-by-step rollout plan from Alpha to full implementation.
- [[20_Roadmap/Roadmap_Overview|Roadmap Overview]]
- [[20_Roadmap/Phase_1_Alpha_Stabilization|Phase 1: Alpha Stabilization]]
- [[20_Roadmap/Reflex_Feature_Roadmap|Reflex Feature Roadmap (Path to v1.0)]]
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