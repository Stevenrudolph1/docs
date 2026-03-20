# Insight Engine

**Location:** `~/Projects/insight-engine/`
**Canonical doc:** `insight-engine/INSIGHT-ENGINE-V2.md`
**Last updated:** 2026-03-17

---

## What It Is

CLI tool that prevents insight collapse through structured observation capture, distillation, novelty gating, and anti-collapse monitoring. Core flow: observations → insights → kernels → content.

## Data Stores

| File | ID Format | What |
|------|-----------|------|
| `observations.jsonl` | OBS-XXX | Raw patterns from reality (concrete scenes) |
| `insights.jsonl` | INS-XXX | Distilled structural truths (scored, gated) |
| `kernels.jsonl` | KRN-XXX | Atomic sentences (<15 words) — idea genomes |

## CLI Commands

All via `python3 generate.py {command}`:

**Capture:** `observe`, `insight` (manual add)
**Generate:** `distill` (obs → insight), `cross` (lens intersections), `invert` (flip test), `kernel` (extract atomic), `expand` (cross-domain), `harvest` (from external), `content` (draft from kernel)
**Quality:** `gate` (novelty check), `gate --all`
**Monitor:** `report` (health dashboard), `brief` (daily snapshot)

## Quality Controls

- **Reality Ratio:** 70% observation-derived (60% minimum). Below 60% → gate blocks non-observation insights.
- **Novelty Gate:** Jaccard similarity >0.7 on titles → reject. >0.5 → merge. 3+ shared tags → merge.
- **Domain Diversity:** 10 canonical domains, saturation warning at >40%.

## Downstream Consumers

| Consumer | How |
|----------|-----|
| Content System | Brief Builder reads kernels via `query-kernels.py` |
| XaviStudio | Kernels → video scripts |
| LinkedIn Article Generator | Kernels tagged `article` |
| Social Media (xavi-post) | Kernels tagged `social` |
| Book chapters | Insight clusters → outlines |

## Upstream Sources

Observations come from: maps, books, consulting sessions, news/social (`harvest`), lived experience, Xavi sessions (synced from M1 via `sync-xavi-observations.py`).

## Key Files

| File | What |
|------|------|
| `generate.py` | CLI entry point (12 subcommands) |
| `config.py` | Paths, domains, lenses, models, gate thresholds |
| `gate.py` | Novelty checker (Jaccard similarity, reality ratio) |
| `report.py` | Domain coverage, S/A/P matrix, saturation warnings |
| `query-kernels.py` | Helper for downstream consumers |
| `sync-xavi-observations.py` | Syncs M1 observations → observations.jsonl |
