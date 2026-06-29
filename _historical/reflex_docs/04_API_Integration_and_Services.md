# API Integration and Services

## The `ApiClient` (`core/api_client.py`)
The `ApiClient` is an asynchronous wrapper around `httpx` that handles the heavy lifting of backend communication. It is designed to be resilient and transparent to the feature states.

### Key Features
1. **JWT Lifecycle Management**: Automatically injects `Authorization: Bearer <token>` headers.
2. **Auto-Refresh (401 Handling)**: If a request fails with a 401 Unauthorized status, the client attempts to use the `refresh_token` to get a new `access_token` and retries the original request once.
3. **Error Normalization**: Converts raw HTTP responses (JSON or HTML) into a consistent `ApiError` object with a message and detail payload.
4. **Performance Tracking**: Includes built-in timing logic (`PerfTimer`) to log request durations for optimization analysis.

## Feature Service Layer (`api.py`)
Each feature module contains an `api.py` file. This file acts as a service layer that abstracts the raw HTTP calls into semantic Python methods.

### Pattern: Service Encapsulation
Feature states should **never** call `ApiClient` directly. Instead, they should use the methods provided in the feature's `api.py`.

```python
# features/transactions/api.py
class TransactionsApi:
    async def list_transactions(self, access_token: str, refresh_token: str, filters: TransactionFilters):
        # 1. Format payload/params
        # 2. Call ApiClient.request
        # 3. Handle response or let state catch ApiError
```

## Runtime Environments

The Reflex client talks to two runtime surfaces during local integration:

- Django API requests use `API_BASE_URL`.
  - Local service runtime (`scripts/fm_services.sh`): Django runs on `http://127.0.0.1:8000`.
  - Compose runtime (`docker-compose.yml`): Reflex uses `http://api:8000` on the internal compose network.
- Reflex event/websocket traffic uses `REFLEX_API_URL`.
  - Local service runtime keeps this on `http://127.0.0.1:8001` so Reflex backend traffic does not collide with Django on port `8000`.
  - Compose runtime routes browser traffic through the proxy at `https://reflex-api.financemanager.local:8443`.
- Browser UI entry points are validated through `design_docs/10_Current_State/01_Runtime_Validation_Checklist.md`.

## Data Contracts and Envelopes
The frontend relies on specific JSON shapes (contracts) defined by the Django API.

### Mutation Responses
Most create/update/delete operations return an envelope:
```json
{
    "accepted": [...],
    "rejected": [...],
    "updated": [...]
}
```
The frontend service layer is responsible for parsing these envelopes and ensuring the UI state is updated correctly.

### Snapshot Shapes
The dashboard relies on the `FinancialSnapshot` payload, which aggregates data from multiple sources. The frontend models (`models/`) are designed to match these backend schemas exactly.

## Error Handling Strategy
Errors are caught at the State level, but normalized at the API level.

1. **API Level**: `ApiClient` catches `httpx` exceptions and creates an `ApiError`.
2. **Service Level**: Can perform additional transformations or logging.
3. **State Level**: Catches the `ApiError` and updates the UI (e.g., `self.error_message = str(err)`).

## Authentication Flow (JWT)
1. **Login**: `AuthApi.login` returns `access` and `refresh` tokens.
2. **Storage**: `AuthState` saves tokens in cookies.
3. **Usage**: Every subsequent request uses the `access` token.
4. **Expiry**: On 401, `ApiClient` calls `/api/token/refresh/`.
5. **Session Death**: If refresh fails, the state triggers `logout()` and redirects to `/login`.
