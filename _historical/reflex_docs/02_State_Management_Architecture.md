# State Management Architecture

## The State Hierarchy

Reflex state follows a hierarchical inheritance model. The Finance Manager leverages this to share authentication and profile context across all feature-specific states.

### `AuthState` (Base)

Located in `core/auth_state.py`, this is the root of the state tree.

- **Responsibilities**: JWT token storage (via `rx.Cookie`), cookie consent tracking, and basic authentication computed variables (`is_authenticated`).
- **Persistence**: `access_token` and `refresh_token` are stored in secure cookies with a 7-day TTL.

### `ProfileState` (Shared Context)

Inherits from `AuthState`. Located in `features/profile/state.py`.

- **Responsibilities**: Loading user profile data, fetching the financial snapshot, and managing session-wide preferences (currency, timezone).
- **Preloading**: Contains the `preload_after_login` logic which warms up core datasets (sources, categories, tags) to ensure a snappy UI experience.

### Feature States

Each feature (e.g., `AgentDashState`, `TransactionsState`) inherits from either `ProfileState` or `AuthState`.

- **Isolation**: Feature-specific logic (e.g., dashboard drillthroughs, transaction filters) is kept within these modules.
- **Inheritance**: Accessing the current user's currency or token is as simple as `self.session_base_currency` within any feature state.

## State Design Patterns

### The "Event -> API -> State" Cycle

Most user interactions follow a standardized asynchronous flow:

1. **Trigger**: User clicks a button, triggering an `@rx.event` method.
2. **Setup**: State sets `is_loading = True` and clears previous errors.
3. **Execution**: State calls a service method (e.g., `TransactionsApi().create_transaction`).
4. **Update**: State updates its local variables with the response and resets flags.
5. **Reactivity**: The UI automatically re-renders based on the updated state variables.

### Computed Variables (`@rx.var`)

Computed variables are used for data transformation and UI logic that shouldn't persist in the state.

- **Performance**: Calculated on the fly, reducing state bloat.
- **Formatting**: Ideal for turning raw API floats into formatted currency strings (e.g., `snapshot_total_assets_text`).

### Mixins and Composition

While Reflex uses inheritance, we treat state classes as compositions of logic.

- **Form Handling**: Mixins handle input validation and submission flags.
- **Lifecycle Management**: Standardized `on_load` methods ensure data is fetched only when the page is active.

## Lifecycle Events

We prioritize the `on_load` event on pages to ensure fresh data.

```python
@rx.event
async def on_dashboard_page_load(self):
    # 1. Guard check
    if not self.is_authenticated:
        return rx.redirect(LOGIN_ROUTE)
    
    # 2. Sequential/Parallel Data Loading
    await self.load_profile_and_snapshot()
    # ... other feature-specific loads
```

## State Best Practices

- **Lean States**: Avoid storing large raw JSON blobs; parse them into typed models or specific fields.
- **Secure Logging**: Never log raw tokens or sensitive password fields. Use `LOGGER.debug("...", has_token=bool(token))` instead.
- **Error Normalization**: Always catch `ApiError` and convert it into a user-friendly `error_message`.

