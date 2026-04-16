# Training

**Location:** `~/Projects/training/` (specs + content), `~/Projects/xavigate-training/` (portal)
**Canonical doc:** `training/TRAINING-ARCHITECTURE-2026-02-15.md`
**Last updated:** 2026-03-17

---

## What It Is

Renergence Practitioner Training (RPT) — 1 course with 7 sequential modules teaching diagnostic practice through exercises, drills, walkthroughs, and case work. Delivered via web portal and Telegram bot (Beacon).

## Course Structure

One course. Seven sequential modules (M1 through M7). No branching — work through in order. Module 1 is free and open. Modules 2-7 are paid.

### Tiers

| Tier | What | Price |
|------|------|-------|
| **Materials Only** | Full course materials, exercises, case walkthroughs. No submission, no review, no completion. | $300 |
| **Standard** | Full course materials + learning portfolio + expert review + two revisions + completion credential. | $600 |
| **With Live Instructor** | Same as Standard + up to 2 hours individual time with Steven Rudolph. | $1,500 |

### Portal Programs

The training portal (`training.xavigate.com`) organizes content into 3 programs:

| Program | Portal file |
|---------|------------|
| Foundations | `programs/foundations.html` |
| MN Training | `programs/mn-training.html` |
| Map Training | `programs/map-training.html` |

## Delivery Channels

| Channel | What | Access |
|---------|------|--------|
| **Web portal** | `training.xavigate.com` — static HTML, CF Pages | Magic link auth |
| **Beacon bot** | Telegram, OpenClaw agent on M1 | Invite codes |

## Case Submission & Review

1. Practitioner writes case (Situation → Domain ID → Structural Analysis → Intervention → Self-Critique)
2. Submit via form (email MVP)
3. AI review (Claude) against 6-dimension rubric
4. Steven spot-checks calibration
5. Feedback: Pass / Revise / Fail
6. 1 free revision, 2nd revision $100 fee

## Key Files

| File | What |
|------|------|
| `training/TRAINING-ARCHITECTURE-2026-02-15.md` | Complete course design, case system, book mapping |
| `training/renergence-practitioner/TRAINING-SPEC.md` | RPT specification |
| `xavigate-training/README.md` | Portal overview + deploy instructions |
| `xavigate-training/admin/docs/` | Full admin documentation (10 HTML pages) |

## Commerce Integration

See `docs/systems/commerce.md` — Stripe purchases create enrollment rows, magic link auth gates portal access.

## Beacon Bot Integration

Beacon (`agents/beacon/` on M1) provides Telegram-native course access with auto-enrollment, inline buttons, and access gating via invite codes. See `docs/systems/agents.md`.

## Deployment

Portal: manual only — `cd xavigate-training && npx wrangler pages deploy . --project-name=xavigate-training --branch=main`. Git push alone does NOT deploy.
