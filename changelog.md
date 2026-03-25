## 2026-03-25

### Xavigate API + Site — User Default Language
- **New** `xavigate-api/migrations/016-add-user-locale.sql` — adds `locale TEXT NOT NULL DEFAULT 'en'` to users table
- **Modified** `xavigate-api/src/index.js` — `getOrCreateUser()` accepts locale param (seeded on INSERT, never overwrites existing); GET/PUT /profile returns/accepts locale; PUT cascades to pulse_users; POST /pulse-subscribe and webhook reactivation copy users.locale; removed 3 assessment-locale fallback lookups (sendBookEmail, gift email, email-change verification)
- **Modified** `xavigate-site/settings.html` — language selector (English / Français) in Preferences with auto-save
- **Modified** `xavigate-site/fr/settings.html` — same language selector with French labels
- **Impact:** Single source of truth for user language preference. All emails, Pulse notes, and UI respect `users.locale`. No more ad-hoc inference from assessments or URL paths.
- **Docs:** `planning/handoffs/2026-03-25-03-xavigate-user-default-language.md`

### Pulse — v3 Three-Axis Note Engine
- 2026-03-25 — Pulse project created. Cloudflare Worker + D1 + Telegram delivery. New diagnostic product: personalized map-linked notes. Three-axis engine (category/touch/function), 20+ topic extractors, quality gate with anti-template + trait pathologizing prevention.

### Xavigate API — E2E Pipeline Fixes (3 bugs)
- **Modified** `xavigate-api/src/map-workflow.js` — renderHTML step now returns R2 key instead of full HTML (was exceeding CF Workflow 1MiB step output limit, blocking all map deliveries)
- **Modified** `xavigate-api/src/index.js` — added `POST /internal/maps/enqueue?id=N` admin endpoint to trigger readiness check + queue enqueue
- **Modified** `xavigate-api/src/index.js` — `/maps/mine` endpoint now returns `web_report_url` and `pdf_url` (dashboard "View Report" button was never showing)
- **Impact:** Map generation pipeline now completes end-to-end. Dashboard shows both PDF download and web report view buttons.

## 2026-03-24

### Diagnostics — Client Privacy Gate (new gate/validator)
- **New** `diagnostics/config/real-clients.txt` — blocklist of real client names
- **New** `diagnostics/gates/client-privacy-gate.sh` — shell scanner (flags real names in any file/directory)
- **New** `diagnostics/gates/client_privacy.py` — Python module for generators (`check_file()`, `check_text()`)
- **Modified** `xavigate-site/scripts/site-release-gate.sh` — added Client Privacy gate (Gate 2, pre-push)
- **Modified** `diagnostics/products/map/pdf/generate-map-pdf.py` — post-generation privacy warning
- **Impact:** Real client data cannot silently reach public-facing artifacts. Gate blocks site deploys if real names detected.

### Xavigate API + Site — Regional Pricing & Stripe Adaptive Pricing
- **Modified** `xavigate-api/src/index.js` — added `locale` and `adaptive_pricing[enabled]` params to all 3 Stripe checkout session endpoints (single item, cart, gift)
- **New** `xavigate-site/data/exchange-rates.json` — static FX rates for 10 currencies
- **New** `xavigate-site/js/regional-pricing.js` — client-side price conversion module (auto-detect currency from browser locale, convert USD to local with ≈ prefix)
- **New** `xavigate-site/scripts/update-exchange-rates.sh` — weekly rate refresh script
- **Modified** `xavigate-site/cart-button-handler.js` — `fmtPrice()` uses regional pricing when available
- **Modified** 32 HTML pages — added `data-usd-cents` attributes (56 total) and `regional-pricing.js` script tags
- **Impact:** Non-USD users see approximate local prices on site (e.g., "≈ 8,62€"). Stripe checkout shows exact local currency via Adaptive Pricing. USD users unaffected.
- **Docs:** `planning/handoffs/2026-03-24-05-xavigate-regional-pricing.md`

### Xavigate Site — Cart French Localization Fix
- **Modified** `cart-button-handler.js` — added `fmtPrice()` locale-aware price formatter, replaced 4 hardcoded `$X.XX` formats
- **Modified** `fr/books.html` — 18 `data-product-name` attributes updated from English to French book titles
- **Modified** `fr/book-organizations-are-built-wrong.html` — 1 `data-product-name` updated to French
- **Impact:** French users now see fully localized cart: buttons ("Ajouter au panier — 9,99 $"), drawer ("Panier"), prices ("9,99 $"), checkout ("Payer")
- **Docs:** `planning/handoffs/2026-03-24-04-xavigate-site-cart-french-fix.md`

---

## 2026-03-22

### Backup System — restic + Cloudflare R2
- **New project:** `~/Projects/backups/` — complete backup infrastructure
- **Daily encrypted incremental backups:** restic → Cloudflare R2 bucket (`sr-backups`)
- **Scope:** M4 (14.9 GB → 9.6 GB), M1 (2.25 GB → 1.76 GB), Cloudflare infrastructure (D1 SQL dumps, DNS records, R2 inventories)
- **Automation:** cron-scheduled daily backup + prune. M4: 3:00 AM. M1: 3:30 AM. Retention: 30 daily, 12 weekly, 6 monthly.
- **Cost:** ~$0.17/month. Complete disaster recovery for both machines.
- **Docs:** `planning/handoffs/2026-03-23-01-backups-r2-restic-setup.md`


### Student Map Pipeline — Phases 1-3 Deployed (xavigate-api, xavigate-site, diagnostics)
- **Database schema:** Added `map_mode` column (TEXT, DEFAULT 'professional') to `intake_profiles` (canonical) and `maps` (denormalized), added `student_school` column to `intake_profiles`, added 27 student-specific columns to `intake_situations` in 6 logical blocks
- **API endpoints:** `POST /intake/profile` accepts `map_mode` and `student_school` parameters; `POST /intake/situation` accepts 27 student intake fields; `POST /intake/submit` loads `map_mode` from DB source of truth and denormalizes once to `maps` table
- **New files:** `xavigate-api/src/student-map-prompts.js` (4 student system prompts + spec), `xavigate-api/src/student-map-report-template.js` (print HTML + tabbed web report renderer)
- **Modified files:** `xavigate-api/src/map-workflow.js` (13 student branch points across all generation steps), `xavigate-api/src/index.js` (profile/situation/submit handlers)
- **Intake form:** `xavigate-site/map-intake.html` updated with Step 0 mode selector (student/professional cards), student-specific profile fields (school, stage), 6-section student situation form (Stage/Context, Options, Pull/Drain, Environment, Pressure/Fear, Horizon)
- **Pre-deploy review:** Fixed 4 critical issues — removed map_mode fallback logic (Rule 1 violation), fixed hardcoded PROMPT_BUNDLE_VERSION, added student_school to migration, accepted 3 orphaned enrichment fields as-is (handled by prompts)

### Student Map Pipeline — Phase 4-5 Deferred
- Phase 4: End-to-end testing (purchase test Map, complete student intake, verify generation + web report + PDF + dashboard)
- Phase 5: Wire `validate-student-report.py` checks into pipeline post-Pass 4 (currently post-hoc only — known regression vector)
- Deferred items: form UI for 3 enrichment fields, parent/guardian consent flow, student-specific marketing page updates

---

## 2026-03-21

### Commerce — Gift a Map Feature Shipped
- **Added** `xavigate-site/gift-map.html` — dedicated gift landing page (4 audience cards, comparison, 3-step flow, personalization + email preview)
- **Added** `POST /checkout-gift` endpoint in both `xavigate-api/src/index.js` and `xavigate-shop-worker/src/index.js`
- **Added** `fulfillGiftPurchase()` in both workers — creates Stripe coupon (100% off, scoped to Map product `prod_U99j5rE3mRTSP2`), generates promo code, emails recipient + buyer
- **Added** D1 `gifts` table — tracks buyer, recipient, promo code, Stripe IDs, status flow (pending → sent → redeemed)
- **Updated** map fulfillment path in both workers — detects gift redemption, sets `redeemed_at` timestamp
- **Updated** site navigation — pink gift icon + "Gift a Map" link (all pages + profile dropdown)
- **Updated** `xavigate-map.html` — gift CTA after pricing section + final CTA
- **Updated** `dashboard.html` — "Share the clarity" card for Map owners
- **Updated** `index.html` — "Gift clarity to someone" link in final CTA
- **Stripe:** New product `prod_UBhZQ5BTTW0amq` (price `price_1TDKARAD6mE4uoyE0J8dVx8y`), D1 product row type=gift
- **Key safeguards:** idempotent webhook, message sanitization (HTML stripped, 500 char limit), email failure → status `failed`, promo code collision retry (5 attempts), buyer email SOT = Stripe session

---

# Changelog

All significant system changes, newest first. Not strategic decisions (that's STATUS.md). Not handoffs (that's planning/handoffs/). This is: what changed in the actual system.

---

## 2026-03-20

### Diagnostics / Offer Study — Student Page Audit Started
- **Added** `planning/handoffs/2026-03-21-02-student-map-study.md`
- **Completed:** Phase 1 audit of the current public student page at `xavigate.com/for-students`
- **Purpose:** keep a running study of student offer, audience, pain families, parent layer, and implementation coherence in one easy-to-find place

### Diagnostics — Student Map v1 Implementation Handoff Drafted
- **Added** `planning/handoffs/2026-03-21-01-student-map-v1-implementation-handoff.md`
- **Purpose:** give Claude a concrete build handoff for implementing Student Map v1 as a wrapper mode on top of the current Map architecture

### Diagnostics — Student Map Wrapper Logic v1 Drafted
- **Added** `diagnostics/docs/STUDENT-MAP-WRAPPER-LOGIC-v1.md`
- **Defined:** translation layer from current Map architecture + student intake into student-specific signals, buckets, and 8-section report output

### Diagnostics — Student Map Sample Output 03 Drafted
- **Added** `diagnostics/docs/STUDENT-MAP-SAMPLE-OUTPUT-03-performing-at-cost.md`
- **Purpose:** test the student report against the hidden-cost case where the student is functioning on paper while paying too much internally

### Diagnostics — Student Map Sample Output 02 Drafted
- **Added** `diagnostics/docs/STUDENT-MAP-SAMPLE-OUTPUT-02-design-business.md`
- **Purpose:** test the student report against the design/business split case (alive vs safe, approved vs workable)

### Diagnostics — Student Map Sample Output 01 Updated
- **Updated** `diagnostics/docs/STUDENT-MAP-SAMPLE-OUTPUT-01-medical-track.md`
- **Change:** added the new `Student Signal Snapshot` layer so the sample output matches the updated report template

### Diagnostics — Student Map Report Template Updated for Signal Layer
- **Updated** `diagnostics/docs/STUDENT-MAP-REPORT-TEMPLATE-v1.md`
- **Change:** restored the score/signal layer as a student-readable `Student Signal Snapshot` section at the front of the report

### Diagnostics — Student Map Sample Output 01 Drafted
- **Added** `diagnostics/docs/STUDENT-MAP-SAMPLE-OUTPUT-01-medical-track.md`
- **Purpose:** test the student report tone and section structure against the high-performing medical-track case

### Diagnostics — Student Map Sample Cases v1 Drafted
- **Added** `diagnostics/docs/STUDENT-MAP-SAMPLE-CASES-v1.md`
- **Defined:** 5 test cases covering prestige pressure, alive-vs-safe choice, hidden cost in a respected path, repeated restart pattern, and family-pressure distortion

### Diagnostics — Student Map Report Template v1 Drafted
- **Added** `diagnostics/docs/STUDENT-MAP-REPORT-TEMPLATE-v1.md`
- **Defined:** 7-section student report structure focused on path/environment clarity, confusion reframe, and next-step guidance

### Diagnostics — Student Map Intake v1 Drafted
- **Added** `diagnostics/docs/STUDENT-MAP-INTAKE-v1.md`
- **Scope:** current context, options, pull vs drain, educational environment, pressure/fear, decision horizon
- **Purpose:** ensure Student Map output answers student questions in student reality instead of defaulting to adult-role framing

### Diagnostics — Student Map Spec Drafted
- **Added** `diagnostics/docs/STUDENT-MAP-SPEC-v1.md`
- **Decision:** start with a Student Map mode layered on the current Map architecture, not a fully separate engine
- **Scope:** student intake, student examples, student output framing, path-choice + path-distress cases

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
