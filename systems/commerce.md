# Commerce

**Locations:** `~/Projects/xavigate-shop-worker/`, `~/Projects/xavigate-training/`
**Canonical admin docs:** `xavigate-training/admin/docs/` (HTML, 10 pages)
**Last updated:** 2026-03-17

---

## What It Is

Two Cloudflare Workers sharing one D1 database handle all commerce: Stripe checkout, webhook fulfillment, enrollment, book delivery, auth, and admin.

## Architecture

```
xavigate-site (product pages, checkout links)
  ↓ Stripe Payment Links
xavigate-shop-worker (CF Worker) — webhook, fulfillment, admin API
  ↓ D1 write + SES email
Customer gets: book download email OR course enrollment
  ↓ if course
xavigate-training (CF Pages portal) + training worker (auth, progress)
```

## Components

| Component | Location | Purpose |
|-----------|----------|---------|
| Shop Worker | `xavigate-shop-worker/src/index.js` | Stripe checkout, webhooks, product CRUD, admin APIs |
| Training Worker | `xavigate-training-worker/` | Auth (magic links), progress tracking, content delivery |
| Training Portal | `xavigate-training/` | Static HTML frontend (CF Pages, manual deploy) |
| D1 Database | `renergence-training` | Shared: users, enrollments, products, purchases, sessions, maps |

## Stripe Flow

1. User clicks checkout → shop worker creates Stripe session (`allow_promotion_codes: true`)
2. Stripe fires `checkout.session.completed` webhook
3. Shop worker validates signature, routes by product type:
   - **Courses:** Parse `course_ids` JSON, INSERT OR IGNORE per course (idempotent)
   - **Books:** Send SES email with download links
   - **Maps:** Create map row (status `awaiting_intake`)
4. Records purchase with `stripe_session_id`

## Auth (Magic Links)

1. User enters email → POST `/auth/request`
2. Check allowlist OR active enrollment → generate 64-char hex token (15-min TTL)
3. Send email via SES with verification link
4. Click → validate, create session (7-day TTL), set `ren_session` cookie
5. Admin emails: `steven@multiplenatures.com`, `shriya@multiplenatures.com`

## D1 Key Tables

| Table | Purpose |
|-------|---------|
| `users` | Identity (email, name, stripe_customer_id) |
| `enrollments` | Per-course access (UNIQUE email+course_id) |
| `products` | Catalog (type, price, course_ids JSON, download URLs) |
| `purchases` | Payment history (stripe session/intent IDs) |
| `sessions` / `magic_links` | Auth state |
| `maps` | Diagnostic product tracking (status lifecycle) |
| `map_generation_jobs` | Map pipeline job tracking (status, attempts, R2 refs) |
| `map_generation_steps` | Per-step tracking within a generation job (pass1–pass4, PDF, email) |
| `map_events` | Full map lifecycle event log (workflow_started, pass_completed, pdf_rendered, email_sent, errors) |
| `mn_assessments` | MN/MI score storage (assessment_uid, scores, result_snapshot) |
| `intake_profiles` | User demographic/context data for map intake |
| `intake_situations` | Per-situation intake data (supports followup/update flows) |
| `security_events` | Security audit trail (sanitizer flags, validation events, scored severity) |
| `alert_rate_limit` | Rate limiting for SMS/email security alerts (hourly buckets) |
| `analytics_events` | Behavior tracking (pages, UTM, geo) |

## Email (AWS SES v2)

- From: `Xavigate <no-reply@multiplenatures.com>`
- Region: us-east-1, AWS Sig V4
- Types: magic links, book delivery, map status

## Map Update Flow

The xavigate-api supports map updates (followup maps) via:
- `map-update-intake.html` — dedicated intake page for returning users
- `map-followup` product ID triggers followup flow at checkout
- `intake_situations` table tracks `is_followup`, `previous_situation_id`, `changed_fields`
- Followup maps reuse existing profile data, only collecting what changed

## Map Event Logging

`map-event-logger.js` provides non-blocking lifecycle event logging to the `map_events` table via `logMapEvent(db, mapId, eventType, detail)`. Events tracked: `workflow_started`, `pass_completed` (per pass), `pdf_rendered`, `status_changed`, `email_sent`, `error`. Used throughout `map-workflow.js` for full timeline visibility.

## Security Headers

`applySecurityHeaders(response)` wraps every response with:
- `Strict-Transport-Security: max-age=31536000; includeSubDomains`
- `X-Content-Type-Options: nosniff`
- `X-Frame-Options: DENY`
- `Referrer-Policy: strict-origin-when-cross-origin`
- `Permissions-Policy: camera=(), microphone=(), geolocation=()`

## GDPR Data Deletion

`DELETE /admin/user/:id/data` — cascading deletion (right to erasure). Deletes across: `map_generation_steps`, `map_generation_jobs`, `maps`, `intake_situations`, `intake_profiles`, `mn_assessments`, `sessions`, `magic_links`, `enrollments`, `lesson_progress`, `pulse_users`/`pulse_events`/`pulse_commands`, and the `users` record itself. Purchases are anonymized (email redacted, user_id nulled). Security events are anonymized (user_id nulled, IP redacted) to preserve audit trail. Logs a `gdpr_data_deletion` security event.

## Admin APIs

Bearer token auth. Endpoints at shop worker: `/admin/enrollments`, `/admin/purchases`, `/admin/products`, `/admin/grant-book`, `/admin/analytics/*`, `/admin/stripe/*`, `/admin/user/:id/data` (GDPR deletion).

## Deployment

- **Shop worker:** Git push auto-deploys via Cloudflare
- **Training portal:** Manual only — `cd xavigate-training && npx wrangler pages deploy . --project-name=xavigate-training --branch=main`

## Detailed Docs

Full API specs, schema diagrams, and workflows in `xavigate-training/admin/docs/` (architecture, database, auth-flow, enrollment-flow, api-training, api-shop, admin-guide, stripe-testing, deployment, troubleshooting).
