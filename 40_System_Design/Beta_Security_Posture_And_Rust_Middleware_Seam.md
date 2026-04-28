# Beta security posture and Rust middleware seam

## Purpose

Separate **what is true for the current server beta** from **future zero-knowledge (ZK), rolling keys, and Rust middleware** so roadmap work does not block shipping, and future integration can attach without rebuilding the whole API surface.

## Current beta posture (implemented or explicitly accepted)

| Area | Beta state |
|------|------------|
| Transport | HTTPS in production; `Secure` / `SameSite` cookies where applicable |
| API auth | JWT access tokens; OAuth social login via existing stacks; DRF defaults require authentication for finance routes unless a view is explicitly public (see `finance_manager_api` settings and `drf-spectacular` `SERVE_PERMISSIONS`) |
| Payloads | **Plaintext JSON** finance data inside the trusted API and between API and Reflex over HTTPS — not end-to-end encrypted with client-side ZK in beta |
| Reflex tokens | Access/refresh held in browser-readable cookies / state for session continuity — **XSS remains a relevant risk**; mitigated by HTTPS, SameSite, and short UX window; HttpOnly refresh and server-issued cookies are a **follow-up**, not a beta fake |
| Logging | Operators should **not** rely on raw financial payloads or end-user-chosen strings in default logs; API is moving to key-level summaries and pseudonymous request context (see API changelog) |

**ZK / client-side encryption / rolling global keys** are **not** beta launch blockers unless explicitly scheduled on the execution board.

## Future integration (contract placeholders — not implemented)

The following are **naming and seam placeholders** so `finance_manager_rust_middleware` and `finance_manager_rust_tools` can land without dictating crypto algorithms in this document:

- **Feature flag (example):** `RUST_MIDDLEWARE_ENABLED` (exact wiring TBD in deployment config).
- **Rolling-key / version headers (placeholders for ADR):** `X-FM-Key-Version`, `X-FM-Enc-Envelope` — present only when middleware is on; values are opaque to Django business logic.
- **Payload envelope:** versioned, **opaque** ciphertext or sealed blob at the HTTP boundary; API code must not assume plaintext-only field paths when the flag is on (adapters map after verify).
- **Redaction:** logs must never print raw ciphertext, key material, or pepper values — at most length/hash class markers.

## Rust integration mode (decision required)

Pick one primary attachment pattern before building production middleware:

1. **Gateway** — terminate TLS, verify envelopes, then forward to Django.
2. **Sidecar / service** — gRPC/HTTP to a co-located Rust process; Python unchanged at the network edge.
3. **FFI (pyo3/maturin)** — in-process for specific hot paths; tighter coupling, faster local dev.

`design_docs/rust_docs/00_Rust_Overview.md` is updated to name **`finance_manager_rust_middleware`** as the main home for the service/gateway direction; FFI vs gateway is an explicit product/ops choice.

## Lockfiles (Rust)

Aligned with `docs/DEPENDENCY_LOCKFILES.md` in the parent workspace: **any shipped Rust binary or long-lived service** should **commit `Cargo.lock`**; library-only crates may follow a different policy per crate. The starter repos drop ignore rules that blocked `Cargo.lock` so the first real crate can commit the lock from day one.

## Related documents

- `00_Encryption_Strategy.md` — long-term ZK and rolling-key *vision* (not “done in beta”)
- `40_System_Design/Authentication.md` — auth flows; beta remains JWT/OAuth
- `rust_docs/00_Rust_Overview.md` — mode list and repo naming
- `20_Roadmap/Beta_Contract_Compatibility_Matrix.md` — client/API contract expectations
- `api_docs/05_Authentication_and_Security.md` — API-facing auth description

## Non-goals

- This note does not implement encryption, HSM integration, or Rust services.
- It does not assert zero-knowledge guarantees for the current release.
