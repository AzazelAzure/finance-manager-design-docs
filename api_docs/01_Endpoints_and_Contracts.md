# Endpoints & Contracts

The Finance Manager API provides a RESTful interface for all financial operations. All requests require authentication unless otherwise specified.

## Base URL

- Production: `https://api.finance.manager/`
- Local: `http://localhost:8000/`

## Authentication Endpoints


| Endpoint              | Method | Description                           |
| --------------------- | ------ | ------------------------------------- |
| `/api/token/`         | `POST` | Obtain JWT access and refresh tokens. |
| `/api/token/refresh/` | `POST` | Refresh an expired access token.      |
| `/api/auth/google/`   | `POST` | Authenticate via Google OAuth2.       |
| `/api/auth/github/`   | `POST` | Authenticate via GitHub OAuth2.       |


---

## Operational Endpoints


| Endpoint       | Method | Description                                                                   |
| -------------- | ------ | ----------------------------------------------------------------------------- |
| `/api/health/` | `GET`  | Lightweight liveness endpoint returning `{"status":"ok"}` for runtime probes. |
| `/api/schema/` | `GET`  | OpenAPI schema document for contract/docs tooling.                            |


---

## Finance Endpoints

### Transactions

Handles the lifecycle of financial entries.


| Endpoint                         | Method   | Parameters                                                                                                        | Description                                |
| -------------------------------- | -------- | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------ |
| `/finance/transactions/`         | `GET`    | `month`, `year`, `source`, `category`                                                                             | List transactions with optional filtering. |
| `/finance/transactions/`         | `POST`   | Payload: `date`, `amount`, `source`, `currency`, `tx_type` (+ optional `description`, `category`, `bill`, `tags`) | Create one or more transactions.           |
| `/finance/transactions/{tx_id}/` | `GET`    | -                                                                                                                 | Retrieve a single transaction.             |
| `/finance/transactions/{tx_id}/` | `PATCH`  | Payload: (any field)                                                                                              | Partial update of a transaction.           |
| `/finance/transactions/{tx_id}/` | `DELETE` | -                                                                                                                 | Delete a transaction.                      |


Transfer create contract note:

- Transfer submissions are sent as paired entries (`XFER_OUT` + `XFER_IN`) with shared metadata.
- Reflex transfer mode keeps UI minimal and auto-populates metadata defaults (e.g., generated description, `category="none"`, empty `bill`/`tags`) so payload remains API-valid without extra user input.

---

### App Profile & Snapshot

User configuration and financial health overview.


| Endpoint                        | Method   | Parameters                  | Description                                       |
| ------------------------------- | -------- | --------------------------- | ------------------------------------------------- |
| `/finance/appprofile/`          | `GET`    | -                           | Retrieve user settings.                           |
| `/finance/appprofile/snapshot/` | `GET`    | -                           | Retrieve the real-time financial snapshot.        |
| `/finance/user/`                | `DELETE` | Payload: `current_password` | Deletes the user account and all associated data. |


---

### Payment Sources

Manage accounts and balances.


| Endpoint                   | Method   | Parameters                                          | Description                      |
| -------------------------- | -------- | --------------------------------------------------- | -------------------------------- |
| `/finance/sources/`        | `GET`    | -                                                   | List all payment sources.        |
| `/finance/sources/`        | `POST`   | Payload: `source`, `acc_type`, `currency`, `amount` | Add a new payment source.        |
| `/finance/sources/{name}/` | `PUT`    | Payload: `acc_type`, `amount`                       | Update a source balance or type. |
| `/finance/sources/{name}/` | `DELETE` | -                                                   | Remove a payment source.         |


---

### Upcoming Expenses (Bills)

Manage recurring and planned expenses.


| Endpoint                             | Method  | Parameters                                            | Description                     |
| ------------------------------------ | ------- | ----------------------------------------------------- | ------------------------------- |
| `/finance/upcoming_expenses/`        | `GET`   | -                                                     | List all upcoming expenses.     |
| `/finance/upcoming_expenses/`        | `POST`  | Payload: `name`, `amount`, `due_date`, `is_recurring` | Add a new bill/planned expense. |
| `/finance/upcoming_expenses/{name}/` | `PATCH` | Payload: `amount`, `due_date`, `paid_flag`            | Update bill details or status.  |


---

## Request & Response Format

### Common Request Payload (JSON)

```json
{
    "amount": 150.00,
    "source": "Main Checking",
    "category": "Groceries",
    "date": "2024-04-26"
}
```

### Common Response Envelope

```json
{
    "status": "success",
    "data": { ... },
    "message": "Operation successful"
}
```

---

**[Return to Overview](file:///home/pproctor/Documents/python/finance_manager/design_docs/api_docs/00_API_Overview.md)**