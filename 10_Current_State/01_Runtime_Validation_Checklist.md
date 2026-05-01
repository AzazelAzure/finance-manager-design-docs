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

## H) BP7 Beta Launch Snapshot (2026-04-29)

Use this snapshot to restore the current known-good beta launch runtime.

- Active color: `blue`
- Inactive color: `green`
- Web commit hash (blue): `c758e106a17a`
- Web commit hash (green): `c758e106a17a`
- API commit hash (blue): `2a482dd75259`
- API commit hash (green): `2a482dd75259`
- Web image/build (blue): `f8bfc4daa6daf80d8625ff180ffdb45607a20beb8cba8efe5c7736117bc6554a` (`2026-04-29 15:29:42 UTC`)
- Web image/build (green): `9ddedc1b8d70aca9b5e2ee34e5ad7fc6bb2bc49b7cdace18236de0cf4a2956aa` (`2026-04-29 15:30:07 UTC`)
- DB migration version (latest applied): `token_blacklist.0013_alter_blacklistedtoken_options_and_more` (`2026-04-28 14:46:09 UTC`)
- Health check result (blue):
  - `https://api.thehivemanager.com/api/health/` -> `{"status":"ok"}`
  - `https://thehivemanager.com/` -> `200 OK` (serving web static)
  - `https://jsdevtesting.thehivemanager.com/` -> `200 OK`
- Health check result (green):
  - `web-green` container responds `HTTP/1.1 200 OK` on internal check
  - `api-green` container status healthy
- Last successful smoke timestamp: `2026-04-29T15:30:25Z`

### Runtime posture

- Proxy is intentionally configured to route production web hostnames to `web-blue` (blue-only web traffic cutover).
- Reflex runtime is removed from live request path; containers are stopped (`fm-beta_reflex-blue_1`, `fm-beta_reflex-green_1` exited).
- Dashboard `Quick add -> +Bill` is intentionally disabled pending redesign of bill-flow semantics.
- Restore note: the `+Bill` disable is now tracked in git at `finance_manager_web/src/components/dashboard/QuickActions.tsx`; keep it disabled until the bill-flow redesign is approved.

### Swap command (to move traffic back to color-aware mode)

```bash
cd ~/finance_manager
cp proxy/nginx.bluegreen.conf.bak-<timestamp> proxy/nginx.bluegreen.conf
cat > proxy/active_color.conf <<'EOF'
map $request_uri $fm_active_color {
    default green;
}
EOF
podman rm -f fm-beta_proxy_1
podman-compose -p fm-beta -f docker-compose.bluegreen.yml --env-file .secrets/server.env up -d proxy
```

### Rollback command (restore this exact blue-only snapshot)

```bash
cd ~/finance_manager
# Ensure blue-only nginx file is present in proxy/nginx.bluegreen.conf
cat > proxy/active_color.conf <<'EOF'
map $request_uri $fm_active_color {
    default blue;
}
EOF
podman rm -f fm-beta_proxy_1
podman-compose -p fm-beta -f docker-compose.bluegreen.yml --env-file .secrets/server.env up -d proxy
podman stop fm-beta_reflex-blue_1 fm-beta_reflex-green_1
```
