# Diagnostics

**Location:** `~/Projects/diagnostics/`
**Canonical doc:** `diagnostics/README.md`, `diagnostics/docs/product-architecture/XAVIGATE-PRODUCT-ARCHITECTURE-v1.md`
**Last updated:** 2026-03-17

---

## What It Is

Unified engine for MN×MI assessment and diagnostic products. One instrument (MNTEST), three diagnostic layers (Alignment → Structure → Positioning), delivered as a staged product staircase.

## Product Ladder (TWO TIERS — updated 2026-03-18)

| Product | Price | Layer | Input | Output |
|---------|-------|-------|-------|--------|
| **Xavigate Insight Assessment** | Free | Alignment | MNTEST (76 items) | 9 MN + 10 MI scores + 8-screen guided recognition report |
| **Xavigate Map** | $97 | + Structure + Positioning | Deeper intake (45–60 min) | Complete diagnostic — person × arrangement × interpretation |

| **Xavigate Map Update** | Included | Structure + Positioning | Followup intake (changed fields only) | Updated map reflecting life changes |

**Deprecated (removed 2026-03-18):** Entry Map ($29), Foundation Scan (renamed), Full Map (renamed), MNTEST Report (absorbed), Career Explorer (absorbed — code still present at `products/career-explorer/`).

**Canonical rules:**
- Always Alignment → Structure → Positioning. Never reordered.
- Xavigate Insight Assessment is genuinely useful standalone — not a teaser.
- No progressive paywalls inside any tier.

## Other Product Lines

Diagnostics is one of three product categories:

| Category | Products |
|----------|----------|
| **Diagnostics** | Xavigate Insight Assessment (free), Xavigate Map ($97) |
| **Books** | Free gateway books + paid titles via Gumroad/Stripe. See `books/` |
| **Courses** | Renergence Practitioner Training (5 courses, 2 tiers: $600 Standard / $1,500 With Live Instructor). See `docs/systems/training.md` |

## Core Engine

| Component | Location | What |
|-----------|----------|------|
| MN Engine | `engine/routes/mn_engine.py` | Score vectors → capability activation matrix, expression clusters |
| Structural Model | `products/map/tools/structural_model.py` | map.json → structural analysis (misalignment, arrangement fit) |
| Career Matcher | `engine/careers/matcher/matcher.py` | MN/MI scores → career alignment (649-career database) |
| Clustering | `engine/clustering/archetype.py` | Activation clusters → archetype labels |

## Pipeline

```
intake.json → generate-map-json.py → map.json
  → structural_model.py → map_state.json
  → mn_engine.py → map_state.json (+ capability_state)
  → auto-generate.py → map.md (narrative)
  → generate-map-pdf.py (PDF) OR web-report/ (interactive HTML)
```

## Key Tools

| Tool | Input → Output |
|------|---------------|
| `products/map/tools/generate-map-json.py` | intake.json + demand-analysis.md → map.json |
| `products/map/tools/structural_model.py` | map.json → map_state.json |
| `engine/routes/mn_engine.py` | map.json → capability_state in map_state.json |
| `products/map/pdf/generate-map-pdf.py` | map.md → PDF |
| `products/map/tools/generate-map-report.py` | map.json + map_state.json → HTML report |
| `products/map/fulfillment/auto-generate.py` | Orchestrates full pipeline (daemon, polls shop worker) |

## Validation

- 4 real case studies in `validation/shared/profiles/`
- 10+ validation profiles in `validation/shared/cases/`
- 15 stress tests in `validation/shared/stress/`
- Tools: `revalidate.py`, `mn_validation.py`, `validation-run.py`, `create-test-case.py`

## Integrations

| Surface | How |
|---------|-----|
| xavigate-api (CF Worker) | Scoring endpoints, map intake, report delivery |
| xavigate-site | `test.html` (MNTEST), `map-intake.html`, `map-delivery.html`, `dashboard.html` |

## Governance Hierarchy

GOV-00 > MN-GOV-00/01 > REPORT_GOVERNANCE > XAVIGATE-PRODUCT-ARCHITECTURE-v1 > Diagnostics canon > MN book > Product specs > Templates > Code. See `diagnostics/docs/SOURCE-OF-TRUTH.md`.

## Structure

```
diagnostics/
├── canon/          # LOCKED theory & specs (alignment, mn, shared)
├── engine/         # Shared computation (routes, clustering, careers, scoring)
├── products/       # mntest, mntest-report (legacy), career-explorer (legacy), map
├── adapters/       # Integration seams (api/, site/)
├── validation/     # Test data + acceptance criteria
├── benchmark/      # Competitor analysis, must-win scorecard, gap analysis
├── docs/           # Product architecture (LOCKED), working docs
└── fonts/          # IBM Plex Sans/Serif, Libre Baskerville
```
