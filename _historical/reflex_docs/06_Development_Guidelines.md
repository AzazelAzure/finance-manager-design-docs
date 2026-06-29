# Development Guidelines

These guidelines are designed to help developers maintain the high standard of the Finance Manager codebase while ensuring architectural consistency.

## Adding a New Page
1. **Define the Route**: Add the path variable and title to `app/routes.py`.
2. **Create the Feature Module**: Create a new directory in `features/` with `view.py`, `state.py`, and `api.py`.
3. **Draft the State**: Create a state class inheriting from `ProfileState` if user context is needed.
4. **Build the View**: Use `page_shell` and `surface_card` to structure the UI.
5. **Register the Page**: Add `app.add_page(...)` to `finance_manager_reflex.py` with appropriate guards.

## Component Creation
- **Prefer Primitives**: Always try to build using existing primitives from `ui/primitives.py`.
- **Logic Placement**: Keep logic out of `view.py`. If you find yourself writing a `lambda` or complex conditional in the UI, move it to a `@rx.var` in the State.
- **Icon Usage**: Use `rx.lucide.icon(tag="...")`. Refer to the [Lucide documentation](https://lucide.dev/icons) for available tags.

## State Management Best Practices
- **Atomic Updates**: Keep event handlers focused. One method should do one thing (e.g., `update_category`, not `update_category_and_refresh_dashboard_and_logout`).
- **Loading States**: Always use an `is_loading` boolean to provide visual feedback (spinners, skeleton screens) during API calls.
- **Cookie Usage**: Only use `rx.Cookie` for persistent, critical data like authentication tokens. For temporary UI toggles, use standard state variables.

## Styling and Tokens
- **NO Hardcoded Values**: If you need a color or spacing value, it must come from `COLOR_TOKENS` or `SPACE_TOKENS`.
- **Responsive Design**: Use Reflex's responsive syntax (e.g., `width=["100%", "50%"]`) for layouts that need to change based on screen size.

## Error Handling Checklist
When implementing a new action:
1. Wrap the API call in a `try/except ApiError`.
2. Capture the error message in a state variable (e.g., `self.error_message`).
3. Display the error to the user using a `rx.callout` or a notification toast.
4. Ensure `is_loading` is set to `False` in the `finally` block.

## Testing and Quality Assurance
- **Simulate Latency**: Test your UI with slow network connections to ensure loading states work as expected.
- **Mobile First**: Regularly check the layout in mobile view (narrow browser window) to prevent horizontal scrolling or overlapping elements.
- **Audit Logs**: Check the browser console for Reflex warnings or API errors during development.
