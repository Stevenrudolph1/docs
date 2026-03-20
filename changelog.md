# Changelog

All significant system changes, newest first. Not strategic decisions (that's STATUS.md). Not handoffs (that's planning/handoffs/). This is: what changed in the actual system.

---

## 2026-03-20

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
