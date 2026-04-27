# Feature Modules Reference

The Finance Manager is organized into functional feature modules. Each module contains its own views, state logic, and API service.

## AgentDash (`features/agentdash/`)
The primary dashboard for financial intelligence.
- **Core View**: A grid of `dashboard_widget` components.
- **Key Logic**: Fetches and parses the `FinancialSnapshot` to render charts (spending trends, asset allocation).
- **In-place Category Filter**: Clicking a category pie slice applies a dashboard category filter and reloads dashboard data in place (`Other` remains non-actionable).
- **Drillthrough**: Explicit source/account drill actions can stage filters and route to the Transactions page for detailed view.
- **Quick Add**: Dashboard quick actions (`Add expense`, `Add income`, `Add transfer`) reuse the shared Transactions editor modal with preconfigured mode/type.

## Transactions (`features/transactions/`)
The transactional heart of the application.
- **Ledger View**: A high-density table or list of transactions.
- **Filtering**: Supports single-select quick filters (type/source/category/currency), date range fields, and optional advanced filters (tag/date/gte/lte/month/year + last month/previous week toggles).
- **CRUD Operations**: Modal-based forms for creating, editing, and deleting transactions.
- **Transfer UX**: Transfer mode hides non-transfer fields for users, while submit logic auto-populates required metadata fields in payload defaults.
- **Batch Processing**: Supports actions on multiple transactions simultaneously (e.g., batch categorization).

## Data Hub (`features/data_hub/`)
The central point for data ingestion and export.
- **Ingestion**: Handling file uploads (CSV, JSON) for historical data importing.
- **Export**: Generating downloadable reports or data dumps.
- **State Management**: Tracks upload progress and handles mapping errors during ingestion.

## Onboarding (`features/onboarding/`)
A guided wizard for new users to set up their environment.
- **Step 1: Sources**: Configuring bank accounts or cash wallets.
- **Step 2: Categories**: Choosing from default expense categories or creating custom ones.
- **Step 3: Initial Snapshot**: Optional seed transaction to establish a baseline.
- **Logic**: Uses a linear state machine to track progression and prevent skipping steps.

## Profile and Settings (`features/profile/`)
User identity and application configuration.
- **Profile**: Managing personal details (username, email).
- **Preferences**: Setting the base currency, timezone, and start of the week.
- **Security**: Password change and account deletion workflows.
- **Integration**: Triggers global data reloads when preferences change to ensure consistent currency rendering across the app.

## Upcoming Expenses (`features/upcoming_expenses/`)
Forward-looking financial planning.
- **Subscription Tracking**: Managing recurring payments and their frequencies.
- **Forecasting**: Visualizing future cash flow based on upcoming expenses.
- **Status Management**: Marking expenses as paid or dismissing them for the current cycle.
