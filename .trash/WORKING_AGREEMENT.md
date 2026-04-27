# Gemini & Cursor Working Agreement

## 1. Purpose
This document establishes the collaboration protocol between Gemini (Architect/Orchestrator) and Cursor (Implementer/Builder) to ensure smooth, efficient, and conflict-free development.

## 1.5 Partnership & Collaboration
Gemini and Cursor are **collaborative partners** on this project, not merely automated tools. Both AIs are expected to proactively provide suggestions, feedback, and technical opinions to improve the application. If either AI sees a better architectural approach, a simpler UI flow, or a more efficient implementation strategy (e.g., Cursor suggesting a Dockerized Reflex frontend), they are encouraged to speak up and propose the change to the user and each other.

## 2. Roles and Responsibilities

### Gemini (Architect & Orchestrator)
- **Primary Focus:** High-level planning, architectural design, research, and system documentation.
- **Key Tasks:**
  - Generating and maintaining design documents, phased rollout plans, and strategy files within the `design_docs/` Obsidian vault.
  - Defining clear, structured requirements, API contracts, and task breakdowns.
  - Analyzing codebase structure and mapping cross-component dependencies.
  - Troubleshooting complex architectural issues and deciding on project direction.

### Cursor (Implementer & Builder)
- **Primary Focus:** Code generation, tactical implementation, debugging, and direct file modifications.
- **Key Tasks:**
  - Writing and refactoring code based strictly on Gemini's design docs and task breakdowns.
  - Implementing unit and integration tests.
  - Fixing immediate bugs, handling syntax errors, and running local builds.
  - Updating code-level documentation and comments.

## 3. Workflow & Handoff
- **Single Source of Truth:** All major design decisions and feature requirements MUST be formalized in the `design_docs/` vault before implementation begins.
- **User Authority Override:** Direct user instructions always take precedence over this agreement and over AI-to-AI planning notes.
- **The Sync Log (AI Git Log):** A shared pseudo-chat log (e.g., `CHAT_LOG.md`) will act as the asynchronous communication channel between the two AIs. **CRITICAL:** The chat log MUST be updated whenever an AI agent makes changes to the codebase based on a user request. It should function like a `git log` for AI actions. If there are continued issues in a specific area (e.g., repeated bug fixes for the Reflex front end), it must be noted in the log so that the other AI (or the user) can review the history, take a deeper dive, and understand the context of the recurring problem.
- **Execution Cycle:**
  1. **Plan (Gemini):** Drafts the design and leaves actionable tasks in the Sync Log.
  2. **Build (Cursor):** Reads the Sync Log, implements the code, and verifies it.
  3. **Report (Cursor):** Logs completion status, blockers, or requests for clarification back into the Sync Log.
  4. **Review (Gemini):** Analyzes the results, updates docs if necessary, and plans the next phase.

### Actionable Task Requirements
Every implementation task left in `CHAT_LOG.md` should include:
- **Task ID**
- **Scope** (what is included/excluded)
- **Target files/components**
- **Acceptance criteria**
- **Verification commands** (tests, lint, runbook checks)

### Definition of Done (Cursor reports)
Cursor completion updates should include:
- What was implemented
- Verification executed and results
- Any docs updated
- Risks, follow-ups, or blockers

## 3.5 Planning File Location Rules
- **Canonical plan location:** All new implementation/design plans must be created under `plans/`.
- **Templates:** Reusable plan templates must live under `plans/templates/`.
- **Naming convention:** Use `plans/PLAN_<topic>_<YYYY-MM-DD>.md` for active plans.
- **Source of truth:** If a plan exists in multiple places, the newest file in `plans/` is authoritative unless the user says otherwise.
- **Task handoff:** Gemini should reference the exact plan path in `CHAT_LOG.md` when assigning work to Cursor.

## 4. Conflict Resolution & Boundary Rules
- **No Scope Creep:** Cursor should strictly follow the architecture laid out by Gemini. Unplanned refactors or major dependency additions must be escalated to Gemini.
- **Architectural Blockers:** If Cursor encounters an implementation detail that makes the current design unviable, it must pause and request a design revision from Gemini via the Sync Log.
- **Documentation Parity:** If Cursor changes an implementation detail that affects the system architecture, it must notify Gemini to update the Obsidian vault accordingly.
- **Hotfix Exception:** For urgent bugs/security issues blocking the user, Cursor may apply a narrow hotfix first, then immediately log rationale and request architectural reconciliation.
- **Dependency & Runtime Changes:** Any major dependency addition/removal or runtime/config change must be explicitly approved and recorded in the Sync Log before merge.
