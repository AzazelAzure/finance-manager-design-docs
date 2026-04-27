# UI System and Theming

## Design Philosophy: "Soft Modern"
The Finance Manager UI adheres to a "Soft Modern" design system. This system prioritizes readability, accessibility, and a premium feel through:
- **Low Contrast Baselines**: Using subtle grays and off-whites for backgrounds to reduce eye strain.
- **Organic Shapes**: Large border radii (`1.25rem` for cards) to create a friendly, modern appearance.
- **Depth and Elevation**: Multi-layered shadows and subtle glows to establish visual hierarchy.

## Design Tokens (`ui/tokens.py`)
All styles are derived from a centralized set of tokens. **Hardcoded hex codes or pixel values should be avoided in feature code.**

### Color Palette
| Token | Value | Purpose |
| :--- | :--- | :--- |
| `bg_canvas` | `#f3f4f6` | The main background behind everything. |
| `bg_surface` | `#ffffff` | Card and modal backgrounds. |
| `brand` | `#4f46e5` | Core action color (Indigo). |
| `text_primary` | `#111827` | High-contrast body text. |

### Spacing and Radius
- **Spacing**: Ranging from `xs` (`0.25rem`) to `3xl` (`3rem`).
- **Radius**: `sm` (`0.375rem`) for inputs, `lg` (`1.25rem`) for cards.

## UI Primitives (`ui/primitives.py`)
Primitives are the building blocks of the application. They wrap base Reflex components with our design tokens and logic.

### `surface_card`
The primary container for content sections.
- **Features**: Automatic hover animations, standardized padding, and shadow elevation.
- **Usage**: Use for dashboard widgets, list items, and form containers.

### `dashboard_widget`
A specialized version of `surface_card` designed for modular dashboard components.
- **Features**: Built-in header with icon support and an "actions" slot for buttons or menus.

### `primary_button` / `secondary_button`
Standardized buttons with built-in:
- Brand colors and hover states.
- Loading indicator support.
- Icon alignment logic.

## Theming and Layout
The global theme is defined in `ui/theme.py` and applied in the main `rx.App` constructor.

### Global Styles (`APP_BASE_STYLE`)
Defined in `ui/__init__.py`, these styles apply to every page:
- **Typography**: Standardizing font family (Inter) and line height.
- **Scrollbars**: Custom styling for a cleaner look.
- **Transitions**: Default animations for page transitions and hover states.

## Aesthetics Checklist
When building new UI components, ensure they meet the following criteria:
1. **Does it use tokens?** (No hardcoded colors).
2. **Is it "Soft"?** (Does it have appropriate border radii?).
3. **Does it have feedback?** (Hover states, active scales).
4. **Is it responsive?** (Does it use `vstack`/`hstack` correctly?).
