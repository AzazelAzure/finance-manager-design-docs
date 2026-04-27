# Reflex State Management Architecture

Related overview: `design_docs/reflex_docs/00_Reflex_Overview.md`

## Base Auth Layer

- `AuthState`
  - token storage (`access_token`, `refresh_token`)
  - computed auth flags
  - redirect helpers
- `ProfileState(AuthState)`
  - login/signup workflow
  - profile and snapshot loading
  - logout and auth redirect behavior

## Feature State Modules

### AgentDash

`AgentDashState` composes state mixins:

- auth
- load lifecycle
- preferences
- drillthrough
- toggles
- state vars

Purpose: aggregate dashboard data and forward drillthrough filters to transactions.

### Transactions

`TransactionsState` composes mixins:

- auth checks
- filters
- form handling
- mutation handlers

Purpose: list/filter transactions and run create/update/delete flows.

### Upcoming Expenses

`UpcomingExpensesState` composes mixins similar to transactions:

- auth
- filters
- form
- mutation

Purpose: list/filter expenses and run mutation flows with envelope handling.

### Onboarding

`OnboardingState(AuthState)` tracks wizard progression and initial setup actions (sources, categories, optional seed transaction).

## State Design Pattern

- UI actions trigger state events.
- State events validate and call API wrappers.
- Responses update state fields and reactive UI sections.
- Auth failures clear tokens and redirect to login.