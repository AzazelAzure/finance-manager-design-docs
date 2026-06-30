# Build, Deploy & Testing

Related: [Web Overview](00_Web_Overview.md) · [Deployment Strategy](../40_System_Design/05_Deployment_Strategy.md)

How the web client is built, configured, deployed in the ecosystem, and verified in CI.

## Toolchain

| Tool | Version / notes |
| :--- | :--- |
| Node | 22 (CI, Docker build stage) |
| Package manager | npm (`package-lock.json` — use `npm ci`) |
| Bundler | Vite 8 |
| UI | React 19 + TypeScript |
| PWA | `vite-plugin-pwa` (Workbox `generateSW`) |

## npm scripts

From [`package.json`](../../finance_manager_web/package.json):

| Script | Command | Purpose |
| :--- | :--- | :--- |
| `dev` | `vite` | Dev server **:5173** (HMR) |
| `build` | `tsc -b && vite build` | Production bundle → `dist/` |
| `preview` | `vite preview` | Serve `dist/` on **:4173** |
| `test` | `vitest run` | Unit tests |
| `lint` | `eslint .` | ESLint |

## Environment variables

Only `VITE_*` variables are exposed to the browser.

| Variable | Required | Purpose |
| :--- | :--- | :--- |
| `VITE_API_BASE_URL` | Yes (prod builds) | Django API origin, no trailing slash |
| `VITE_STAGING_API_BASE_URL` | Staging | Used when host is `jsdevtesting.thehivemanager.com` |
| `VITE_CLIENT_BUILD` | CI / release | Build id; falls back to `GITHUB_SHA` or `"dev"` |

Local: `.env.local` (gitignored). See [`finance_manager_web/README.md`](../../finance_manager_web/README.md) Lane A/B.

### Build-time define

[`vite.config.ts`](../../finance_manager_web/vite.config.ts) sets `__FM_CLIENT_BUILD__` → `src/lib/clientBuild.ts` → `X-Client-Build` header.

**Implication:** changing API URL or client build id requires **rebuild** of static assets — nginx container does not read runtime env for API base.

## Docker / nginx

[`Dockerfile`](../../finance_manager_web/Dockerfile): multi-stage `node:22-alpine` build → `nginx:1.27-alpine` serving `dist/`.

- SPA routing: `docker/nginx-spa.conf` → `try_files` to `index.html`.
- Parent monorepo compose services: `web`, `web-blue`, `web-green`.
- Proxy routes public HTTPS (`:8443`) to active color.

Build args pass `VITE_API_BASE_URL` at image build time.

## Development lanes (summary)

| Lane | Web | API | Doc detail |
| :--- | :--- | :--- | :--- |
| **A — local** | `npm run dev` :5173 | `runserver` :8000 | Web README § Lane A |
| **B — jsdevtesting tunnel** | dev or preview behind Cloudflare | `api.thehivemanager.com` or staging API | Web README § Lane B |
| **VPS static** | `scripts/vps-serve.sh` | remote API | Web README § VPS |

**Runtime coordination:** when multiple agents share local compose ports, use parent `governance/Runtime_Signup_Sheet.md` — not removed `design_docs/30_Releases/` paths.

## Production artifacts (`dist/`)

| File | Role |
| :--- | :--- |
| `index.html` | SPA shell + SEO meta |
| `assets/*` | Hashed JS/CSS |
| `sw.js`, `workbox-*.js` | Service worker |
| `version.json` | SW eviction hash |
| `manifest.webmanifest` | PWA manifest (from `public/`) |

## CI

[`.github/workflows/web-ci.yml`](../../finance_manager_web/.github/workflows/web-ci.yml):

1. `npm ci`
2. `npx tsc -b` (project references build mode)
3. `npm test`

Runs on push/PR to `main`. Dependabot maintains npm + Actions bumps.

## Testing strategy

**Vitest only** — no Playwright/E2E in repo today.

| Area | Test files |
| :--- | :--- |
| Offline outbox | `offline/outbox.test.ts`, `queueMutating` coverage via integration tests |
| Overlays | `transactionOutboxOverlay.test.ts`, `upcomingOutboxOverlay.test.ts`, `lookupsOutboxOverlay.test.ts`, `profileOutboxOverlay.test.ts` |
| Connectivity | `offline/connectivity.test.ts` |
| Auth helpers | `state/auth.test.ts` |

Co-locate new tests with the module they cover. Prefer testing pure overlay/queue functions over full mount tests.

### Manual smoke checklist

- [ ] Login / logout / refresh on production or inactive color origin
- [ ] Dashboard loads snapshot; STS displays
- [ ] Quick-add transaction online + offline queue + drain
- [ ] Installed PWA: seed progress, offline read, reconnect sync
- [ ] Profile pay-cycle change reflects STS
- [ ] Export CSV from Data Hub
- [ ] Support ticket submit
- [ ] `ClientBuildUpgradeGate` when API rejects build (staging test)

Blue-green: parent `scripts/fm_server_beta.sh smoke --color <inactive>` after inactive-color rebuild.

## SEO & static public assets

| Asset | Path |
| :--- | :--- |
| `robots.txt` | `public/robots.txt` |
| `sitemap.xml` | `public/sitemap.xml` |
| OG / JSON-LD | `index.html` + per-route Helmet |

Research-level SEO tasks (SSG, font preload) may be tracked in the **private parent** distribution-channel research — not duplicated here.

## Related deployment docs

| Doc | Location |
| :--- | :--- |
| Ecosystem blue-green | Parent `deploy/BLUEGREEN_SWITCHOVER.md` |
| VPS beta scripts | Parent `scripts/fm_server_beta.sh` |
| API CORS troubleshooting | `finance_manager_api` repo `docs/CORS_PRODUCTION_TROUBLESHOOTING.md` |
| System design deployment | [`40_System_Design/05_Deployment_Strategy.md`](../40_System_Design/05_Deployment_Strategy.md) |

---

**[Return to Overview](00_Web_Overview.md)** · **Next:** [i18n, Theme & Guided Tours](08_i18n_Theme_and_Guided_Tours.md)
