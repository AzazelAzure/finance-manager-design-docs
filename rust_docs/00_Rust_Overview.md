# Rust middleware (planned)

## Current status: planned (not yet implemented)
**Location:** `finance_manager_rust_middleware/` (parent workspace sub-repo; starter shell today).

As the project scales from a local tool to a more robust, potentially hosted application, a Rust middleware layer is planned to handle high-performance, security-critical operations. Read `40_System_Design/Beta_Security_Posture_And_Rust_Middleware_Seam.md` for **beta vs future** boundaries and feature-flag placeholders.

## Proposed Responsibilities
- **Payload Encryption**: Securing sensitive financial data in transit and at rest.
- **Performance Proxy**: Handling high-volume requests or calculations before they hit the Django API.

## Integration Modes Under Consideration
1. **Library Mode**: Python calls Rust via FFI (pyo3/maturin). Best for keeping things tightly coupled locally.
2. **Service Mode**: A sidecar or microservice over HTTP/gRPC. Offers cleaner isolation.
3. **Gateway Mode**: Sits in front of the API/frontends, intercepting and decrypting payloads.

## Next steps
- Keep the `finance_manager_rust_middleware` repo as the default home for the gateway or sidecar implementation; choose **gateway vs sidecar vs FFI** in an ADR before first production path.
- Implement behind a feature flag so the local Python/Django environment remains fully functional without it during the initial rollout.
- **Commit `Cargo.lock`** for any binary/service you deploy; enforce locked release builds.