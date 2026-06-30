# Finance Manager API Documentation

Welcome to the Finance Manager API documentation. This documentation provides a comprehensive guide to the architecture, data models, endpoints, and business logic of the Finance Manager backend.

The API is built using **Django REST Framework (DRF)** and follows a strict 6-layer architecture to ensure modularity, testability, and performance.

## Documentation Sections

- **[API Overview & Architecture](02_Data_Flow_and_Architecture.md)**: Deep dive into the 6-layer architecture and request lifecycle.
- **[Endpoints & Contracts](01_Endpoints_and_Contracts.md)**: Detailed API reference for all endpoints, request/response formats.
- **[Models Reference](03_Models_Reference.md)**: Comprehensive guide to the data models and their fields.
- **[Business Logic](04_Business_Logic_Reference.md)**: Documentation of complex calculations (Snapshots, Bill Matching).
- **[Authentication & Security](05_Authentication_and_Security.md)**: Details on JWT, Social Auth, and hardening measures.
- **[Data Access Layer](06_Data_Access_Layer.md)**: Documentation of query optimizations and database interactions.
- **[Validators & Tools](07_Validators_and_Tools.md)**: Reference for shared validation logic and utilities.

## Core Responsibilities

- **Central Source of Truth**: Stores all financial transactions, payment sources, bills, and user configurations.
- **Financial Intelligence**: Calculates real-time financial snapshots, "Safe-to-Spend" metrics, and aggregates spending by category.
- **Secure Integration**: Provides a robust, authenticated interface for the Reflex frontend and CLI tools.
- **Production-Ready Patterns**: Implements standard security practices, including JWT authentication, CSRF protection, and thorough data validation.

## Technical Stack

- **Framework**: Django REST Framework (DRF)
- **Database**: PostgreSQL (Production) / SQLite (Local Dev)
- **Auth**: JWT (SimpleJWT) + dj-rest-auth (OAuth2/Social)
- **Documentation**: drf-spectacular (OpenAPI 3.0)