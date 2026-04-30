# Localization Strategy: PH-First, Global-Eventual

**Major revision 2026-04-30 post-beta huddle.** PH-first market focus locked; US/EU deferred behind P-6 trigger conditions.

## Overview

Finance Manager is **PH-first**. All marketing, distribution, content, and product decisions optimize for PH. US/EU support continues passively (existing testers grandfathered) but is not a development priority until specific trigger conditions fire (per `plans/cursor/strategic-roadmap-reframe-53be/PARKING_LOT.md` P-6).

## Active Locale (PH)

**Primary:** Filipino (Tagalog) + English. Most PH users are bilingual; Filipino UI is mandatory for sari-sari B2B (S6); web/Android UI ships English-first with Filipino translation as a S2 deliverable per S2 retention investment workstream.

**Currency:** PHP (Philippine Peso, ₱). Display formatting: `₱1,234.56` (Western-style separators since most PH users are bilingual and read both formats).

**Mobile wallet integration:** GCash + Maya (free for PH users per strategic decision §3.4). Direct payment integration via S1.B research workstream.

## Localization Requirements

### 1. Internationalization (i18n) Infrastructure

- **Web frontend (`finance_manager_web`, flagship):** Translation key system in TypeScript. Locale loaded based on user profile + browser preference.
- **Android (`finance_manager_android`):** Native Android resource bundles (`strings.xml` per locale).
- **API backend:** Error messages and notifications translatable; locale passed via header or user preference.

### 2. Regional Financial Conventions

- **Currency formatting:** PHP-aware (`₱` prefix; comma thousands separator); future global expansion handles `1,234.56` vs `1.234,56`.
- **Date/Time formats:** PH default `MM/DD/YYYY` (matches US convention; PH does not have a strong local convention).
- **First Day of Week:** Configurable in `AppProfile`; PH default is Sunday.

### 3. Currency Conversion (Future)

- Multi-currency support: post-S2 feature, not S1 priority.
- Base currency in `AppProfile`. Real-time FX via integration with an exchange rate API (post-S2).

## Priority Regions

| Region | Status | Reason |
|---|---|---|
| **Philippines (PH)** | **Primary** | Wedge audience; thin-margin households; mobile-first; underserved by Western PFMs. |
| **USA** | **Passive** | Existing testers grandfathered as Honorary Founders. New US acquisition deferred behind P-6 trigger (PH MRR ≥ ₱200k/mo OR ZK shipped OR specific inbound). |
| **EU** | **Out of scope** | Revisit only after PH success and US re-engagement decision. GDPR compliance is brought forward via ZK middleware design (S5). |

## Mobile Dependency Gate (Updated 2026-04-30)

- Android product implementation no longer depends on Reflex (Reflex archived 2026-04-30).
- Web flagship (`finance_manager_web`) provides the translation key reference.
- Android i18n shares localization artifacts (translation keys, locale formatting rules, UX copy patterns) with web.
- Filipino translation effort applies to web first; Android follows.

## Implementation Steps (S2 deliverable)

- [ ] Audit `finance_manager_web/` for hardcoded English strings; extract to translation keys.
- [ ] Provide Filipino (Tagalog) translation file for all UI surfaces.
- [ ] Currency formatting helper that respects locale (PHP, future USD/EUR).
- [ ] Date formatting helper.
- [ ] Locale-aware error messages on API.

## References

- Strategic context: `plans/cursor/strategic-roadmap-reframe-53be/00_strategic_context.md` §3.8 (PH-first lock).
- Parking lot: `plans/cursor/strategic-roadmap-reframe-53be/PARKING_LOT.md` P-6 (US re-engagement trigger).
- S2 phase doc: `plans/cursor/strategic-roadmap-reframe-53be/phases/S2_ph_vertical_hardening.md` W5 (retention investment).
