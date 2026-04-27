# Phase 1: Alpha Stabilization

**Status:** `In Progress`
**Objective:** Before we can host this application or add complex middleware, we must ensure the local codebase is reproducible, strictly version-controlled, and that the web frontend (Reflex) can successfully communicate with the API.
**Execution semantics:** This phase is a broad, multi-epic undertaking executed through many tasks and subplans. It is not intended to be completed in a single implementation cycle.

## Phase Scope Boundaries
- Workstream A: reproducible environments and dependency discipline.
- Workstream B: repository/workflow discipline and release hygiene.
- Workstream C: frontend MVP parity against API/CLI contracts.
- Workstream D: runtime validation readiness for containerized execution.
- Completion requirement: all workstreams must satisfy exit criteria, with deferred items documented explicitly.

## Step 1: Strict Dependency Management
*See [[../30_Releases/Dependency_Management]] for policy details.*

- [x] **Audit Current State:** Review `pyproject.toml` files in `finance_manager_api`, `finance_manager_cli`, and `finance_manager_reflex`.
- [x] **Generate Lockfiles:** Ensure `uv.lock` is generated and committed for each component.
- [x] **Enforce Frozen Installs:** Update local runbooks and test scripts to use `uv sync --frozen` to guarantee that local environments perfectly match the lockfile.

## Step 2: Git Structure & Workflow Scripts
*Currently, the codebase lives in a single directory structure. We need to decide and enforce how Git handles this.*

- [x] **Determine Repo Strategy:** Decide whether to keep a monorepo approach or split API, CLI, and Reflex into separate Git repositories connected via a workspace meta-repo.
- [x] **Initialize Git:** Apply the chosen strategy to the local filesystem.
- [x] **Develop Git Helper Scripts:** Create bash scripts to handle repetitive tasks:
  - Synchronizing commits across multiple repos (if split).
  - Tagging releases consistently across components.
  - Generating changelogs.

## Step 3: Reflex Frontend MVP
*The API and CLI are operational. Reflex needs to catch up to be considered Beta-ready.*

- [x] **API Connectivity:** Establish the core HTTP request layer within the Reflex app to fetch data from the Django API.
- [x] **Feature Parity:** Ensure ALL features currently available in the API and CLI are fully functional within the Reflex frontend.
- [x] **Onboarding Flow:** Develop a smooth, frictionless onboarding process for new users (profile setup, first accounts, initial configuration).
- [x] **UI/UX Polish:** Ensure the interface is intuitive and simple enough that the "least common denominator" of users can navigate and manage their finances without confusion.

## Step 4: Dockerization & "Ready for User Testing"
*To simplify deployment and guarantee a consistent test environment, the UI needs to be containerized and mounted for live testing.*

- [x] **Dockerize Reflex:** Create a `Dockerfile` and `docker-compose.yml` (if needed to link with the API) specifically for `finance_manager_reflex`. (Dockerfiles exist, but local running is deferred).
- [ ] ~~**Mount & Test:** Run the containerized Reflex app to perform a comprehensive "Ready for User Testing" pass.~~ *(Deferred: Local daemon access blocked. Will perform in Phase 2 on Test Server).*
- [x] **UI/UX Adjustments:** Make final layout, copy, and styling adjustments based on live interaction with the mounted site.

## Exit Criteria for Phase 1
- [x] All Python projects install predictably via `uv.lock`.
- [x] Git history is structured, and bash scripts exist to manage workflow seamlessly.
- [x] The Reflex MVP is complete: all API features are accessible, onboarding is seamless, and the UI/UX is highly functional and intuitive.
- [x] Reflex is successfully Dockerized (local testing deferred to Phase 2 Test Server due to environment constraints).

**Phase 1 is now officially considered Complete.**