# Social Media

**Location:** `~/Projects/marketing/` (scripts, batches, ops doc)
**Canonical doc:** `marketing/SOCIAL-MEDIA-OPS.md`
**Last updated:** 2026-03-17

---

## What It Is

Xavi-post: a stateful, governed workflow for 5 social channels across 2 brands. Content flows from kernel → brief → generation → approval → Late API → scheduled publish. All posts require Steven's explicit "yes."

## Channels

| Channel | Account | Posts/Week | Format |
|---------|---------|-----------|--------|
| Steven LinkedIn | Personal | 4 (Mon/Wed/Fri/Sat) | 500–1,600 chars |
| Steven X | @stevenrudolph | 6 (Mon–Sat) | <280 chars |
| Steven Facebook | Personal | 2 (Tue/Thu) | Cross-post from LinkedIn |
| MNI LinkedIn | Multiple Natures | 2 (Tue/Fri) | Nature-in-context |
| MNI Facebook | Multiple Natures | 2 (Tue/Fri) | Cross-post from MNI LinkedIn |

12 slots/week, ~68 posts/month (Late Build tier: 120/month limit).

## The Xavi-Post Protocol

1. **Read State** — parse `schedule.json` + `_state.json`, identify gaps
2. **Report Status** — dashboard of current + next week slots
3. **Generate** — trace to kernel (KRN-XXX), use Brief Builder, write content, derive visual fields, show Steven
4. **Publish Approved** — upload image to Late `/media`, create post via Late `/posts`, update schedule
5. **Update State** — increment counters, update drift trackers

## Key Files

| File | What |
|------|------|
| `batches/schedule.json` | Master schedule (source of truth) |
| `batches/_state.json` | Drift tracking (engine/style/temperature rotation) |
| `batches/content-ledger.json` | Concept coverage heatmap |
| `01-hopper/pending/` | Posts awaiting approval |
| `01-hopper/approved/` | Posts ready to publish |
| `02-published/` | Archive by week |

## Scripts

| Script | Purpose |
|--------|---------|
| `scripts/xavi-post.py` | Schedule manager, dashboard |
| `scripts/xavi-analytics.py` | Performance, follower growth, pillar report |
| `scripts/content-ledger.py` | Coverage tracking, gap analysis, sync with universal ledger |

## Late API

- **Base:** `https://getlate.dev/api/v1` (NOT api.getlate.dev)
- **Upload:** `POST /media` — field name `files` (plural)
- **Schedule:** `POST /posts` — nested `platforms` array + `mediaItems` + `scheduledFor` (ISO 8601)

## Content System Integration

All social content must trace to a kernel in `insight-engine/kernels.jsonl`. Brief Builder generates channel-specific briefs. Published posts log to `content-system/ledger/publications.jsonl`. See `docs/systems/content-system.md`.
