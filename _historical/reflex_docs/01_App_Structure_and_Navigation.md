# App Structure and Navigation

## Centralized Routing

Routes are managed centrally in `finance_manager_reflex/app/routes.py`. This ensures consistency across links, navigation menus, and page registrations.

### Route Registry


| Route Variable           | Path                | Description                    |
| ------------------------ | ------------------- | ------------------------------ |
| `SPLASH_ROUTE`           | `/`                 | Marketing landing page.        |
| `LOGIN_ROUTE`            | `/login`            | User authentication.           |
| `DASHBOARD_ROUTE`        | `/dashboard`        | Main financial overview.       |
| `TRANSACTIONS_ROUTE`     | `/transactions`     | Filterable transaction ledger. |
| `SETTINGS_PROFILE_ROUTE` | `/settings/profile` | User profile and security.     |
| `ONBOARDING_ROOT_ROUTE`  | `/onboarding`       | Wizard start page.             |


## Page Guards and Security

Access control is enforced at the page level using Reflex's `on_load` event handlers. These guards interact with `AuthState` to ensure users are redirected appropriately.

### Guard Logic (`app/guards.py`)

- `**guard_protected**`: Used on pages requiring authentication (e.g., Dashboard). Redirects to `/login` if no valid token is found.
- `**guard_authenticated**`: Used on public-facing pages that should be hidden from logged-in users (e.g., Login/Signup). Redirects to `/dashboard` if a session exists.

Example usage in `finance_manager_reflex.py`:

```python
app.add_page(
    agentdash_page,
    route=DASHBOARD_ROUTE,
    on_load=AgentDashState.on_dashboard_page_load, # Internal guard + data load
)
```

## Navigation Patterns

The application uses two primary navigation modes to ensure responsiveness across devices.

### Sidebar Navigation

On desktop screens, a persistent sidebar provides quick access to core features. It includes:

- User profile summary.
- Core links (Dashboard, Transactions, Data Hub).
- Settings and Logout.

### Mobile Navigation

On smaller screens, the sidebar is replaced by a bottom navigation bar or a hamburger menu (depending on the specific view implementation), prioritizing touch-friendly targets.

## The Layout System

Consistency is maintained via the `page_shell` primitive found in `ui/primitives.py`.

### `page_shell`

The `page_shell` wraps every page content, providing:

- Standardized maximum width and padding.
- Consistent vertical spacing between sections.
- Centered layout for better readability on large monitors.

```python
def page_shell(*children: rx.Component, max_width: str = "960px") -> rx.Component:
    return rx.container(
        rx.vstack(
            *children,
            spacing="5",
            align="stretch",
            width="100%",
            max_width=max_width,
            margin_y=SPACE_TOKENS["2xl"],
        ),
        size="3",
    )
```

