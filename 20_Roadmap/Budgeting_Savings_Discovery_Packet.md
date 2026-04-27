# Budgeting and Savings Discovery Packet

## Objective
Define bounded contexts and contract options for budgeting/savings without entering implementation.

## Bounded Context Proposal
- `BudgetEnvelope`: period-scoped target per category/group.
- `SavingsGoal`: target amount, target date, contribution cadence.
- `PlannedExpenseLink`: optional tie between envelope and recurring/upcoming expenses.

## Contract Options (API-first Draft)
- `GET /finance/budgets/summary/`
  - Returns period totals: `planned`, `actual`, `variance`, `variance_pct`.
- `GET /finance/savings/goals/trajectory/`
  - Returns per-goal progress points with projected completion date.
- `POST /finance/budgets/envelopes/` and `PATCH /finance/budgets/envelopes/{id}/`
  - For envelope lifecycle management.

## Currency and Aggregation Rules
- All envelope and trajectory aggregate metrics default to user `base_currency`.
- Original-currency transaction detail can remain available in drill views, but does not alter aggregate math.

## Migration/Schema Sketch (No Code Yet)
- New tables:
  - `budget_envelope`
  - `savings_goal`
  - `budget_actual_cache` (optional for performance)
- Foreign-key strategy:
  - Explicit link to user profile and optional category references.

## Open Decisions
- Carryover strategy (`strict reset` vs `rollover residue`).
- Planned expense allocation model (`fixed percentage` vs `priority queue`).
- Goal progress source (`transaction-tag driven` vs explicit transfer mapping).

## Discovery DoD
- Context map agreed.
- Contract option set documented with sample payloads.
- Migration approach captured with risks and deferred decisions.
