# CLI Architecture & Commands

## Current Status: Operational
**Location:** `finance_manager_cli/`

The CLI is a Python-based terminal application that allows users to interact with the API without needing a web interface. It is currently the most robust and complete client for the system.

## Core Responsibilities
- **Direct User Interaction**: Provides terminal prompts and commands for adding transactions, viewing snapshots, and managing bills.
- **API Communication**: Wraps HTTP requests to the `finance_manager_api` endpoints.
- **Local Tooling**: Useful for scripting, quick data entry, and local development testing.

## Technical Structure
- Built via standard Python packaging (`pyproject.toml`, `uv`).
- Designed to fail gracefully if the API is unreachable.
- Adheres to cross-repo contracts (e.g., CLI >= 0.4 requires API >= 1.2).

## Next Steps
- Ensure dependency lockfiles (`uv.lock`) are strictly enforced.
- Implement schema contract testing so CLI builds fail if the API payload shape changes.