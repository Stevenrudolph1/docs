# Changelog

All significant system changes, newest first. Not strategic decisions (that's STATUS.md). Not handoffs (that's planning/handoffs/). This is: what changed in the actual system.

---

## 2026-03-20

### Landing Pages — All 3 Launch LPs Ready, Migrated to /landing/
- **Audited + fixed** `landing/no-traction.html` (LP-002) — LG2 gate failure resolved (paid tier CTA made non-linked), proof quotes moved adjacent to mid-page and bottom CTAs. Score: 153 → 159/200. All 8 gates pass.
- **Migrated** `direction-confusion.html` → `landing/direction-confusion.html` (LP-003) — all relative paths fixed, canonical URL updated to `/landing/direction-confusion`
- **Confirmed** `landing/wrong-fit.html` (LP-001) already in correct location
- **Added tier card block** to LP-003 — standard "Two levels of read" section, paid tier routes to `../test.html` only (no Map link)
- **Added 301 redirects** in `xavigate-site/_redirects` — `/wrong-fit` and `/direction-confusion` both redirect to `/landing/` equivalents
- **Updated registry** — all 3 launch LPs at `status: ready` with canonical paths corrected (`marketing/registries/landing-pages.json`)
- **Auto-sync wired** — `marketing/.git/hooks/pre-push` now runs `sync-registry-to-db.py` on every push; sync failure is non-blocking

### Campaign Tracker V2 — UI Rebuild + API POST Endpoint
- **Rebuilt** `xavigate-site/admin-campaigns.html` — full V2 redesign (was ~550 lines, now ~640 lines)
  - Landing Pages tab: card grid instead of table rows — each card shows name, LP ID slug, colored status badge, priority tag, pain-family badge, brief/audit/campaign indicators, status dropdown, "↗ open page" link resolving `canonical_landing_path` to live xavigate.com URL, click-to-edit inline notes
  - Campaigns tab: same table structure but added **Copy UTM** button per row (builds full UTM URL from `platform` + `utm_campaign` + LP path, copies to clipboard); inline click-to-edit notes; **+ Add Campaign** modal (fields: campaign_id, name, platform, destination LP, UTM slug, objective)
  - Performance tab: proper empty state with API instructions and field reference
  - Overview cards: green/red/grey color signals instead of flat counts
- **Added** `POST /admin/campaigns` to `xavigate-api/src/index.js` — creates new campaign row in D1 `campaigns` table; returns 201 on success, 409 on duplicate campaign_id
- **Backend unchanged:** D1 schema, auth pattern, PATCH endpoints, sync script all untouched

#### Campaign tracker architecture summary
| Layer | What | Where |
|-------|------|-------|
| UI | `admin-campaigns.html` | `xavigate-site/` — deployed via Cloudflare Pages |
| API | GET/PATCH/POST landing-pages, campaigns; GET/POST performance | `xavigate-api/src/index.js` — Cloudflare Worker at `api.xavigate.com` |
| DB | D1 `renergence-training` — tables: `landing_pages`, `campaigns`, `performance_log` | Cloudflare D1 |
| Auth | `XavAuth.checkAuth()` — admin-gated (`steven@multiplenatures.com`) | `xavigate-site/js/auth.js` |
| Sync | `python3 marketing/scripts/sync-registry-to-db.py` | Pushes JSON registries → D1 |
| Registries | `marketing/registries/landing-pages.json`, `campaigns.json` | Source of truth for LP/campaign metadata |

### Marketing System — Landing Page Operations Layer Added
- **Added** `marketing/canon/MARKETING-16-pain-to-page-routing.md` — operational workflow from pain kernels → user-surface language → ad hook → landing page → assessment → Map
- **Updated** `marketing/canon/MARKETING-15-pain-kernels.md` — linked to companion routing workflow
- **Added** `marketing/briefs/2026-03-20-no-traction-lp-brief.md` — brief for the missing launch landing page covering effort-without-traction / talent-not-converting
- **Added** `marketing/canon/MARKETING-17-landing-page-operations.md` — landing-page status/version/CTA/launch workflow rules
- **Added registries:**
  - `marketing/registries/landing-pages.json`
  - `marketing/registries/campaigns.json`
  - `marketing/registries/performance-log.csv`
- **Effect:** Landing pages are now treated as tracked commercial assets with page IDs, versions, campaign linkage, and performance logging instead of loose files

### Xavigate Site — Landing Directory Created
- **Added** `xavigate-site/landing/README.md`
- **Purpose:** establish `/landing/` as the canonical home for paid-traffic landing pages, separate from evergreen site pages
- **Current state:** structure created; legacy LPs still exist at root during transition

## 2026-03-18

### Recovery System — Built and Deployed
- **New feature:** Automated recovery email system for abandoned checkout + assessment no-purchase
- **New tables (D1):** `checkout_sessions`, `recovery_flows`, `recovery_email_queue`, `email_suppression` (migration 009)
- **New cron triggers:** `0 * * * *` (qualify flows) + `*/15 * * * *` (process send queue)
- **New endpoints:** `GET /recovery/unsubscribe`, `POST /ses-events`, `/admin/recovery/*` (dashboard + queue approval)
- **New admin page:** `xavigate-site/admin-recovery.html` — hold queue approval + dashboard
- **New SES config set:** `xavigate-recovery` (us-east-1) with bounce+complaint suppression
- **Fixed:** `~/bin/send-mail` rewritten in Python/boto3 — special characters no longer break sends
- **Doc:** `xavigate-api/RECOVERY-SYSTEM.md`
- **Handoff:** `planning/handoffs/2026-03-18-04-xavigate-api-recovery-system.md`

### Straight-to-Map Flow — Specced
- **Created** `docs/systems/xavigate-map-flow.md` — system doc for Map purchase flow (pages, state machine, endpoints, emails, D1 changes, implementation stages)
- **Engineering spec:** `planning/handoffs/2026-03-18-01-xavigate-straight-to-map-engineering-spec.md` — exact contracts, migrations, copy
- **Scope:** post-purchase routing, score persistence, teaser screen, intake prefill, generation trigger, magic-link delivery, dashboard, return buyer flow
- **New endpoints needed:** `POST /intake/scores`, enhanced `GET /intake/profile`, enhanced `POST /internal/maps/complete`, `GET /map/:token`, `GET /api/dashboard`
- **New pages needed:** `map-start.html`, `map-delivery.html`, `dashboard.html`; `test.html` modified for paid flow variant

### Product Ladder Simplified to Two Tiers
- **Removed:** Entry Map ($29) deprecated 2026-03-18
- **Current:** Xavigate Insight Assessment (free) → Xavigate Map ($97) only
- **Updated:** `docs/systems/diagnostics.md`, `docs/project-map.md`

## 2026-03-17

### Documentation System — Full Build
- **Created** 7 system pages in `docs/systems/`: diagnostics, commerce, training, social-media, insight-engine, xavigate-site, agents
- **Wired** CLAUDE.md: Session Start scans changelog, Work Tracking adds changelog entries, Domain-Specific Knowledge table updated
- **Wired** Xavi system prompt on M1: session start scans M4 docs changelog via SSH

### Centralized Documentation System Created
- **Created** `docs/` hub with canonical system architecture, changelog, project map, dependencies, and systems docs
- **Scope:** For Steven, Claude, and Xavi to operate the project ecosystem as a system
- **Files created:**
  - `docs/README.md` — entry point and navigation rules
  - `docs/architecture.md` — full stack diagram, integration points, data stores
  - `docs/changelog.md` — this file, system change log
  - `docs/project-map.md` — directory inventory and scope assignments
  - `docs/dependencies.md` — what connects to what, breaking points
  - `docs/systems/content-system.md` — detailed content system architecture
- **Governance:** Integrated with CLAUDE.md auto-load triggers and Session-End Protocol

### Content System Architecture — Built and Wired
- **Created** `content-system/` — canonical content production architecture
  - 4 JSON schemas (insight, kernel, brief, product)
  - 3 gates (insight-gate.py 15/20, monotony-gate.py, imprint-gate.py)
  - Shared Python lib (`lib/content_system.py`)
  - Brief Builder (`builders/brief-builder.py`)
  - Component Registry (`registry/components.yaml` — 25 components)
  - Publication Ledger (`ledger/publications.jsonl`)
- **Wired** Substack producer (`communication/substack/produce-article.py`) — new, replaces `generate-article.sh`
- **Wired** LinkedIn Article Generator (`marketing/linkedin-article-generator/content_system_bridge.py`) — kernel support added
- **Wired** XaviStudio insight gate (`xavistudio/INSIGHT-GATE.md`) — aligned to universal 5 dimensions, threshold raised to 15
- **Wired** Social media ops (`marketing/SOCIAL-MEDIA-OPS.md`) — content system integration section
- **Wired** Content ledger (`marketing/scripts/content-ledger.py`) — sync/export to universal ledger
- **Wired** Insight Engine (`insight-engine/config.py`, `gate.py`, `generate.py`) — schema-compliant output
- **Updated** CLAUDE.md — Content Production Triad (3 chained triggers), self-diagnosing rule
- **Updated** MEMORY.md — Content Production Triad at top
- **Updated** GOV-12 — Content Production Triad section
- **Updated** knowledge/REGISTRY.md — GOV-12/14/17 + content-system section
- **Updated** PROJECT-INDEX.md — content-system added
- **Created** Xavi awareness file on M1 (`CONTENT_SYSTEM_AWARENESS_v1.md`)

### Xavigate Insight Assessment Report Redesign
- Rebuilt as 8-screen guided recognition experience
- Pattern families (30 authored), tension engine, misread/reframe
- New file `xavigate-site/js/foundation-engine.js` (~600 lines)
- Deployed to production

### Oggie Agent Setup (Michael Rudolph)
- OpenClaw agent scaffolding for Michael on Cloudflare Moltworker
- System prompt, knowledge folder, email tool, website update spec
