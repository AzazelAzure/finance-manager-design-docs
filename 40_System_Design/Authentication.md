# Authentication & Security Strategy

## Overview
This document outlines the evolutionary path for user authentication and data security within the Finance Manager, moving from a standard Beta launch to a fully realized Zero-Knowledge (ZK) architecture.

## Phase 1: Beta Launch (OAuth Integration)
To reduce friction during onboarding, the application will support OAuth 2.0.

### Supported Providers
- **Google:** Planned for initial Beta. Widely used and trusted.
- **Facebook/Meta:** *On Hold*. Pending review of their data sharing terms and how they align with our privacy-first, zero-knowledge goals.
- **Email/Password:** Traditional fallback for users who prefer not to use federated logins.

### Development & Testing
- **Localhost Testing:** Yes, OAuth *can* be tested locally. Providers like Google allow `http://localhost:<port>` or `http://127.0.0.1:<port>` as valid authorized redirect URIs for development environments.
- **Hosting Dependency:** While local testing is possible, the production OAuth consent screens require a verified, hosted domain with HTTPS. Therefore, setting up the Test Server infrastructure must happen concurrently with or slightly before finalizing the OAuth implementation.

## Phase 2: Zero-Knowledge (ZK) Architecture & Cryptography
The ultimate goal for the Finance Manager is a Zero-Knowledge architecture powered by the Rust middleware, ensuring the server NEVER sees the user's unencrypted financial data.

To achieve this while maintaining OAuth or standard session logins, the system implements a **Passphrase + Rolling Key** architecture.

### Cryptographic Keys & Peppers
- **BIP-39 Environment Peppers:** The Rust middleware and the Django API will each maintain a BIP-39 generated string in their `.env` files acting as a global pepper/salt.
- **User Passphrase:** In addition to the standard login (OAuth or Email), the user MUST provide a local passphrase. 

### The Security Table & Verification
To verify the passphrase without storing it:
1. A database table holds a validation record keyed by a hash of the user's `UUID` + a previous hash.
2. The record contains a random BIP-39 string hashed against the user's plaintext passphrase and the global pepper.
3. When the user enters their passphrase, the Rust middleware attempts to recreate the hash. If successful, the passphrase is correct and is kept strictly in-memory on the client/middleware to encrypt/decrypt payloads.
4. **Failure State:** If the passphrase is wrong, the data returned is garbage, the payload is discarded, and the user is forcefully logged out.

### The Rolling Key Session System
To prevent session hijacking and packet-loss lockouts, a rolling key model is used:
1. **Session Initialization:** Upon login, a temporary `session_token` is generated alongside a `current_key` (a UUID hashed with the user's ID).
2. **Request Cycle:** Every API request must include the `current_key`.
3. **Key Rotation:** Once a request is successfully processed, the `current_key` is demoted to `previous_key`, and a newly generated `current_key` is returned to the user for their next request.
4. **Graceful Failover:** If a user's request drops and they retry with the `previous_key`, the server simply returns the active `current_key` without processing a mutation, ensuring they are not locked out due to network issues.
5. **Security Benefit:** Using a compromised `current_key` invalidates it immediately, and standard JWT/Auth tokens are meaningless without the correctly synchronized rolling key.

## Next Steps
1. Create a Google Cloud Console project to generate development OAuth credentials for `localhost` testing.
2. Draft the API architecture for handling JWT generation post-OAuth callback.
3. Begin prototyping the "Two-Step" ZK login flow in the Rust middleware.