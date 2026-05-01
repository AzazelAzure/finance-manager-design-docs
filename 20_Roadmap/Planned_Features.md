# Planned Features & Detailed Requirements

This document is a **feature backlog and requirement sketch**, not the phase schedule. **Sequencing, market focus, and gates** are defined in `plans/cursor/strategic-roadmap-reframe-53be/` (see `README.md`, `phases/`, `PARKING_LOT.md`). Items here must be checked against the wedge ("safe to spend") and PH-first locks in `00_strategic_context.md` before scheduling.

Each feature listed here is slated for development across beta and later phases, and should eventually be tied to a governed tactical plan under `plans/cursor/<phase-stage>/<sub-plan>/` (registry: `plans/_governance/plan_registry.md`).

## 💰 Investment & Asset Tracking

### Investment Portfolio

- **Capabilities**: Allow users to track holdings in stocks, mutual funds, and ETFs.
- **Valuation**: Integration with market data APIs to provide End-of-Day (EoD) valuations.
- **Calculations**: Automated computation of total portfolio value based on quantity (e.g., 2.458 shares of ABC).
- **Disclaimer**: Mandatory UI disclaimer directing users to verify with their official brokers for final accuracy.

### Crypto Currency Tracking

- **Capabilities**: Real-time or periodic value tracking for major cryptocurrencies.
- **Failsafes**: Robust error handling for external API calls; caching of the last known "good" price if the API is unreachable.
- **Refresh Logic**: Manual or automatic refresh on dashboard load.

## 📊 Calculators & Forecasting

### Savings Calculators

- **Capabilities**: Project future savings based on current contributions and interest rates.
- **Goal Tracking**: Integration with the "Savings Goals" feature to show time-to-completion.

### Investment Calculators

- **Capabilities**: Compound interest and growth projections for investment accounts.

## 💳 Advanced Financial Tools

### Credit Card Strategy

- **Capabilities**: Tracking of individual card balances, APRs, and "Payoff Order" suggestions (e.g., Avalanche vs. Snowball).

### Budgeting Engine

- **Capabilities**: Per-category monthly budgeting with "rollover" and visual alert systems.

### Upcoming Expenses 2.0

- **Capabilities**: Advanced cash-flow forecasting based on confirmed upcoming bills and projected income.

---

*For strategic timeline and triggers, see `plans/cursor/strategic-roadmap-reframe-53be/README.md`. For historical pre-reframe narrative, see [Roadmap Overview](file:///home/pproctor/Documents/python/finance_manager/design_docs/20_Roadmap/Roadmap_Overview.md).*