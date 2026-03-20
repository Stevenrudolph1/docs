# Content System

**Location:** `~/Projects/content-system/`
**Canonical doc:** `content-system/CONTENT-SYSTEM-ARCHITECTURE.md`
**Created:** 2026-03-17

---

## What It Is

The canonical architecture for all content production. Defines vocabulary, component classes, schemas, gates, and the flow from raw sources to published output.

## Key Files

| File | What |
|------|------|
| `CONTENT-SYSTEM-ARCHITECTURE.md` | Binding canon — vocabulary, flow, rules |
| `registry/components.yaml` | 25 registered components across 8 classes |
| `schemas/*.json` | 4 schemas: insight, kernel, brief, product |
| `gates/insight-gate.py` | Universal quality gate (15/20, 5 dimensions) |
| `gates/monotony-gate.py` | Portfolio diversity enforcement |
| `gates/imprint-gate.py` | Authority compliance check |
| `builders/brief-builder.py` | Kernel → channel-specific brief |
| `lib/content_system.py` | Shared Python library for all producers |
| `ledger/publications.jsonl` | Cross-channel publication history |

## The Flow

Sources → Insight Engine → **INSIGHT GATE** (15/20) → Kernel Store → **Brief Builder** → Producer → **Ledger**

## Insight Gate Dimensions

| Dimension | Hard fail below |
|-----------|----------------|
| Recognition | 3 |
| Compression | 3 |
| Reframe | 3 |
| Grounding | — |
| Generativity | — |

Pass: 15/20. Each dimension 0–4.

## Connected Producers

| Producer | Location | Channel |
|----------|----------|---------|
| Substack | `communication/substack/produce-article.py` | substack |
| LinkedIn Articles | `marketing/linkedin-article-generator/pipeline.py` | linkedin-article |
| Social Media | `marketing/scripts/xavi-post.py` | social-linkedin, social-x, social-facebook |
| XaviStudio | `xavistudio/` | video |

## CLAUDE.md Enforcement

Content System Pipeline trigger fires on ALL content keywords (write, article, post, content, generate, etc.) as part of the Content Production Triad (GOV-14 + Copy Gate + Content System Pipeline).
