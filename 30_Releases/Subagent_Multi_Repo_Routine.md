# Multi-Repo Subagent Routine

## Purpose
This runbook standardizes cross-repo execution when a feature requires coordinated updates in:

- `finance_manager_api/`
- `finance_manager_cli/`
- `finance_manager_reflex/`

It operationalizes the existing API-first dependency rule and prevents repo drift.

## Routine (Execution Order)

1. **Impact Mapping (`explore` subagent)**
   - Identify touched contracts, downstream consumers, and risk surface.
   - Output: repo sequence, dependency notes, and stop conditions.
2. **API Implementation (`generalPurpose` subagent)**
   - Implement endpoint/schema behavior and tests.
   - Emit handoff: contract details and compatibility constraints.
3. **CLI Verification (`generalPurpose` or `shell`)**
   - Integrate API changes in CLI and run targeted checks.
   - Emit handoff: pass/fail, observed gaps, required API follow-ups.
4. **Reflex Implementation (`generalPurpose` subagent)**
   - Wire frontend behavior after API/CLI verification.
   - Emit handoff: UX impact and backend assumptions.
5. **Cross-Repo Validation (`shell` subagent)**
   - Run repo-scoped status/diff/test passes and release notes checks.
   - Confirm per-repo feature branch, PR URL/state, and required checks/signoffs.
   - Use local `gh` CLI for PR/status/check operations when managing GitHub workflow from terminal.

## Hard Guardrails

- Never stage or commit files from different repos in one commit step.
- Never implement shared feature work directly on `main`/`master`; use per-repo feature branches.
- Block downstream implementation if upstream contract is unresolved.
- Every repo touch must include changelog/design-doc impact assessment.
- Every repo intended for merge must have PR status and check/signoff state recorded in handoff output.
- Every handoff must use the shared handoff contract.
- Verify `gh auth status` before PR actions if GitHub auth state is uncertain.

## Run-Through Example: Transaction Calendar Program

This example follows the roadmap dependency for calendar aggregates (API -> CLI -> Reflex).

### Step A: Impact Mapping
- Inputs: `design_docs/20_Roadmap/Roadmap_Overview.md`, `design_docs/20_Roadmap/API_Feature_Roadmap.md`, `design_docs/20_Roadmap/Reflex_Feature_Roadmap.md`
- Output:
  - API adds calendar aggregate payload shape.
  - CLI adds baseline verification command/output checks.
  - Reflex consumes API-calculated daily/weekly/monthly totals.

### Step B: API Workstream
- Define contract for:
  - daily totals
  - weekly row totals
  - monthly grand total
- Validate query performance and response stability.

### Step C: CLI Workstream
- Add/adjust command that exercises new API payload.
- Confirm schema compatibility and error handling behavior.

### Step D: Reflex Workstream
- Bind calendar view and day-detail interactions to the new contract.
- Ensure no local heavy aggregation is introduced in frontend.

### Step E: Closeout
- Record unresolved items as explicit follow-up tasks per repo.
- Trigger documentation sync for architecture and roadmap state.
