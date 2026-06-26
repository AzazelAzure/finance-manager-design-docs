# Server Logging & Support Strategy

## Overview

Operational logging for the Django API uses **Loguru** with per-user diagnostic files for authenticated traffic. Support intake (F-012) persists tickets in Postgres and triggers operator notifications via the F-014 `[FM-NOTIFY]` Celery dispatcher.

## Per-user diagnostic logs (F-013)

| Path | Purpose |
|------|---------|
| `/app/logs/diagnostic/{user_id}.log` | Per-`AppProfile.user_id` (UUID) diagnostic file |
| `/app/logs/incidents/incident_{ticket_id}.log` | Bug-ticket 10-minute window dump (F-012) |

**Context:** `UserLogContextMiddleware` binds `uid` from `request.user.appprofile.user_id`. Anonymous requests do **not** create per-user files.

**Levels:** INFO and above for authenticated UUID contexts.

**Rotation:** 10 MB per file → renamed to `.log.1`. Probabilistic 14-day retention purge on write.

### Operator procedure — intake → logs

1. Open `SupportTicket` row (DB/admin) and note `uid` (UUID) and `id` (ticket UUID).
2. Tail diagnostic file: `tail -f /app/logs/diagnostic/{uid}.log` (inside API container or shared `logs` volume).
3. For bugs, read incident bundle: `cat /app/logs/incidents/incident_{ticket_id}.log`.
4. `[FM-NOTIFY]` emails list the same relative paths under `Relevant-Files`.

**Privacy:** Filenames use UUID only. Do not enable `LOG_FULL_USERNAME=1` in production. Notify emails are UUID-only (no username/email in body).

## Support intake (F-012)

- API: `POST /finance/support/tickets/` (authenticated, rate-limited).
- Web: `/app/support` (bug always; feature requests when `BETA_FEATURE_REQUESTS_ENABLED=true`).
- Weekly feature digest: Celery beat Monday 09:00 (`TIME_ZONE`), last 7 days, `emailed=False`.

## Operator notifications (F-014)

- Celery task: `finance.tasks.notify.notify_operator`
- Subject prefix: `[FM-NOTIFY]`
- Real-time bug reports enqueue notify after incident dump (async; submission does not fail if email fails after retries).

## Celery services (blue/green)

Run **one** `celery-beat` scheduler and at least one `celery-worker` against shared Redis. Without workers, digests and notify tasks will not execute.

```bash
./scripts/fm_server_beta.sh status   # confirm redis + workers
podman-compose -p fm-beta -f docker-compose.bluegreen.yml up -d celery-worker-blue celery-beat redis
```

## Long-term goals

- Centralized log aggregation (post-beta).
- Automated alerting for system-wide failures (partially addressed by F-014 threshold alerts).
