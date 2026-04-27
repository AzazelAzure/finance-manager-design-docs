# Git & Workflow Strategy

## Overview
The Finance Manager project will utilize a **Multi-Repo Strategy**. Each major component (API, CLI, Reflex, and eventually Rust Middleware) will maintain its own distinct Git repository. This allows each component to have its own independent feature rollout schedule, version tags, and CI/CD pipelines.

## Agent Execution Rules (The Git Ecosystem)
To ensure traceability and roll-back capability, all agents MUST adhere to the following Git workflow during execution:

1. **Working in Main:** For the current Alpha/Beta phase, agents work directly in the `main` branch to maintain velocity.
2. **Atomic Commits:** Upon completing a logical task (e.g., "Fix dashboard charts"), the agent MUST commit the changes with a clear `type: description` message (Conventional Commits style).
3. **Version Tagging (v-tags):** 
    - Every successful execution of a plan MUST be accompanied by a version tag increment.
    - Format: `vX.Y.Z` (e.g., `v0.1.1`, `v0.1.2`).
    - This allows the user to roll back the entire repository to a known good state if an agent introduces a regression.
4. **Git First:** No changes should be considered "complete" until they are committed and tagged.

---

## Feature Rollout Pipeline
Because the components are heavily interdependent but decoupled, new features follow a cascading rollout pipeline:

1. **API Feature Branch:** New logic and endpoints are implemented and tested in `finance_manager_api`.
2. **CLI Integration:** The CLI is updated in `finance_manager_cli` to consume the new API feature for base-level verification and developer testing.
3. **Bug Fixes (Round 1):** Any issues identified via CLI testing are fixed in the API/CLI.
4. **Reflex Implementation:** The new feature is implemented in the `finance_manager_reflex` frontend.
5. **Bug Fixes (Round 2):** Any UX or state issues identified via Reflex testing are fixed.
6. **CLI Retest:** The CLI verifies that no API changes required by Reflex broke the core CLI workflows.
7. **Release:** Coordinated version tags are cut across all modified repositories.

## Workspace Management
To manage this multi-repo setup locally, we will use a **Meta-Workspace Repo** (the root directory where these folders reside). This workspace repo will NOT contain the code itself, but rather:
- The `design_docs/` Obsidian vault.
- `plans/` for Cursor execution.
- High-level `docs/` and runbooks.
- **Workflow Bash Scripts** to automate tasks across all child repositories.

## Planned Workflow Scripts (To be implemented)
To make local development seamless, we need to implement a suite of bash scripts in a `scripts/` directory at the workspace root:

1. `scripts/git_sync.sh`: Automates checking out the correct branches or pulling the latest `main` across all child repositories simultaneously.
2. `scripts/status.sh`: Provides a quick overview of `git status` (uncommitted changes, current branch) for all repos at once.
3. `scripts/feature_branch.sh <feature_name>`: Automatically creates a uniformly named feature branch in all necessary child repos.
4. `scripts/tag_release.sh <version>`: Helps cut tags across the repos that participated in a feature release, ensuring cross-repo compatibility contracts are logged.

These scripts will bridge the gap, giving the feel of a monorepo locally while preserving the benefits of a split architecture.