# Security & Encryption Strategy (Path to Zero-Knowledge)

This document outlines the architectural strategy for transitioning the Finance Manager to a zero-knowledge system where the server cannot read user financial data.

## 1. Guiding Principles
- **Zero Knowledge:** The server and its administrators must not be able to decrypt user financial data. All encryption and decryption will happen on the client-side.
- **User Trust:** The user is the sole holder of the key to their data. This means account recovery for a forgotten passphrase is not possible; the data will be irrecoverable. This trade-off must be clearly communicated.
- **Defense in Depth:** We will use multiple layers of security (authentication, key derivation, per-request keys) to protect user data.

## 2. Core Components

### 2.1. User Passphrase
- The user provides a high-entropy passphrase that they can remember.
- This is the single secret the user must protect.
- A minimum length and complexity will be enforced (e.g., 14+ characters).
- This passphrase is **never** transmitted to the server.

### 2.2. Key Derivation (Passphrase to Master Key)
The user's **Master Encryption Key (MEK)** will be derived on the client-side using a Password-Based Key Derivation Function (PBKDF).

- **Algorithm:** **Argon2id** (the current recommended standard for password hashing and key derivation).
- **Inputs:**
    1.  **User Passphrase:** Provided by the user on login.
    2.  **Per-User Salt:** A unique, long, cryptographically secure random string generated for each user and stored in plaintext in the database (e.g., in `AppProfile`).
    3.  **Global Pepper:** A secret string stored securely on the server (e.g., in `.env` file), inaccessible to the database.
- **Process:** `MEK = Argon2id(passphrase, salt, pepper)`
- **Lifecycle:** The MEK exists **only in client-side memory** for the duration of a logged-in session. It is never stored.

### 2.3. Data Encryption
- **Algorithm:** **AES-256-GCM**. This is an authenticated encryption cipher that provides both confidentiality and integrity.
- **Process:** All sensitive financial data (transactions, source balances, etc.) will be encrypted with the MEK before being sent to the server for storage. The server will only ever handle encrypted blobs of data.

### 2.4. Rolling Key System (Per-Request Authorization)
To protect against session hijacking and replay attacks, every authenticated API request will require a short-lived, single-use key.

- **Model:**
    - `RollingKey.current_key`: The key required for the *next* API request.
    - `RollingKey.previous_key`: The key from the *previous* request, kept temporarily to handle network race conditions or packet loss.
- **Flow:**
    1.  User logs in and provides their passphrase to derive the MEK.
    2.  The client makes an initial authenticated call to get the first `current_key`.
    3.  For each subsequent API request, the client sends the `current_key`.
    4.  The server validates the `current_key`.
        - If valid, it processes the request. In the response, it includes a **new** `current_key` for the next request. The old `current_key` becomes the `previous_key`.
        - If the key matches `previous_key`, the server rejects the request but re-sends the valid `current_key` (handles packet loss where the client missed the last key rotation).
        - If the key is invalid, the server returns a `403 Forbidden` error.
- **Storage:** The `RollingKey` model will store hashed versions of these keys, linked to the user's profile.

## 3. Open Questions & Risks
- **Frontend Concurrency:** The rolling key system, in its strictest form, forces sequential API requests, which could introduce significant UI latency.
    - **Mitigation to Explore:** Benchmark the performance impact. Consider a modified flow where only write operations (`POST`, `PATCH`) roll the key, allowing parallel `GET` requests.
- **Account Recovery:** If a user forgets their passphrase, their encrypted data is permanently lost.
    - **Mitigation:** This must be made explicitly clear during user onboarding. We could offer an option to download a recovery kit (e.g., a file containing their salt and an encrypted version of their MEK, which they can store securely), but this adds complexity.
- **Implementation:** The client-side encryption logic and key management will be complex. This logic should be centralized in the planned Rust middleware, which can be called from both the web frontend (via WASM) and potentially the CLI.

## 4. Related Documents
- The initial brainstorming for this is in `business_notes.txt`.