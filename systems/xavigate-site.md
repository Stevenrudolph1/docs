# Xavigate Site

**Location:** `~/Projects/xavigate-site/`
**URL:** xavigate.com (renergence.com 301s here)
**Hosting:** Cloudflare Pages — git push to `main` = auto-deploy
**Last updated:** 2026-04-16

---

## What It Is

The active Xavigate website. Plain HTML5 + CSS + JS, no build system, no framework. ~275 public pages (326 total HTML files including utility/template files). Product staircase: Xavigate Insight Assessment (free) → Xavigate Map ($97). Book library. Practitioner tools. Paid-traffic landing pages are now being organized under `/landing/`.

## Page Structure

| Category | Pages | Rules |
|----------|-------|-------|
| Product pages | homepage, audience doors, how-it-works, product pages, utility | No backend language (Copy Gate enforced) |
| Framework pages | multiple-natures, multiple-intelligences, renergence, research | Full framework language permitted |
| Articles | 23 article pages | GOV-14 writing method |
| Book pages | 41 book landing pages | Cover = `<img>` tags only, never CSS |
| Utility | faq, contact, 404 | Standard |

**Page-Type Matrix:** `PAGE-TYPE-MATRIX.md` — mandatory read before writing any page. Declares page type, audience state, product step, next click, forbidden moves.

## Copy Gate (Quality Enforcement)

- **Banned phrases:** `config/banned-frontend-phrases.txt` (33 phrases)
- **Phrase map:** `config/phrase-map.md` (banned → allowed translations)
- **Pre-push hook:** `.git/hooks/pre-push` runs lint automatically
- **Scripts:** `scripts/lint-banned-phrases.sh`, `scripts/site-release-gate.sh`

## Deploy Pipeline

1. `scripts/lint-banned-phrases.sh` (must pass)
2. Score changed pages via `scripts/RELEASE-SCORECARD.md` (min 85 product, 75 other)
3. `scripts/site-release-gate.sh` (confirms both)
4. Git push to main (auto-deploys)
5. `scripts/site-release-gate.sh --post-deploy` (production verify)

## Key Directories

| Path | What |
|------|------|
| `landing/` | Canonical home for paid-traffic landing pages (new) |
| `css/landing.css` | Single unified stylesheet |
| `js/` | Analytics, Meta Pixel, auth, checkout, foundation-engine, and related site scripts |
| `config/` | Copy Gate SSoT (banned phrases, phrase map) |
| `scripts/` | Release gates, lint, smoke tests, deploy verify |
| `tools/` | AI Toolkit files + zips |
| `images/covers/rendered/` | Book cover PNGs/JPGs (never CSS-rendered) |
| `sr-design-system/` | Design tokens CSS, cover rendering system |
| `books/` | Book landing pages + download links |

## Key Integrations

| Integration | How |
|-------------|-----|
| xavigate-shop-worker | Stripe checkout links from product pages |
| xavigate-api | Xavigate Insight Assessment scoring (`test.html`) and related diagnostic flows |
| Content System | All page copy governed by Content Production Triad |

## Cloudflare Config

- `_redirects` — old routes → 301, `/auth/*` → API proxy
- `_headers` — cache: JS/CSS 60s, HTML no-cache, images 1 day
