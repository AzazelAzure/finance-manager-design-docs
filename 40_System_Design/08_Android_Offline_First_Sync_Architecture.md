# Android Offline-First Sync Architecture

## Purpose
Define the target architecture for an Android app that remains usable with intermittent connectivity (especially in PH mobile-first contexts), while preserving API contract integrity and preventing silent data loss.

## Program Gate (Hard Dependency)
- **Android full product implementation is gated on flagship web + localization readiness** (Reflex is **archived**; do not key gates on Reflex).
- Localization / UX readiness means:
  - Shared translation key catalog is stable and versioned (owned with **`finance_manager_web`** as the reference UI).
  - Locale-aware formatting rules (currency/date/number) are defined and documented for **PH-first** scope per `03_Localization_Strategy.md` and the strategic plan.
  - UX copy and interaction patterns are consistent enough to reuse between **web** and Android.
- Until this gate is complete, Android work is limited to research/prototyping and interface contracts only (scaffold in repo is expected).

## Related — Flagship Web PWA (install / offline / resync)

PH-first distribution includes an **installable web PWA** before native Android reaches full parity. **Outbox, idempotency, and server authority** for that surface are specified alongside S1.B gates in the ecosystem parent `plans/` tree. Vault bridge: [12_Web_PWA_Install_Offline_Sync](./12_Web_PWA_Install_Offline_Sync.md).

## Architecture Principles
- **Offline first:** The app must always allow local capture/edit workflows even without internet.
- **Server authority with atomic validation:** The API remains source of truth for final persisted state.
- **Idempotent replay:** Sync operations must be safe to retry without duplicating writes.
- **Contract parity:** Android uses the same API contracts as **web** and CLI.
- **Durable local state:** Pending operations survive app restarts and device reboot.

## Layer Model
1. **Presentation Layer**
   - Screens, input flows, and localized UX components.
   - Uses shared design tokens and interaction patterns aligned with the **web** app.
2. **Domain Layer**
   - Use-case orchestration for create/update/delete/list operations.
   - Conflict handling policy and retry policy selection.
3. **Data Layer**
   - Local SQLite store for cached entities and sync queue.
   - Remote API client for authenticated requests.
4. **Sync Engine**
   - Background worker that drains queued operations.
   - Handles retries, backoff, conflict detection, and checkpointing.

## Local Data Strategy (SQLite)
- **Entity tables:** canonical cached resources (transactions, sources, categories, tags, expenses, profile snapshot).
- **Outbox table:** append-only queue of pending mutations.
- **Operation fields (minimum):**
  - `op_id` (UUID, client-generated idempotency key)
  - `entity_type`, `entity_id` (or temporary client id)
  - `op_type` (`create`, `update`, `delete`)
  - `payload_json`
  - `created_at`, `attempt_count`, `last_error`
  - `sync_state` (`pending`, `in_flight`, `acked`, `failed`)
- **Checkpoint table:** last successful pull cursor/version per resource type.

## Sync Protocol
### Push Path (Outbox -> API)
1. User action writes to local entity table and enqueues outbox record in one local transaction.
2. Sync engine reads pending outbox records in deterministic order.
3. Each request includes idempotency metadata (`op_id`) and auth context.
4. API validates atomically and applies or rejects.
5. Client marks outbox row as `acked` on success and updates local cache from server response.
6. On failure, classify as retryable or terminal and update `sync_state`.

### Pull Path (API -> Cache)
1. Sync engine requests changes since local checkpoint.
2. Merge server changes into local cache.
3. Advance checkpoint only after successful batch apply.

## Conflict Resolution Policy (Initial)
- **Default:** server wins for canonical fields unless user-facing merge logic is explicitly defined.
- **Create vs delete race:** delete wins if server confirms object removal.
- **Update collisions:** use optimistic concurrency headers/version tokens when available.
- **User-visible conflicts:** surface a clear "needs review" state for irreconcilable edits.

## Reliability and Retry Policy
- Exponential backoff with jitter for transient network/server failures.
- Retry budget per operation before `failed` terminal state.
- Explicit user action to retry failed operations after correction (for validation errors).
- Sync resumes automatically when connectivity is restored.

## Security and Privacy
- Encrypt auth tokens using platform-secure storage.
- Store only necessary local financial data; avoid storing sensitive secrets in plaintext.
- Prepare for future Rust/crypto integration by isolating serialization and encryption boundaries.

## Observability
- Structured sync logs (local) with `op_id` correlation.
- Sync health metrics:
  - queue depth
  - oldest pending age
  - success/failure rates
  - average replay latency
- Redact sensitive values in logs.

## Milestones
### Milestone A: Foundations (Pre-Gate)
- Define Android module boundaries.
- Draft data schemas and sync state machine.
- Finalize API idempotency requirements with backend.

### Milestone B: Post-Localization Gate (Execution Start)
- Implement local cache + outbox.
- Implement authenticated push/pull sync workers.
- Implement conflict surfaces and retry UX.

### Milestone C: Hardening
- Add soak tests for offline/online transitions.
- Validate battery/performance behavior under poor networks.
- Validate crash recovery and zero-data-loss guarantees.

## Exit Criteria
- App supports offline create/update/delete for core financial entities.
- No data loss across restart, reboot, or transient connectivity failures.
- Sync replay is idempotent and validated atomically by API.
- Localization parity with **web** for supported locales in release scope.
