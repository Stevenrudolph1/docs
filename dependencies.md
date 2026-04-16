# Dependencies

What connects to what. If you change X, what else might break.

**Last updated:** 2026-03-17

---

## Content Production Chain

```
insight-engine/observations.jsonl
  ↓ (generate.py distill/kernel)
insight-engine/insights.jsonl
  ↓ (insight-gate.py scores)
insight-engine/kernels.jsonl
  ↓ (brief-builder.py reads)
content-system/builders/brief-builder.py
  ↓ (generates briefs, checks monotony gate)
content-system/ledger/publications.jsonl ←── monotony-gate.py reads
  ↓
Channel Producers:
  • communication/substack/produce-article.py
  • marketing/linkedin-article-generator/pipeline.py
  • marketing/scripts/xavi-post.py
  • xavistudio/ (manual, follows INSIGHT-GATE.md)
```

**If you change:** `content-system/schemas/*.json` → check all producers still comply
**If you change:** `insight-engine/kernels.jsonl` format → check `content-system/lib/content_system.py` still reads it
**If you change:** gate thresholds → update `content-system/gates/`, XaviStudio INSIGHT-GATE.md, CONTENT-SYSTEM-ARCHITECTURE.md

## Commerce Chain

```
xavigate-site/ (product pages, checkout links)
  ↓ (Stripe Payment Links)
xavigate-shop-worker/ (webhook handler)
  ↓ (D1 write + SES email)
Customer gets: book download email OR course enrollment
  ↓ (if course)
xavigate-training/ (portal) + xavigate-api/ (progress tracking)
```

**If you change:** Stripe products → update shop worker product table + site payment links
**If you change:** D1 schema → update shop worker AND training worker AND API worker

## Diagnostic Chain

```
xavigate-site/test.html (MNTEST 76Q)
  ↓ (scores submitted → Foundation layer free)
xavigate-api/ (scoring endpoint)
  ↓ (results stored)
diagnostics/engine/ (MN engine, structural model)
  ↓
diagnostics/products/map/ (Xavigate Map $97 — all 3 layers)
```

**Product tiers:** Xavigate Insight Assessment (free) → Xavigate Map ($97)
**If you change:** MNTEST questions → update `diagnostics/products/mntest/question-bank/`
**If you change:** MN engine scoring → revalidate with `diagnostics/validation/`

## Map Update Chain

```
xavigate-site/map-update-intake.html (3-step wizard)
  ↓ (POST)
xavigate-shop-worker/ → /intake/map-update endpoint
  ↓ (D1 write)
map_updates + map_summary_points tables (migration 017)
  ↓ (read by)
xavigate-shop-worker/ → /map/update-points endpoint
  ↓
diagnostics/products/map-update/ (generation + PDF)
```

**If you change:** map_updates or map_summary_points schema → update shop worker endpoints + intake form
**If you change:** Map Update generation prompts → update `diagnostics/products/map-update/`

## Training Chain

```
xavigate-training/ (static HTML portal — Cloudflare Pages)
  ↓ (API calls)
xavigate-training-worker/ (Cloudflare Worker — magic link auth, progress, enrollments)
  ↓ (D1 read/write)
Shared D1 database (same as API + shop worker)
```

**If you change:** D1 schema → update training worker alongside API and shop worker
**If you change:** training content → manual deploy via wrangler (GitHub auto-deploy broken)

## Security Chain

```
security/ (audit report, threat model, remediation plan)
  ↓ (implemented in)
xavigate-api/src/input-sanitizer.js → sanitizeObject()
xavigate-api/src/output-validator.js → validateAndClean()
xavigate-api/src/pi-scorer.js → scoreFlags(), scoreValidationEvents()
xavigate-api/src/alert-dispatcher.js → dispatchAlert() → AWS SNS/SES
xavigate-api/src/security-logger.js → D1 security_events table
  ↓ (monitored via)
GET /admin/security/events, GET /admin/security/summary
```

**If you change:** sanitizer rules → update pi-scorer thresholds + red-team tests (`security/scripts/redteam-e2e.sh`)
**If you change:** alert destinations → update wrangler secrets (ALERT_PHONE_NUMBER) + SES config

## Governance Chain

```
knowledge/governance/GOV-00–17
  ↓ (referenced by)
renOS/checkers/ (compliance enforcement)
  ↓ (used by)
All content producers, book generator, training content
```

**If you change:** a GOV doc → check renOS checkers still align, check CLAUDE.md triggers

## Agent Chain (M1 ↔ M4)

```
M4 (SSOT): all code, data, governance
  ↑↓ SSH
M1: Xavi agent, Beacon bot, cron jobs
  • Xavi reads M4 code/governance via SSH
  • Todo tracker on M4, thin wrapper on M1
  • Governance sync: planning/scripts/push-governance-m4-to-m1.sh
```

**If you change:** file paths on M4 → Xavi's SSH commands may break
**If you change:** governance docs → run sync script to M1
