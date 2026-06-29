# Research Backlog

> **Note (2026-06-29):** Active research queue and parking-lot items live in the parent workspace `strategy/research/` and `strategy/parking_lot/`. This file retains durable backlog themes for the vault; prefer `strategy/` for current prioritization.

This document tracks items for investigation and future architectural consideration.

## 1. Regional Formatting & I18n
- Researching `Babel` for automated currency and date formatting based on user locale.
- Investigating `gettext` integration for Reflex frontend translations.

## 2. Distributed Architecture
- Researching local database synchronization (e.g., PouchDB/CouchDB or custom SQLite sync) for offline-first capabilities.
- Exploring `gRPC` as a high-performance alternative for internal service communication (API to Middleware).

## 3. Security Hardening
- Evaluating `WASM`-based crypto libraries for the Reflex frontend to support Zero-Knowledge payloads.
- Investigating OAuth2/OpenID Connect providers for future enterprise-grade authentication.

## 4. Data Visualization
- Exploring advanced `Recharts` configurations for predictive financial trend lines.
- Researching automated "Spend Leak" detection algorithms using historical transaction data.

## 5. Server Runtime, Scaling, and Agent Infrastructure
- **Antigravity Server:** Researching the implementation of a dedicated agentic language server for remote hotfixes and automation.
- **Porta:** Investigating [Porta](https://github.com/levitation-studio/porta) as a mobile-friendly proxy for interacting with the Antigravity Language Server via Connect RPC.
- **Levitation:** Exploring [Levitation](https://levitation.studio) for remote agent management and cloud-relayed interaction with local instances.
- **Supabase:** Evaluating [Supabase](https://supabase.com/docs) for regional routing, integrated authentication, and edge-ready PostgreSQL capabilities.
- **Neon:** Researching [Neon](https://neon.tech/docs) for serverless Postgres, database branching, and regional deployment strategies to reduce latency in US/PH rollouts.

## 6. Mobile & Native Development
- **React Native / Expo:** Evaluating the use of Expo for rapid cross-platform Android/iOS development, ensuring high design fidelity and agent-friendly code generation.
- **Atomic Synchronization (PowerSync/WatermelonDB):** Researching tools for robust, atomic local SQLite-to-cloud Postgres synchronization to ensure data integrity during offline-to-online transitions.
- **Rust to Mobile (uniffi-rs):** Investigating the use of `uniffi-rs` to compile the `finance_manager_crypto` Rust middleware into Kotlin/Swift bindings for native mobile zero-knowledge encryption.
- **SMS/Notification Parsing:** Researching Android-native SMS reading APIs for automated transaction logging from PH-specific e-wallets (GCash, Maya).
- **Push Notifications (OneSignal):** Evaluating OneSignal for cross-platform notification management (Upcoming Expense reminders, spend alerts).

## 7. Desktop Standalone Portability
- **Bundled Runtime:** Investigating the feasibility of bundling the Django backend and Reflex/Web frontend into a single standalone application (Linux, Windows, macOS) for users who prefer local-only or offline-first desktop experiences.


