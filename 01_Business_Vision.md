# Business Vision & User Personas

**Major revision:** 2026-04-30 post-beta huddle. Wedge sentence formalized; primary persona reframed from "Average Joe vs Finance Bro" to "thin-margin PH household"; PH-first market focus locked.

## Overview

The Finance Manager is a PH-first SaaS that helps people on thin margins decide what they can actually spend before payday. Built and operated by a single human (HitM) with AI agent workforce. Strategic plan canonical at `strategy/strategic-roadmap-reframe-53be/`.

## The Wedge (Single Source of Brand Truth)

> **"The personal finance app that tells you what's actually safe to spend before payday — built for people living on thin margins, not people optimizing surplus."**

This sentence is the brand. It belongs on the landing page hero, the dashboard primary KPI, every social post screenshot, every PH community thread, and app store descriptions when published.

## User Personas

### Primary: The "Living Thin" Household (PH-first)

- Lives in PH (or US/EU expat with PH ties; OFW families).
- Income real but variable (freelance, part-time, multi-source, GCash/Maya wallet first).
- Multiple recurring obligations competing for the same pesos.
- Practices "partial bill payment" survival math (pay 60% of electric to keep it on, 100% of rent, defer phone) on a regular basis.
- Burned by Western PFMs that assume surplus and zero-based budgeting.
- Mobile-first, often offline, low-RAM Android device, intermittent connectivity.

### Secondary ("whales"): "Finance Bro" Power Users

Multi-currency, investment tracking, AI-assisted planning. They subsidize the primary persona via paid AI tier. **They are not the marketing target.** They self-discover from the same channels.

### Retired persona: "Average Joe"

The original "Average Joe" framing was a target list, not a strategy. Replaced by "thin-margin PH household" which is more specific, more underserved, and more aligned with the wedge.

## Monetization Strategy

**Subscription-only** (locked 2026-04-30 post-beta huddle). Ads and user-data monetization formally rejected (`strategy/strategic-roadmap-reframe-53be/00_strategic_context.md` §4).

### Tiers (PH-anchored pricing)

| Tier | PHP/mo | What's included |
|---|---|---|
| Free | ₱0 | Safe-to-spend, manual entry, single account, GCash/Maya CSV import (PH only), ~10 AI credits/mo |
| Pro (PH) | **₱249**/mo list | Multi-account, automatic GCash/Maya parsing, 100 AI credits/mo, priority support, family-share (`01_unit_economics_and_costs.md` §2.0) |
| Pro+ AI | **₱349**/mo list | Pro + **250 AI credits/mo**, AI planning sessions, advanced predictions |
| Founding Lifetime | ₱999–₱1,499 one-time | **First 100 seats** hard cap (planning lock 2026-05-01) |
| Honorary US Founder | $0 | US testers grandfathered (pre-PH-pivot era) |

Full pricing model and unit economics at `strategy/strategic-roadmap-reframe-53be/01_unit_economics_and_costs.md`.

## Core Competitive Advantages

- **Wedge alignment:** "Safe to spend" is the *only* PFM that explicitly serves thin-margin survival math. Western competitors (Monarch, Copilot, YNAB) assume surplus.
- **PH-native:** GCash/Maya ingestion (free for PH users), Filipino-language UI, PHP-anchored pricing, mobile-wallet-first billing.
- **Zero-Knowledge encryption (S5+, future):** structural revenue defense and brand differentiator. "We genuinely cannot read your data" — none of the Western competitors can claim this.
- **Mobile-first offline:** Android-native with offline-first SQLite + delta sync, designed for spotty PH connectivity. Browser-only PFMs fail this persona.

## Strategic Phases

S1–S6 multi-year roadmap; canonical at `strategy/strategic-roadmap-reframe-53be/`.

| Phase | Focus | Status |
|---|---|---|
| S1 | Public Beta Launch (PH-only) | Active (Stage S1.A → S1.B) |
| S2 | PH Public Launch + Scaling | Pending |
| S3 | Android Scaling and Feature Parity | Pending |
| S4 | Trust & Reputation Building | Pending |
| S5 | ZK Middleware (revenue defense) + US re-engagement | Conditional |
| S6 | Sari-Sari B2B Vertical | Conditional |

## Operating Model

- **Single human (HitM) + AI agent workforce.** No team unless explicitly stated.
- **Solo founder velocity:** 10hr/day, 55hr/week ceiling during Sprints; 6hr/day, 30hr/week during Decompression.
- **Cursor cap as forcing function** (no overage purchases).
- **Family/health gate quarterly review** (first 2026-06-30).
- **Per-feature color-cycle deployment** on blue-green VPS (one feature at a time on inactive color).

## References

Strategic Plan canonical: `strategy/strategic-roadmap-reframe-53be/README.md`.
Operating governance: `governance/`.
Vocabulary: `governance/glossary.md`.
