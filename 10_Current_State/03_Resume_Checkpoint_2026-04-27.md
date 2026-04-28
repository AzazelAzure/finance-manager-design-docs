# Resume Checkpoint - 2026-04-27

## Context

This checkpoint captures in-flight implementation status while manual verification is still pending. It is intended as the restart anchor after long rebuild/recreate cycles.

## Behavior Changes Captured In Code (This Wave)

- API token obtain accepts username or email identifier (`/api/token/`, `/api/token/auth/`).
- API token refresh maps missing-user refresh cases to `401 InvalidToken` instead of server `500`.
- Reflex login form binds submit payload from `form_data` (`username`, `password`) to avoid stale-state credential submission.
- Splash preview account-health rows now rely on status values mapped to localized labels instead of placeholder key rendering.

## Known Runtime Finding During Verification

- A login regression that looked like credential mismatch was traced to empty runtime user data after rebuild/recreate.
- API runtime was confirmed on Postgres at time of diagnosis; auth failures were due to data-state reset, not frontend payload mutation.

## Pending Manual Verification Before Final Sendoff

1. Confirm splash account-health copy renders translated labels with no placeholder text.
2. Confirm login succeeds in UI with known-good user credentials after fresh environment rebuild.
3. Confirm refresh-token behavior for deleted-user paths returns `401` only.
4. Re-run onboarding path (source creation + initial transaction) after clean build.

## Git Status Snapshot (Captured 2026-04-27)

### Workspace root

- Modified repositories: `design_docs`, `finance_manager_api`, `finance_manager_reflex`, `finance_manager_cli`, `finance_manager_android`
- Multiple untracked root-level files/directories are present and should be triaged before final commit packaging.

### `finance_manager_api` (`main`)

- Modified: `finance_api/urls.py`
- Untracked: `finance/tests/user_tests/test_token_login_identifier.py`

### `finance_manager_reflex` (`main`)

- Modified: `finance_manager_reflex/features/auth/view.py`
- Modified: `finance_manager_reflex/features/profile/state.py`

### `finance_manager_cli` (`main`)

- Working tree clean

### `finance_manager_android` (`main`)

- Working tree clean

### `design_docs` (`main`)

- Modified: `10_Current_State/01_Runtime_Validation_Checklist.md`
- Modified: `api_docs/05_Authentication_and_Security.md`
- Modified: `reflex_docs/05_Feature_Modules_Reference.md`
- Modified: `.obsidian/workspace.json`
- Modified: `20_Roadmap/Beta_Contract_Compatibility_Matrix.md`
- Untracked: `.gitignore`
- Untracked: `20_Roadmap/Budgeting_Savings_Discovery_Packet.md`
- Untracked: `20_Roadmap/Calendar_Phase1_Contract_and_UI.md`
- Untracked: `20_Roadmap/Persistent_Balance_History_Decision_Record.md`
- Untracked: `20_Roadmap/Volatile_Bills_and_Partial_Payment_Rulebook.md`
- Untracked: `20_Roadmap/orchestration_manager/`

## Resume Sequence

1. Run rebuild/recreate and validate runtime health.
2. Verify API is using expected database backend and that expected seed/test users exist.
3. Execute manual verification items in order.
4. Reconcile untracked root-level files before preparing final PR(s).
