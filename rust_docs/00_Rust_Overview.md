# Rust Middleware (Planned)

## Current Status: Planned (Not yet implemented)
**Location:** TBD (`finance_manager_crypto/` or similar)

As the project scales from a local tool to a more robust, potentially hosted application, a Rust middleware layer is planned to handle high-performance, security-critical operations.

## Proposed Responsibilities
- **Payload Encryption**: Securing sensitive financial data in transit and at rest.
- **Performance Proxy**: Handling high-volume requests or calculations before they hit the Django API.

## Integration Modes Under Consideration
1. **Library Mode**: Python calls Rust via FFI (pyo3/maturin). Best for keeping things tightly coupled locally.
2. **Service Mode**: A sidecar or microservice over HTTP/gRPC. Offers cleaner isolation.
3. **Gateway Mode**: Sits in front of the API/frontends, intercepting and decrypting payloads.

## Next Steps
- Establish the repo and CI/CD pipelines.
- Implement behind a feature flag so the local Python/Django environment remains fully functional without it during the initial rollout.
- Commit a `Cargo.lock` and enforce locked builds.