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
| `analytics_events` | Behavior tracking (pages, UTM, geo) |

## Email (AWS SES v2)

- From: `Xavigate <no-reply@multiplenatures.com>`
- Region: us-east-1, AWS Sig V4
- Types: magic links, book delivery, map status

## Admin APIs

Bearer token auth. Endpoints at shop worker: `/admin/enrollments`, `/admin/purchases`, `/admin/products`, `/admin/grant-book`, `/admin/analytics/*`, `/admin/stripe/*`.

## Deployment

- **Shop worker:** Git push auto-deploys via Cloudflare
- **Training portal:** Manual only — `cd xavigate-training && npx wrangler pages deploy . --project-name=xavigate-training --branch=main`

## Detailed Docs

Full API specs, schema diagrams, and workflows in `xavigate-training/admin/docs/` (architecture, database, auth-flow, enrollment-flow, api-training, api-shop, admin-guide, stripe-testing, deployment, troubleshooting).
