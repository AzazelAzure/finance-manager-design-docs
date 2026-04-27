# Localization Strategy: Global Reach

## Overview
The Finance Manager is designed for a global audience. Our goal is to provide a localized experience that respects regional financial conventions, currencies, and languages.

## Multi-Agent Localization Requirements

### 1. Internationalization (i18n) Infrastructure
- **Reflex Frontend**: Utilize a translation key system (e.g., `get_text("DASHBOARD_TITLE")`) instead of hardcoded strings. This allows us to swap language files (JSON/YAML) seamlessly.
- **API Backend**: Ensure error messages and notifications are translatable.

### 2. Regional Financial Conventions
- **Currency Formatting**: Support for varied decimal separators (e.g., `1,234.56` vs `1.234,56`) and symbol placements.
- **Date/Time Formats**: Respect local conventions (DD/MM/YYYY vs MM/DD/YYYY).
- **First Day of Week**: Already implemented in `AppProfile` as a user-configurable setting.

### 3. Currency Conversion
- **Base Currency**: Users define their "home" currency. 
- **Real-Time FX**: Future integration with an exchange rate API to provide unified totals for multi-currency transactions.

## Priority Regions for Beta/Post-Beta
- **USA**: Default (USD).
- **Philippines (PH)**: Primary international test bed. Focus on **PHP** currency and integration with local wallets (GCash/Maya).
- **EU**: Future focus on GDPR compliance and Euro support.

## Mobile Dependency Gate
- Android product implementation depends on localization completion in Reflex.
- The shared localization artifacts (translation keys, locale formatting rules, and reusable UX copy patterns) are prerequisites for Android feature build-out.
- Until the gate is complete, Android work should remain scoped to architecture and contract planning.

## Implementation Steps
- [ ] Audit `primitives.py` for hardcoded strings and move to a `translations.py` dictionary.
- [ ] Research `Babel` or similar Python libraries for automated formatting based on locale.
