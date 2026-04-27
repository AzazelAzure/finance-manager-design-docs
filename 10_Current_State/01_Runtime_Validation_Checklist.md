# Runtime Validation Checklist

Use this checklist after rebuilds/restarts to prevent stale-image false negatives when validating frontend changes.

## Scope

- Workspace root: `/home/pproctor/Documents/python/finance_manager`
- Primary control scripts:
  - `scripts/fm_docker.sh`
  - `scripts/fm_services.sh`

## A) Container Runtime (Podman/Docker Compose Path)

1. Rebuild and recreate when validating fresh code:
   - `./scripts/fm_docker.sh rebuild`
2. Confirm containers are healthy:
   - `./scripts/fm_docker.sh status`
3. Confirm frontend route is reachable:
   - `https://financemanager.local:8443`
   - `http://localhost:8080` is also exposed by the local proxy for HTTP-only checks.
4. If output seems stale, verify startup logs for current run:
   - `podman logs --tail 120 finance-manager-reflex`
   - `podman logs --tail 120 finance-manager-api`

## B) Local Service Runtime (Non-container Path)

1. Start local services:
   - `./scripts/fm_services.sh start`
2. Confirm running:
   - `./scripts/fm_services.sh status`
3. Validate frontend route:
   - `http://localhost:3000`
4. Port expectations for local integration:
   - Django API listens on `http://127.0.0.1:8000`.
   - Reflex backend/event traffic is forced to `http://127.0.0.1:8001` so it does not collide with Django.

## C) Smoke Tests For UI Change Validation

Run these checks in the target runtime URL after login:

1. Dashboard route loads without fatal UI/API errors.
2. Expected widget/layout changes are visible.
3. Dashboard quick filters update in place (no unintended redirect).
4. Cookie consent behavior matches current requirement:
   - show once before accept
   - remain hidden after accept + refresh/revisit

## D) Upcoming Expenses Filter Semantics

Validate this exact behavior:
- Initial page load defaults to current month.
- Clear filters followed by apply/load returns all expenses.
- Existing explicit filters (month/paid/recurring) still function.

## E) Failure Triage Order

When behavior does not match expectations:
1. Confirm correct runtime URL.
2. Confirm fresh build/recreate completed successfully.
3. Confirm container/service status is healthy.
4. Inspect API and Reflex logs for contract/runtime errors.
5. Re-run the smoke checklist before concluding regression.

## F) Authentication Runtime Guardrails (Post-Rebuild)

Use this sequence before diagnosing frontend login regressions:

1. Confirm API runtime database backend from inside the API container:
   - `podman exec finance-manager-api uv run python manage.py shell -c "from django.conf import settings; print(settings.DATABASES['default'])"`
2. Confirm expected users exist in Postgres:
   - `podman exec finance-manager-db psql -U finance_user -d finance_db -c "select count(*) as users from auth_user;"`
3. If API login returns `401` for known-good credentials, verify account presence before frontend debugging.
4. Treat empty `auth_user` after restart/rebuild as a data-state issue (seed/reset) rather than an auth contract regression.

## G) Manual Verification Gate (Current)

Pending manual verification before final signoff of this change wave:

1. Splash/account-health labels render localized values (no placeholder keys).
2. Login succeeds from UI for both known username and known email identifiers.
3. Token refresh path returns `401` (not `500`) when refresh token references deleted user.
4. Onboarding source + initial transaction flow works end-to-end with canonical source naming.
