# truth.md — Reality vs. Claim Audit

A deep inspection of every file in this repository, compared against the claims made in `README.md`.

**Audit date:** 2026-05-24
**Scope:** Full repository tree at HEAD
**Method:** Enumerated every tracked file, read source, resolved imports, checked for build/config artifacts.

---

## TL;DR

The `README.md` describes a large, polished, production-grade product ("World Monitor Pro") with a Tauri desktop app, Vite/TypeScript frontend, 60+ Vercel edge functions, 22 gRPC services defined with 92 protobuf files, AI/ML pipelines, 100+ data connectors, 435+ news feeds, 21-language i18n, native installers, and team/SSO features.

**The repository contains effectively none of that.** It holds:

- 1 marketing `README.md`
- 1 `LICENSE` file
- 1 `.gitignore` (a stock AL/Dynamics 365 Business Central template — unrelated to the project)
- 10 TypeScript files in `app/` that **cannot compile or run** because ~130 of their imports point to modules (`@/services/*`, `@/components/*`, `@/config/*`, `@/types`, `@/utils/*`, `@/generated/client/*`) that are **not present** in this repo.
- A handful of JSON data files and 4 small helper scripts in `scripts/`
- 2 screenshot images

There is **no** `package.json`, **no** `tsconfig.json`, **no** Vite config, **no** Cargo/Tauri config, **no** Rust code, **no** Vercel functions, **no** `.proto` files, **no** AI/ML code, **no** desktop installers, **no** releases, **no** test suite, **no** CI, and **no** `SECURITY.md` (even though the README links to one).

The repository is best characterized as a **marketing README plus a small subset of orphaned source fragments** from some other (private?) codebase.

---

## 1. Repository inventory (ground truth)

Top-level tree:

```
.
├── .gitignore                 (371 B — AL/Dynamics 365 template, off-topic)
├── LICENSE                    (AGPL-3.0)
├── README.md                  (4.9 KB — marketing copy)
├── app/                       (10 TypeScript files, ~9,222 LOC, unbuildable)
├── images/                    (2 image files)
└── scripts/
    ├── data/                  (8 JSON files)
    ├── lib/                   (2 helper scripts)
    └── shared/                (20 JSON/JS shared files)
```

### 1.1 `app/` (TypeScript modules)

| File | LOC | Notes |
|---|---:|---|
| `app-context.ts` | 95 | Type/interface definitions |
| `country-intel.ts` | 1,191 | Class `CountryIntelManager`, uses ~30 missing imports |
| `data-loader.ts` | 3,222 | Class `DataLoaderManager`, imports ~80 missing modules |
| `desktop-updater.ts` | 219 | Tauri updater wrapper; depends on missing `@/services/tauri-bridge` |
| `event-handlers.ts` | 1,570 | Class `EventHandlerManager`, many missing imports |
| `index.ts` | 8 | Barrel file re-exporting the above |
| `panel-layout.ts` | 2,100 | Class `PanelLayoutManager`, many missing imports |
| `pending-panel-data.ts` | 30 | Small queue helper |
| `refresh-scheduler.ts` | 119 | Scheduler class |
| `search-manager.ts` | 668 | Class `SearchManager`, many missing imports |
| **Total** | **9,222** | |

Every non-trivial file pulls from path aliases (`@/...`) that resolve to nothing in this repo. A representative (incomplete) list of **missing** modules referenced by `app/`:

- `@/types`, `@/utils`, `@/utils/keyword-match`, `@/utils/sanitize`, `@/utils/country-flag`, `@/utils/cross-domain-storage`, `@/utils/reverse-geocode`
- `@/config` and 14 sub-modules (`@/config/geo`, `@/config/countries`, `@/config/panels`, `@/config/pipelines`, `@/config/tech-companies`, `@/config/ai-datacenters`, …)
- `@/components` and 26 sub-modules (panels, modals, widgets…)
- `@/services` and **~80** sub-modules (`rpc-client`, `tauri-bridge`, `ml-worker`, `gdelt-intel`, `radiation`, `wildfires`, `i18n`, `entitlements`, `billing`, `checkout`, …)
- `@/generated/client/worldmonitor/{conflict,intelligence,market,news,research}/v1/service_client` — the supposed protobuf-generated clients

None of these directories exist on disk. The TypeScript here is a non-compilable fragment.

### 1.2 `scripts/`

- `scripts/data/` — 8 JSON files (≈ 308 KB total): `cascade-rules.json`, `country-codes.json`, `curated-bases.json` (~47 KB), `entity-graph.json`, `forecast-evaluation-benchmark.json`, `forecast-historical-benchmark.json`, `mirta-processed.json` (~228 KB), `prediction-tags.json`.
- `scripts/lib/` — 2 files: `crypto.cjs` (1.7 KB), `thermal-escalation.mjs` (13.6 KB).
- `scripts/shared/` — 20 small JSON/JS files (country bboxes, ISO code maps, RSS allow-list, commodity/crypto/etf/stablecoin/stock lists, an ACLED OAuth helper, a geo-extract helper). Total ≈ 60 KB.

These are static reference data and a couple of utility helpers. None of them constitute a "service", a "connector", an "AI pipeline", or a "data ingestion stack".

### 1.3 `images/`

- `new-world-monitor.png` (1.6 MB) — not referenced from README
- `worldmonitor-7-mar-2026.jpg` (334 KB) — referenced as the dashboard screenshot

### 1.4 Notably **absent** files

- `package.json`, `package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`
- `tsconfig.json`, `vite.config.ts`, any bundler config
- `Cargo.toml`, `tauri.conf.json`, `src-tauri/`
- Any `.proto` file (the README claims 92)
- Any Vercel function (`api/`, `vercel.json`)
- Any Dockerfile, deployment manifest, or Railway config
- Any test file or test runner config
- Any GitHub Actions workflow (`.github/workflows/`)
- `SECURITY.md` — README links to `./SECURITY.md`, but the file does not exist (broken link)
- Any release artifact in `Releases`
- `index.html`, any frontend entry point
- Any localization / i18n catalog
- Any AI/ML model, prompt, or pipeline file

---

## 2. Claim-by-claim verification

Legend: ✅ Supported · ⚠️ Partial / weak evidence · ❌ Unsupported by repo · 🚩 Misleading / broken

### 2.1 Headline claims

| # | Claim from README | Reality in repo | Verdict |
|---|---|---|---|
| 1 | "Far beyond the original repo with advanced geopolitical tracking, AI briefs, and 100+ connectors." | No connector code present. ~80 `@/services/*` imports reference missing files. | ❌ |
| 2 | "Real-time global intelligence dashboard" | No dashboard, no HTML, no frontend entry point, no rendering code. | ❌ |
| 3 | Download badges → `../../releases` for Windows `.exe` and macOS Apple Silicon | No releases artifacts in the repo; the badges link to a repo-relative `releases` page that, per repo contents, has nothing built from this source. | 🚩 |
| 4 | Dashboard screenshot `images/worldmonitor-7-mar-2026.jpg` | File exists. | ✅ |

### 2.2 "What It Does" bullets

| # | Claim | Reality | Verdict |
|---|---|---|---|
| 5 | "435+ curated news feeds across 15 categories, AI-synthesized into briefs" | No feed list of that size. `scripts/shared/rss-allowed-domains.json` (~6.5 KB) is an allow-list of domains, not a feed registry. No AI synthesis code. | ❌ |
| 6 | "Dual map engine — 3D globe (globe.gl) and WebGL flat map (deck.gl) with 45 data layers" | No globe.gl, deck.gl, MapLibre, or Three.js code or dependency declaration present. `LAYER_TO_SOURCE` is imported from a missing `@/config` module. | ❌ |
| 7 | "Cross-stream correlation — military, economic, disaster, escalation signals" | Only `scripts/lib/thermal-escalation.mjs` (13.6 KB) is on disk. The cross-correlation logic referenced from `data-loader.ts` (`signal-aggregator`, `cross-source-signals`, `geo-convergence`, `temporal-baseline`, …) is **all missing**. | ⚠️ trace only |
| 8 | "Country Intelligence Index — composite risk scoring across 12 signal categories" | `app/country-intel.ts` exists (1,191 LOC) but depends on ~30 missing modules and cannot run. No "12 categories" enumeration is verifiable. | ⚠️ stub only |
| 9 | "Finance radar — 92 stock exchanges, commodities, crypto, 7-signal composite" | `scripts/shared/stocks.json` (3.6 KB), `commodities.json`, `crypto.json` exist as **small static lists** (not 92 exchanges). No "7-signal composite" code. | ❌ |
| 10 | "Local AI — run everything with Ollama, no API keys required" | No Ollama integration, no model code, no AI client. | ❌ |
| 11 | "5 site variants from a single codebase (world, tech, finance, commodity, happy)" | `SITE_VARIANT` is imported from missing `@/config`. No variant configs on disk. | ❌ |
| 12 | "Native desktop app (Tauri 2) for macOS, Windows" | `app/desktop-updater.ts` (219 LOC) references `@tauri-apps/plugin-updater` and a missing `@/services/tauri-bridge`. **No `src-tauri/`, no `Cargo.toml`, no `tauri.conf.json`, no Rust code at all.** | 🚩 |
| 13 | "21 languages with native-language feeds and RTL support" | No i18n catalogs, no translation files, no RTL CSS. `@/services/i18n` is missing. | ❌ |

### 2.3 "World Monitor Pro" professional features

Every bullet in this section (equity research, Grand Chessboard framework, GDP/inflation/rates analytics, Slack/Telegram/WhatsApp/Email alerts, central bank tracking, scenario analysis, "<60s refresh, 22 services, 1 key", saved watchlists, premium map layers, satellite + SAR imagery, AI agents with MCP, 50,000+ infrastructure assets, 100+ connectors including Splunk/Snowflake/Sentinel, REST API, webhooks, bulk export, team workspaces with SSO/MFA/RBAC, white-label, Android TV, cloud/on-prem/air-gapped, dedicated onboarding) is **unsupported**. None of these features have implementation files in this repo.

| # | Claim | Reality | Verdict |
|---|---|---|---|
| 14 | "22 services" via protobufs | No `.proto` files, no `@/generated/client/*` actually generated. Only 5 service clients are *referenced* by imports (conflict, intelligence, market, news, research). | ❌ |
| 15 | "92 protos" | Zero `.proto` files in the tree. | ❌ |
| 16 | "50,000+ infrastructure assets mapped" | `scripts/data/curated-bases.json` (~47 KB) is the closest artifact — orders of magnitude smaller than 50,000 records. | ❌ |
| 17 | "100+ data connectors (Splunk, Snowflake, Sentinel...)" | No connector code; no mention of Splunk/Snowflake/Sentinel anywhere outside the README. | ❌ |
| 18 | "Team workspaces with SSO/MFA/RBAC" | No auth code, no IdP integration, no RBAC model. | ❌ |
| 19 | "Android TV app for SOC walls" | No Android sources. | ❌ |
| 20 | "Cloud, on-prem, or air-gapped deployment" | No deployment manifests of any kind. | ❌ |

### 2.4 Quick Start / installers

| # | Claim | Reality | Verdict |
|---|---|---|---|
| 21 | "Download `worldmonitor_x64.exe`" | No build pipeline, no installer config; whether an exe exists in Releases is not verifiable from the repo, but **nothing in this repo builds one**. | 🚩 |
| 22 | "Download `worldmonitor_macOS.dmg` … drag the **Hermes Agent** icon" | The instructions reference a "Hermes Agent" — an unrelated product name that does not appear anywhere in this repository. Looks like copy-pasted instructions from another project. | 🚩 |

### 2.5 Tech Stack table

| # | Claim | Reality | Verdict |
|---|---|---|---|
| 23 | Frontend: Vanilla TypeScript, Vite, globe.gl + Three.js, deck.gl + MapLibre GL | TypeScript files exist (uncompilable). No Vite config, no map libraries on disk or in any manifest. | ⚠️ TS only |
| 24 | Desktop: Tauri 2 (Rust) with Node.js sidecar | No Rust, no Tauri config, no sidecar. Only a 219-line TS updater file uses Tauri types. | ❌ |
| 25 | AI/ML: Ollama / Groq / OpenRouter, Transformers.js | None of these are present. | ❌ |
| 26 | API Contracts: Protocol Buffers (92 protos, 22 services), sebuf HTTP annotations | Zero `.proto` files, no generated clients. | ❌ |
| 27 | Deployment: Vercel Edge Functions (60+), Railway relay, Tauri, PWA | No `api/` dir, no `vercel.json`, no Railway config, no service worker, no manifest. | ❌ |
| 28 | Caching: Redis (Upstash), 3-tier cache, CDN, service worker | No Redis client, no cache implementation on disk (`@/services/persistent-cache` is a missing import). | ❌ |

### 2.6 Flight Data / Wingbits

| # | Claim | Reality | Verdict |
|---|---|---|---|
| 29 | "Flight data provided gracefully by Wingbits" | No code uses Wingbits. `fetchFlightDelays`, `fetchMilitaryFlights`, `initMilitaryVesselStream` are imported from missing modules. | ❌ |

### 2.7 License

| # | Claim | Reality | Verdict |
|---|---|---|---|
| 30 | "AGPL-3.0 … See LICENSE" | `LICENSE` is present and is the standard GNU AGPL-3.0 text. | ✅ |
| 31 | "Copyright (C) 2024-2026 Elie Habib." | Asserted in README; not separately verifiable. | ⚠️ |

### 2.8 Security Acknowledgments

| # | Claim | Reality | Verdict |
|---|---|---|---|
| 32 | "See our [Security Policy](./SECURITY.md)" | **`SECURITY.md` does not exist.** Broken link. | 🚩 |
| 33 | Researcher acknowledgement (Cody Richard, 2026) | Not verifiable from the repo (no disclosure record, no advisories tab content). | ⚠️ |

---

## 3. Concrete defects discovered

1. **Broken README link**: `./SECURITY.md` does not exist (README §"Security Acknowledgments").
2. **Wrong/copy-pasted install instruction**: macOS step 3 tells the user to drag a "**Hermes Agent**" icon to Applications — that product name is unrelated to World Monitor and does not appear anywhere else in the repo.
3. **Off-topic `.gitignore`**: The file is the **AL / Dynamics 365 Business Central** template (ignores `.alpackages/`, `*.app`, `rad.json`, etc.). None of those apply to a TypeScript/Tauri project. It does **not** ignore `node_modules/`, `dist/`, `target/`, `.env`, etc.
4. **Unbuildable `app/`**: Without `package.json`, `tsconfig.json`, and ~130 referenced modules under `@/services`, `@/components`, `@/config`, `@/utils`, `@/types`, `@/generated`, the TypeScript files cannot be type-checked, bundled, or executed.
5. **Unreferenced asset**: `images/new-world-monitor.png` (1.6 MB) ships in the repo but is not used.
6. **`scripts/` cannot run standalone**: `scripts/shared/acled-oauth.mjs` and `scripts/shared/geo-extract.mjs` are helpers expected to be imported by a larger pipeline that isn't here.

---

## 4. Overall assessment

| Dimension | Score |
|---|---|
| Marketing surface (README) | Extensive, polished, ambitious. |
| Buildable code | **None.** No build system, no entry point, no dependency manifest. |
| Implementation coverage of README claims | **< 5%.** Only data fixtures and disconnected TS fragments are present. |
| Documentation accuracy | **Low.** Broken `SECURITY.md` link; cross-product copy-paste ("Hermes Agent"); installation references to releases that this repo doesn't produce. |
| Repository hygiene | Poor: off-topic `.gitignore`, unused image, missing manifests, no CI, no tests. |

**Conclusion.** With respect to the question "does this repository deliver what its README claims?", the answer is **No.** What is shipped here is a marketing README, a license, a screenshot, ~9 KLOC of orphaned TypeScript that does not compile, and a small bundle of static JSON reference data. The "World Monitor Pro" product described in the README — if it exists at all — lives elsewhere; this repository is not it.

---

## 5. Suggested next steps (non-binding)

1. Either bring in the missing source (`services/`, `components/`, `config/`, `utils/`, `types/`, `generated/`, `src-tauri/`, `package.json`, `tsconfig.json`, `vite.config.ts`, `Cargo.toml`, `tauri.conf.json`, `api/`, `.github/workflows/`) so the codebase matches the README — **or** rewrite the README to honestly describe what is present (a small set of TS modules and JSON data).
2. Add the missing `SECURITY.md` or remove the link.
3. Fix the macOS install instructions ("Hermes Agent" → correct app name) or remove them until installers exist.
4. Replace the AL/Dynamics 365 `.gitignore` with one suitable for a TypeScript / Vite / Tauri project (`node_modules/`, `dist/`, `target/`, `.env*`, etc.).
5. Remove or use `images/new-world-monitor.png`.
6. Tone down or substantiate the quantitative claims ("435+ feeds", "92 protos", "22 services", "50,000+ assets", "100+ connectors", "21 languages") — none are evidenced by repository contents.
