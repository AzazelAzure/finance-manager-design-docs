# Reflex Frontend - Implementation Plan for Zero-Knowledge

This document outlines the concrete implementation steps required to evolve the Reflex frontend from its current MVP state to a client that fully supports the zero-knowledge encryption strategy.

## 1. Guiding Principles

- **The Frontend is the Security Boundary:** All encryption and decryption must occur on the client. The frontend is responsible for managing the user's keys in memory.
- **UX is Paramount:** The complexity of the security model must be abstracted away from the user wherever possible, with clear communication where user action is required (e.g., passphrase management).
- **Centralize Crypto Logic:** All interactions with the security model (key derivation, encryption, rolling keys) should be handled in a single, well-defined layer of the application to ensure consistency and maintainability.

## 2. Core Task: Integrating the Rust Crypto Middleware (WASM)

The Rust middleware, compiled to WebAssembly (WASM), will perform all cryptographic operations. The Reflex application needs a way to call it.

- **Task:** Create a Python service/wrapper within the Reflex backend (e.g., `services/crypto.py`).
- **Interface:** This service will be responsible for loading the WASM module and exposing clean Python methods to the rest of the Reflex application.
  - `derive_key(passphrase: str, salt: str) -> str:` Takes the user's inputs and returns the Master Encryption Key (MEK) as a hex string.
  - `encrypt(plaintext: dict, mek: str) -> str:` Takes a Python dictionary, serializes it to JSON, encrypts it with the MEK, and returns a base64-encoded ciphertext string.
  - `decrypt(ciphertext: str, mek: str) -> dict:` Takes the base64 ciphertext, decrypts it with the MEK, and returns a Python dictionary.

## 3. State Management Strategy

The application state (`AppState`) needs to be updated to manage security-related data for the user's session.

- **New State Class:** Define a `SecurityState(rx.Base)` to be included in the main `AppState`.
- **Fields:**
  - `is_unlocked: bool = False`: A flag to indicate if the user has entered their passphrase for the current session. UI components can react to this to show either locked or unlocked views.
  - `user_salt: str = ""`: Fetched from the user's profile upon login and used for key derivation.
  - `current_key: str = ""`: The current rolling key required for the next API request.
  - `previous_key: str = ""`: The previous rolling key, used for retries in case of network race conditions.

**Critical Note:** The Master Encryption Key (MEK) derived from the passphrase **must not** be stored in the Reflex state. Storing it in the state risks accidental logging or persistence. It should be held in a transient variable within the scope of a request or a short-lived session context, primarily managed by the API Client (see below).

## 4. Centralized API Client

To enforce the security protocol for all API calls, a dedicated API client wrapper is essential. All event handlers in the app should use this client instead of making direct `httpx` calls.

- **Task:** Create a new module, `utils/api_client.py`.
- **Responsibilities:**
  1. **Key Management:** Before sending a request, it must retrieve the `current_key` from the `SecurityState`.
  2. **Encryption:** For `POST`, `PATCH`, and `PUT` requests, it will use the crypto service to encrypt the request body.
  3. **Decryption:** It will decrypt the response bodies of `GET` requests that return sensitive data.
  4. **Key Rolling:** After a successful request, it will parse the *new* rolling key from the response (e.g., from a custom header like `X-Next-Key`) and update `current_key` and `previous_key` in the `SecurityState`.
  5. **Error Handling:** It will specifically handle `403 Forbidden` errors. If the error indicates an invalid rolling key, it can automatically retry the request once using the `previous_key`.

## 5. UX and Component Roadmap

The following components and flows need to be created or modified.

### 5.1. Onboarding (`OnboardingWizard` Component)

This is the most critical UX flow.

- **Step 1: Zero-Knowledge Explanation:** A clear, concise explanation of what "zero-knowledge" means for the user (we can't see your data).
- **Step 2: Passphrase Education & Creation:**
  - Explain that the passphrase is the *only* way to access their data.
  - Emphasize that if it's forgotten, the data is **irrecoverable**.
  - Include a mandatory checkbox: "I understand that if I lose my passphrase, my data is lost forever."
  - A form to create the passphrase, with a strength indicator.

### 5.2. Login Flow (`LoginPage` and `PassphraseModal`)

The login process becomes a two-stage affair.

1. **Authentication:** User logs in with email/password or OAuth as usual. This authenticates the *session*.
2. **Decryption:** Upon successful login, the UI must present a modal (`PassphraseModal`). The user enters their passphrase here to unlock their data. Submitting this modal triggers the derivation of the MEK and the fetching of the initial rolling key.

### 5.3. UI Latency and Loading States

- The sequential nature of rolling keys and the overhead of client-side decryption will introduce latency.
- **Task:** All components that display encrypted data must implement robust loading states (e.g., skeleton loaders). This prevents the UI from feeling frozen while the API client does its work.

## 6. Next Steps for Implementation

1. **Build the Rust/WASM module:** The frontend plan is blocked until a callable crypto library exists.
2. **Implement the `SecurityState` and `PassphraseModal`:** This provides the foundation for the unlock flow.
3. **Build the centralized `api_client`:** Start with a simple version that handles key rolling before adding encryption/decryption.
4. **Refactor one endpoint:** Convert an existing feature (e.g., `fm transactions list`) to use the new `api_client` and handle decryption, including its loading states. This will serve as a template for the rest of the app.
5. **Build the Onboarding Wizard:** Implement the full user education and passphrase creation flow.