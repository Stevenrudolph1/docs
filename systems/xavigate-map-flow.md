# Xavigate Map Flow

**What it is:** The end-to-end purchase-to-delivery flow for the Xavigate Map ($97).
**Last updated:** 2026-04-16
**Status:** Backend pipeline fully built and deployed. Frontend partially built (intake exists, routing and delivery pages not yet built). See details below.

---

## Reference Docs

| Doc | Purpose |
|-----|---------|
| `planning/handoffs/2026-03-18-01-xavigate-straight-to-map-engineering-spec.md` | Full engineering spec — exact contracts, D1 migrations, page requirements, email copy |
| `diagnostics/products/map/fulfillment/FULFILLMENT-V1.md` | Current manual process (active until automation ships) |
| `diagnostics/products/map/fulfillment/auto-generate.py` | Generation daemon — 4-pass Claude pipeline |
| `xavigate-api/src/index.js` | All intake, maps, and internal API endpoints |
| `xavigate-api/src/map-workflow.js` | 11-step Cloudflare Workflow pipeline (intake → 4 LLM passes → PDF → email) |
| `xavigate-api/src/map-engine.js` | Deterministic map.json builder + structural model |
| `xavigate-api/src/map-report-template.js` | HTML report renderer (LOCKED — see CLAUDE.md) |

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
| `xavigate-api` (was xavigate-shop-worker) | All API: intake, scores, map jobs, magic links, emails. Deployed at `api.xavigate.com`. |
| `xavigate-api/src/map-workflow.js` | `MapGenerationWorkflow` — 11-step Cloudflare Workflow pipeline (built and deployed) |
| Cloudflare R2 (`xavigate-maps`) | PDF + HTML report artifact storage |
| Cloudflare D1 (`renergence-training`) | All persistent state |
| AWS SES | Email delivery |
| Stripe | Purchase trigger, session ID for buyer identity |

---

## Pages

| Page | Route | Purpose | Status |
|------|-------|---------|--------|
| `map-start.html` | `/map-start?session_id=...` | Post-purchase routing brain | **Not built** |
| `test.html` | `/test.html?source=map` | MNTEST assessment (paid flow variant — teaser, not full results) | Built |
| `map-intake.html` | `/map-intake` | Situation intake (scores pre-filled, read-only) | Built |
| `map-delivery.html` | `/map/:token` | Magic link destination — Map + full Xavigate Insight Assessment results | **Not built** |
| `dashboard.html` | `/dashboard` | All maps for this buyer | Built |

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

## Backend Pipeline (Built)

The `xavigate-api` map workflow (`src/map-workflow.js`) implements an 11-step Cloudflare Workflow pipeline:

1. `loadInputs` — load intake data from D1
2. `buildIntakeJson` — assemble intake JSON
3. `pass1NormalizeIntake` — LLM pass: normalize intake into structured markdown
4. `pass2DemandAnalysis` — LLM pass: demand analysis
5. `buildMapJson` — deterministic map.json construction (via `map-engine.js`)
6. `computeStructuralModel` — structural model computation
7. `pass3Narrative` — LLM pass: map narrative generation
8. `pass4Audit` — LLM pass: audit and refinement
9. `renderHTML` — render web report HTML (via `map-report-template.js`)
10. `renderPDF` — generate PDF artifact
11. `finalizeArtifacts` — store in R2, create magic link
12. `deliver` — send delivery email via SES

Supporting infrastructure: input sanitization, output validation, security scoring, alert dispatch, event logging. Student map variant also supported.

## Key Endpoints (xavigate-api)

| Endpoint | What | Status |
|----------|------|--------|
| `GET /intake/profile` | Buyer profile + routing state | Built |
| `POST /intake/profile` | Create/update intake profile | Built |
| `GET /intake/situations` | List intake situations | Built |
| `GET /intake/situation` | Get single intake situation | Built |
| `POST /intake/situation` | Submit intake situation data | Built |
| `POST /intake/submit` | Mark intake submitted, create map job | Built |
| `GET /maps/mine` | All maps for authenticated buyer | Built |
| `GET /maps/r2/*` | Fetch map artifacts from R2 | Built |
| `GET /maps/download` | Download map PDF | Built |
| `GET /maps/report` | View map web report | Built |
| `GET /maps/view` | View map details | Built |
| `GET /internal/maps/pending` | Internal: fetch queued jobs | Built |
| `POST /internal/maps/start` | Internal: mark job as generating | Built |
| `POST /admin/maps/*` | Admin: enqueue, approve, deliver, rerender, upload, etc. | Built |

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
| 1 | Score persistence, post-purchase routing, onboarding screen, teaser screen, intake prefill | **Backend: built.** Frontend routing page (`map-start.html`) not built. |
| 2 | Authoritative intake submit, map job creation, confirmation email, daemon pickup | **Built.** Intake endpoints, map job queue, and Cloudflare Workflow all deployed. |
| 3 | Completion callback, magic link, ready email, delivery page, dashboard | **Backend: built.** Delivery page (`map-delivery.html`) not built. Dashboard page exists. |
| 4 | Return buyer routing, editable prefill, prior map access | Backend support exists (D1 schema has `is_followup`, `previous_situation_id`). Frontend not built. |
| 5 | Failure hardening, retry tooling, funnel analytics | Partial — workflow steps are idempotent and retryable. Security scoring and alert dispatch built. Funnel analytics not started. |

Update this table as stages ship.
