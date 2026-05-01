# Server Runtime, Scaling, and Architecture

> **Strategic conflict (pending rewrite):** Section 2 "Rollout Strategy" below describes **US-first then PH** regional rollout. The canonical strategic plan locks **PH-first** go-to-market and defers US acquisition (`plans/cursor/strategic-roadmap-reframe-53be/00_strategic_context.md` §3.8). See `design_docs/10_Current_State/Strategic_doc_conflicts_pending_direction.md` — do not treat §2 as current strategy until HitM updates or replaces it.

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
- **Decoupled Hosting:** We will evaluate the benefits and drawbacks of splitting the API and Frontend (Reflex) into different server locations or hosting providers to optimize delivery (e.g., edge delivery for the frontend, dedicated compute for the API).

### Rollout Strategy
- **Phase 1: US Launch:** The initial production rollout will be targeted at the US region.
- **Phase 2: Philippines (PH) Launch:** Subsequent expansion will target the PH market.
- **Latency Monitoring:** We will strictly monitor latency between regions to determine when and how aggressively to implement edge caching and regional routing.

## 3. Database Architecture & Scaling
To support a global user base without compromising the "10 DB hits max" golden rule, we are looking into advanced database topologies.

### Regional Routing & Edge Caching
- **Technology Evaluation:** We are looking into platforms like **Supabase** or **Neon** to provide regional routing and edge-ready PostgreSQL capabilities.
- **Data Sharding & DB Mirroring:** Implementing edge caching with regional databases that act as temporary or mirrored data stores to serve local traffic instantly.
- **Central Synchronization:** Regional databases will periodically push state changes to a Central Main Database to ensure global consistency without bottlenecking local reads/writes.

### Security Integration
- **Zero-Knowledge (ZK) Compatibility:** Any database scaling, mirroring, or sharding strategy must be evaluated against the eventual Phase 3 Security Hardening goals. Moving encrypted data between regional and central databases must not compromise the Rust middleware (`finance_manager_crypto`) encryption guarantees. Feasibility, implementation, and security overlap will be deeply researched during the Beta phase.
- **Program Coordination:** Security hardening and tester-facing validation flows must align with [Licensing and Publication Strategy](file:///home/pproctor/Documents/python/finance_manager/design_docs/40_System_Design/09_Licensing_and_Publication_Strategy.md), [Bounty Sandbox Architecture](file:///home/pproctor/Documents/python/finance_manager/design_docs/40_System_Design/10_Bounty_Sandbox_Architecture.md), and [Security Disclosure and Bounty Program](file:///home/pproctor/Documents/python/finance_manager/design_docs/40_System_Design/11_Security_Disclosure_and_Bounty_Program.md).
