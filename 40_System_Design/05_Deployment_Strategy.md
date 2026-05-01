# Deployment Strategy: Blue-Green Multi-Agent Pipeline

> **Updated 2026-04-30:** Reflex archived; flagship product is `web` (`finance_manager_web`). The active blue-green stack pairs `api-blue/api-green` and `web-blue/web-green` (no longer Reflex). Per-feature color-cycle workflow is canonical at `plans/_governance/branching_guidelines.md`.

## Overview

To achieve high availability and zero-downtime updates, the Finance Manager ecosystem utilizes a **Blue-Green Deployment Strategy**. This involves running two identical environments (active and inactive) simultaneously, with one feature at a time staged on the inactive color.

## Architecture

- **Infrastructure**: Each service (API, web) runs within its own Docker container, with blue and green variants per service.
- **Orchestration**: A traffic manager (Nginx, HAProxy, or a Redis-backed router) sits in front of the services.
- **Shared Persistence**: Both the "Blue" (Live) and "Green" (Testing) environments point to the same PostgreSQL database to ensure state consistency.

## Workflow

1. **Implementation**: Subagents execute plans in the "Green" environment.
2. **Verification**: Tests are run against the "Green" instance.
3. **Divergence**: Once verified, the traffic manager redirects user traffic from "Blue" to "Green."
4. **Sunset**: The old "Blue" version is decommissioned or kept as a fallback.

## Considerations for the Future

- **Database Migrations**: We must develop a strategy for "forward-compatible" migrations (e.g., adding columns instead of renaming) to ensure the "Blue" runtime doesn't break while "Green" is performing updates.
- **Session Persistence**: Ensure that switching traffic doesn't force users to log in again (shared JWT store/Redis).
- **Automation**: Developing a "Mission Control" script to handle the traffic toggle.
