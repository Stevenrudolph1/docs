# Xavigate Map Flow

**What it is:** The end-to-end purchase-to-delivery flow for the Xavigate Map ($97).
**Last updated:** 2026-03-18
**Status:** Specced — not yet built. See engineering spec for implementation contracts.

---

## Reference Docs

| Doc | Purpose |
|-----|---------|
| `planning/handoffs/2026-03-18-01-xavigate-straight-to-map-engineering-spec.md` | Full engineering spec — exact contracts, D1 migrations, page requirements, email copy |
| `diagnostics/products/map/fulfillment/FULFILLMENT-V1.md` | Current manual process (active until automation ships) |
| `diagnostics/products/map/fulfillment/auto-generate.py` | Generation daemon — 4-pass Claude pipeline |
| `xavigate-shop-worker/src/index.js` | All intake and internal map API endpoints |

---

## What This Flow Does

Takes a buyer from Stripe payment through:
1. Assessment (if no scores on file)
2. Intake (situation questions)
3. Generation (automated, daemon-driven)
4. Delivery (magic link + email)
5. Dashboard (permanent access)

Handles first-time buyers, first-time buyers with prior scores, and return buyers.

---

## Systems Involved

| System | Role |
|--------|------|
| `xavigate-site` | Frontend pages: map-start, test.html, map-intake, map-delivery, dashboard |
| `xavigate-shop-worker` | All API: intake, scores, map jobs, magic links, emails |
| `diagnostics/auto-generate.py` | Generation daemon — runs on M4, polls shop worker every 60s |
| Cloudflare R2 (`xavigate-maps`) | PDF + HTML report artifact storage |
| Cloudflare D1 (`renergence-training`) | All persistent state |
| AWS SES | Email delivery |
| Stripe | Purchase trigger, session ID for buyer identity |

---

## Pages

| Page | Route | Purpose |
|------|-------|---------|
| `map-start.html` | `/map-start?session_id=...` | Post-purchase routing brain |
| `test.html` | `/test.html?source=map` | MNTEST assessment (paid flow variant — teaser, not full results) |
| `map-intake.html` | `/map-intake` | Situation intake (scores pre-filled, read-only) |
| `map-delivery.html` | `/map/:token` | Magic link destination — Map + full Xavigate Insight Assessment results |
| `dashboard.html` | `/dashboard` | All maps for this buyer |

---

## State Machine

```
purchased           ← Stripe webhook creates maps row
awaiting_assessment ← buyer has no scores; routed to assessment
awaiting_intake     ← scores exist; ready for intake
intake_in_progress  ← intake started (optional state)
submitted           ← intake submitted; daemon will pick up
generating          ← daemon running 4-pass pipeline
generated           ← artifacts in R2; magic link created; ready email sent
delivered           ← buyer opened map (optional tracking)
failed              ← generation error; no delivery email sent
```

---

## Key Endpoints (shop worker)

| Endpoint | What |
|----------|------|
| `POST /intake/scores` | Persist MNTEST scores from test.html — NEW |
| `GET /intake/profile` | Buyer profile + routing state (`next_state`) — ENHANCED |
| `POST /intake/submit` | Mark intake submitted, create map job, send confirmation email |
| `GET /internal/maps/pending` | Daemon fetches queued jobs |
| `POST /internal/maps/start` | Daemon marks job as generating |
| `POST /internal/maps/complete` | Daemon marks done; creates magic link; sends ready email — ENHANCED |
| `GET /map/:token` | Magic link verification + delivery page |
| `GET /api/dashboard` | All maps for authenticated buyer — NEW |

---

## Email Triggers

| Email | Trigger | Subject |
|-------|---------|---------|
| Submission confirmation | `POST /intake/submit` success | "Your Map is now in production" |
| Map ready | `POST /internal/maps/complete` | "Your Xavigate Map is ready" |

---

## D1 Schema Changes (Migration 002)

```sql
ALTER TABLE intake_profiles ADD COLUMN scores_saved_at TEXT;
ALTER TABLE intake_profiles ADD COLUMN scores_source TEXT DEFAULT 'manual';
ALTER TABLE magic_links ADD COLUMN map_id INTEGER REFERENCES maps(id);
ALTER TABLE magic_links ADD COLUMN purpose TEXT DEFAULT 'login';
ALTER TABLE maps ADD COLUMN confirmation_email_sent INTEGER DEFAULT 0;
ALTER TABLE maps ADD COLUMN delivery_email_sent INTEGER DEFAULT 0;
ALTER TABLE maps ADD COLUMN magic_link_token TEXT;
```

---

## Return Buyer Logic

- Detection: prior completed map exists for this email → return buyer screen
- Default path: update situation (not retake assessment)
- Intake prefill: prior `intake_situations` row loaded as editable draft
- Scores: carry forward unless buyer explicitly retakes
- Map history: new row created, prior map never overwritten
- D1 support: `is_followup`, `previous_situation_id`, `changed_fields` already in schema

---

## Language Rules (Baked In)

All copy in this flow must follow MN framework invariants:

**Never use:** dominant natures, strongest nature, top intelligence, highest nature — anything implying ranking.

**Use instead:** where your energy tends to organize, where work feels natural, where friction tends to build, how your patterning shows up, what conditions support you.

---

## Implementation Stages

| Stage | What | Status |
|-------|------|--------|
| 1 | Score persistence, post-purchase routing, onboarding screen, teaser screen, intake prefill | Not started |
| 2 | Authoritative intake submit, map job creation, confirmation email, daemon pickup | Not started |
| 3 | Completion callback, magic link, ready email, delivery page, dashboard | Not started |
| 4 | Return buyer routing, editable prefill, prior map access | Not started |
| 5 | Failure hardening, retry tooling, funnel analytics | Not started |

Update this table as stages ship.
