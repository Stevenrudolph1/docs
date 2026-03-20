# System Architecture

**Last updated:** 2026-03-17

---

## The Stack

```
┌─────────────────────────────────────────────────────┐
│                    USER SURFACES                      │
│  xavigate.com  │  training.xavigate.com  │  Telegram │
│  (Cloudflare   │  (Cloudflare Pages +    │  (OpenClaw│
│   Pages)       │   Worker + D1)          │   M1)     │
└───────┬────────┴──────────┬──────────────┴─────┬─────┘
        │                   │                    │
┌───────┴───────┐  ┌────────┴────────┐  ┌───────┴──────┐
│  xavigate-    │  │  xavigate-      │  │  Xavi Agent  │
│  shop-worker  │  │  api             │  │  (M1)        │
│  (CF Worker)  │  │  (CF Worker)    │  │              │
│  Stripe,D1,   │  │  MNTEST, Map    │  │  Beacon Bot  │
│  SES email    │  │  scoring, auth  │  │  (courses)   │
└───────────────┘  └────────┬────────┘  └──────────────┘
                            │
┌───────────────────────────┴──────────────────────────┐
│                  DIAGNOSTIC ENGINE                    │
│  diagnostics/                                        │
│  Foundation (free) → Entry Map ($29) → Xavigate Map ($97)│
│  engine/ (MN engine, structural model)               │
│  canon/ (locked theory specs)                        │
└──────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────┐
│               CONTENT PRODUCTION SYSTEM               │
│                                                       │
│  insight-engine/     content-system/     Producers:   │
│  observations →      schemas, gates,     • Substack   │
│  insights →          brief builder,      • LinkedIn   │
│  kernels             registry, ledger    • xavi-post  │
│                                          • XaviStudio │
│  Flow: Source → Insight Gate (15/20) → Kernel →       │
│        Brief Builder → Producer → Ledger              │
└──────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────┐
│                 FRAMEWORK / IP LAYER                  │
│                                                       │
│  books/ (18 manuscripts)    knowledge/ (GOV-00–17)   │
│  renOS/ (4 compliance       design-system/ (tokens,  │
│   checkers)                  marks, covers)           │
│  multiple-natures/ (MN      training/ (RPT spec,     │
│   theory, MNTEST Q-bank)     course content)         │
└──────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────┐
│                   OPERATIONS LAYER                    │
│                                                       │
│  planning/ (STATUS.md, handoffs, governance, todos)  │
│  renergence-intelligence/ (ren.py, signals, scores)  │
│  communication/ (Substack repo, MailerLite, partners)│
│  marketing/ (social media, Late API, analytics)      │
│  skills/ (Claude skill files)                        │
└──────────────────────────────────────────────────────┘

Infrastructure:
  M4 (MacBook Pro) — SSOT for all code, data, governance
  M1 (Mac Mini)    — Xavi agent, Beacon bot, cron jobs
  Cloudflare       — Pages (site, training), Workers (shop, API), D1 (database)
  Stripe           — Payments (books, courses, maps)
  AWS SES          — Transactional email (steven@xavigate.com)
  Late API         — Social media scheduling (5 channels)
  MailerLite       — Email lists (~400 subscribers)
  GitHub           — Source repos (planning, marketing, knowledge, substack, books)
```

## Key Integration Points

| From | To | How |
|------|----|-----|
| Insight Engine | Content System | Kernels store (`kernels.jsonl`) is the shared data |
| Content System | All Producers | Brief Builder generates channel-specific briefs |
| Content System | Publication Ledger | Every published piece logs here |
| Monotony Gate | Publication Ledger | Reads recent pubs to check diversity |
| xavigate-site | xavigate-shop-worker | Stripe checkout links, book delivery |
| xavigate-site | xavigate-api | MNTEST scoring, map intake |
| xavigate-training | xavigate-api | Course progress, auth |
| diagnostics | xavigate-api | Engine routes, scoring |
| Xavi (M1) | M4 (SSH) | Reads code, governance, tasks |
| renOS checkers | All content | Compliance validation |

## Data Stores

| Store | Location | Format | What |
|-------|----------|--------|------|
| Observations | `insight-engine/observations.jsonl` | JSONL | Raw observations from reality |
| Insights | `insight-engine/insights.jsonl` | JSONL | Scored candidate insights |
| Kernels | `insight-engine/kernels.jsonl` | JSONL | Approved, producible insight units |
| Publications | `content-system/ledger/publications.jsonl` | JSONL | Everything published, all channels |
| Component Registry | `content-system/registry/components.yaml` | YAML | All content system components |
| Social Schedule | `marketing/batches/schedule.json` | JSON | Weekly social media calendar |
| Content Ledger | `marketing/batches/content-ledger.json` | JSON | Concept coverage tracking |
| Todo Tracker | `planning/todo-tracker/data/tasks.json` | JSON | All tasks across projects |
| ISBN Registry | `planning/book-management/isbn-data.json` | JSON | Book ISBNs and production status |
| D1 Database | Cloudflare D1 | SQLite | Users, enrollments, products, purchases |
