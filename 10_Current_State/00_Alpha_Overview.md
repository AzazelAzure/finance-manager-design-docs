# Alpha State Overview

The Finance Manager is currently in an **Alpha state**. The core logic and data processing are functional, but the frontend and deployment architectures are still being finalized.

## Component Status Summary

| Component | Status | Location | Notes |
| :--- | :--- | :--- | :--- |
| **API** | Operational | `finance_manager_api/` | Handles all core logic, data storage, and calculations. |
| **CLI** | Operational | `finance_manager_cli/` | Full-featured command line interface for managing finances locally. |
| **Reflex** | Beta / Active Development | `finance_manager_reflex/` | Web frontend. Feature-usable for authenticated dashboard, transactions, upcoming expenses, profile, and onboarding flows; API parity checks continue as contracts evolve. |
| **Rust Middleware** | Planned | N/A | Future proxy/middleware for payload encryption and security. |

## High-Level System Architecture (Current)

Currently, the system is designed around a decoupled monolith pattern, running locally:

1. **The Core (API)**: A Django REST Framework backend serving JSON endpoints. It holds the local development database and executes all business logic (Snapshot generation, transaction matching, recurring bills).
2. **The Clients (CLI / Reflex)**: The CLI remains the primary automation and local scripting client. The Reflex web app is now the active GUI client for authenticated finance workflows, with ongoing Beta contract validation against the API.

## Deep Dives

For detailed architectural breakdowns of each component, refer to their specific documentation:

- [[../api_docs/00_API_Overview|API Architecture & Data Models]]
- [[../cli_docs/00_CLI_Overview|CLI Architecture & Commands]]
- [[../reflex_docs/00_Reflex_Overview|Reflex Architecture & UI State]]
- [[../rust_docs/00_Rust_Overview|Rust Middleware (Planned)]]