# finance-manager-design-docs

Public **active system design** vault for the Finance Manager ecosystem. This repository holds durable technical truth — how the built system works — for the API, CLI, Rust middleware seam, and cross-cutting system design.

Roadmap sequencing, governance runbooks, and tactical plans live in the **private** parent workspace (`strategy/`, `governance/`, `plans/`). They are intentionally not mirrored here.

## Start here

- **[00_Dashboard.md](00_Dashboard.md)** — vault index (Obsidian-friendly wikilinks)
- **[CHANGELOG.md](CHANGELOG.md)** — vault change history

## Vault layout

| Area | Path | Contents |
| :--- | :--- | :--- |
| **Guidelines & encryption** | `00_*.md` | Coding guidelines, encryption strategy |
| **API** | [`api_docs/`](api_docs/00_API_Overview.md) | Endpoints, models, business logic, auth, data access |
| **Web (flagship SPA)** | [`web_docs/`](web_docs/00_Web_Overview.md) | React client, routing, PWA/offline, pages, build/deploy |
| **CLI** | [`cli_docs/`](cli_docs/00_CLI_Overview.md) | Command architecture, auth/HTTP, configuration |
| **Rust** | [`rust_docs/`](rust_docs/00_Rust_Overview.md) | Planned middleware layer (scaffold today) |
| **System design** | [`40_System_Design/`](40_System_Design/05_Deployment_Strategy.md) | Deployment, logging, PWA/offline, Android sync, security posture |

## Component READMEs (implementation home)

| Component | Repository | Status |
| :--- | :--- | :--- |
| Web (flagship SPA) | `finance_manager_web` | Tight Beta |
| API | `finance_manager_api` | Tight Beta |
| CLI | `finance_manager_cli` | Alpha |
| Rust tools (numerics) | `finance_manager_rust_tools` | Tight Beta |
| Rust middleware | `finance_manager_rust_middleware` | Scaffold |

## Ecosystem clone layout

When all repos are checked out as siblings under one parent directory, cross-repo doc links use paths like `../../finance_manager_api/...`. Runtime coordination (VPS signup sheet, handoff templates) is in the parent `governance/` tree — not in this vault.

## Historical note

Older roadmap, release-ops, reflex, and research drafts were **removed from this public repo** during the 2026-06-30 scope cleanup. Archive copies may exist in the private parent workspace under `strategy/archive/design_docs/`.
