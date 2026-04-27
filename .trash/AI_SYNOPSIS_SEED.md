AI Context Seed (Finance Manager)

**Project:** Finance Manager
**Current State:** Alpha (API and CLI are v1.0 operational; Reflex frontend MVP is in progress).
**Architecture:** Decoupled Monolith (Django REST API, Typer/Click CLI, Reflex React/Python Frontend).
**Future Architecture:** Hosted Zero-Knowledge (ZK) system with a Rust middleware encryption layer.

## The Golden Rule (Rigid Feature Pipeline)
1. **API First:** No UI or CLI feature is built until the API endpoint and exact JSON payload are finalized.
2. **CLI Verification:** The CLI integrates and tests the API feature. This acts as the baseline integration test.
3. **Reflex Implementation:** Only once the CLI proves the API contract works does the Reflex frontend implement the UI.
*All math and heavy data aggregation must be offloaded to the API or Rust middleware. The frontend must remain lean.*

## Repository & Workflow Strategy
- **Multi-Repo Workspace:** The API, CLI, and Reflex components live in separate directories (and will have separate Git histories) managed by a meta-workspace containing the `design_docs/` Obsidian vault and unified `scripts/`.
- **AI Collaboration:** Gemini acts as the Architect/Orchestrator (planning, docs, rules). Cursor acts as the Implementer/Builder (tactical coding). They coordinate asynchronously via `design_docs/CHAT_LOG.md` and `plans/`.

## Key Architectural Goals
- **Reflex MVP:** "Intuitive Input" phase. Keyboard-friendly transaction forms, digestible dashboards, and an onboarding wizard with built-in tutorials for all features.
- **Zero-Knowledge Security:** The server will eventually only store encrypted blobs.
  - **Auth:** OAuth (Google) or Email/Password handles the *session*.
  - **Encryption:** A user-provided *Passphrase* handles the in-memory decryption via the Rust middleware.
  - **Validation:** Uses global BIP-39 environment peppers hashed against the user's plaintext passphrase.
  - **Sessions:** Implements a strict "Rolling Key" system (`current_key` / `previous_key`) for every API request to prevent hijacking and handle packet-loss gracefully.

## Next Immediate Milestones
1. Finalize Reflex MVP and Dockerize it for local "Ready for User Testing" validation.
2. Provision Live and Test servers.
3. Implement OAuth and operational logging (`loguru`, user-tagged logs, email bug reporting).
4. Develop the Rust middleware (`finance_manager_crypto`) and the live data migration tool (Celery/Redis/Rust) for the encryption cutover.