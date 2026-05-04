# Server Runtime, Scaling, and Architecture

> **Regional strategy (resolved 2026-05-01):** Section 2 aligns with **PH-first** go-to-market and passive US presence per `strategy/strategic-roadmap-reframe-53be/00_strategic_context.md` §3.8 and `PARKING_LOT.md` (P-6).

This document outlines the goals and roadmap for the Finance Manager server runtime, deployment architecture, and database scaling strategies as the application transitions from local development to a global production environment.

## 1. Automated Deployment & Hotfix Management
To maintain high availability and rapid iteration, we are evaluating approaches for deployment and hotfix management:

### The "Antigravity Server" Concept
- **Agent-Driven Hotfixes:** Utilizing an AI agent (e.g., Antigravity) running directly on the server to handle potential hotfixes in the staging or production environments.
- **Instance Interaction:** Tools like **Porta** or **Levitation** would be used to interact with and manage these instances dynamically.
- **Git Watcher Alternative:** A simpler, alternative approach is utilizing a watcher on the server that checks the GitHub repository's git history, pulls the most recent pushes, and spins up test servers to implement them. While this might be difficult to maintain and could negate the need for a dedicated agent, having an agent for dynamic hotfixes is a strong consideration.

## 2. Server Hosting & Regional Routing
As we move towards production, the physical location and distribution of our servers become critical for performance and user experience.

### Splitting API and Frontend
- **Decoupled Hosting:** We will evaluate the benefits and drawbacks of splitting the API and **web** static/API surfaces (`finance_manager_web` + Django API) across hosting or CDN edges versus keeping a unified VPS blue-green stack (see `design_docs/40_System_Design/05_Deployment_Strategy.md`).

### Rollout Strategy (PH-primary)
- **Primary market — Philippines (PH):** Product, distribution, billing research, and performance tuning **prioritize PH** users (mobile-first, GCash/Maya constraints). New acquisition spend and localized campaigns target PH.
- **Passive US / global:** The web app and API remain reachable worldwide; existing non-PH testers may be grandfathered per strategic plan. **No US-targeted acquisition** until explicit trigger conditions are met (`strategy/strategic-roadmap-reframe-53be/PARKING_LOT.md` P-6).
- **Latency and infra:** Monitor API and static asset latency from PH (and diaspora) vantage points; add edge caching or regional read paths when measured pain exceeds agreed thresholds—without reversing PH-first product priorities.

## 3. Database Architecture & Scaling
To support a global user base without compromising the "10 DB hits max" golden rule, we are looking into advanced database topologies.

### Regional Routing & Edge Caching
- **Technology Evaluation:** We are looking into platforms like **Supabase** or **Neon** to provide regional routing and edge-ready PostgreSQL capabilities.
- **Data Sharding & DB Mirroring:** Implementing edge caching with regional databases that act as temporary or mirrored data stores to serve local traffic instantly.
- **Central Synchronization:** Regional databases will periodically push state changes to a Central Main Database to ensure global consistency without bottlenecking local reads/writes.

### Security Integration
- **Zero-Knowledge (ZK) Compatibility:** Any database scaling, mirroring, or sharding strategy must be evaluated against **S5** ZK middleware goals in the strategic plan (not a generic “Phase 3” label). Moving encrypted data between regional and central databases must not compromise the Rust middleware (`finance_manager_crypto`) encryption guarantees. Feasibility, implementation, and security overlap are researched during beta and hardened toward S4/S5 entry triggers.
- **Program Coordination:** Security hardening and tester-facing validation flows must align with [Licensing and Publication Strategy](file:///home/pproctor/Documents/python/finance_manager/design_docs/40_System_Design/09_Licensing_and_Publication_Strategy.md), [Bounty Sandbox Architecture](file:///home/pproctor/Documents/python/finance_manager/design_docs/40_System_Design/10_Bounty_Sandbox_Architecture.md), and [Security Disclosure and Bounty Program](file:///home/pproctor/Documents/python/finance_manager/design_docs/40_System_Design/11_Security_Disclosure_and_Bounty_Program.md).
