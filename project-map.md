# Project Map

Every folder in `~/Projects/` and what it is.

**Last updated:** 2026-03-17

---

## Platform (Xavigate product system)

| Folder | What | Scope | Has git remote |
|--------|------|-------|---------------|
| `xavigate-site/` | THE website — xavigate.com. Cloudflare Pages, auto-deploy on push. Includes `/landing/` for paid-traffic LPs. | user-facing | yes |
| `xavigate-shop-worker/` | Stripe checkout + webhooks + book delivery. Cloudflare Worker + D1. | backend | yes |
| `xavigate-training/` | Training portal. Cloudflare Pages (manual deploy via wrangler). | user-facing | yes |
| `xavigate-api/` | API worker — MNTEST scoring, map intake, auth. | backend | yes |
| `diagnostics/` | Unified diagnostic system: Xavigate Insight Assessment (free) → Xavigate Map ($97). Entry Map deprecated 2026-03-18. | engine | no (local) |
| `content-system/` | Content production architecture — schemas, gates, builders, registry. | engine | no (local) |
| `insight-engine/` | Content supply chain hub — observations → insights → kernels. | engine | no (local) |
| `xavistudio/` | Video production (Remotion). Insight-driven pipeline. | content producer | no (local) |
| `marketing/` | Marketing machine + social media (LP generator/auditor, pain kernels, registries, xavi-post, analytics). | content producer | yes |

## Framework (Renergence/MN IP)

| Folder | What | Scope | Has git remote |
|--------|------|-------|---------------|
| `books/` | 18 manuscripts across 4 themes + book generator pipeline. | IP | yes |
| `knowledge/` | Canonical SSOT for all framework knowledge (GOV-00–17, publishing, framework). | governance | yes |
| `renOS/` | Constraint Operating System. 4 compliance checkers. | governance engine | no (local) |
| `design-system/` | Root visual design spec: palette, typography, marks, covers. | design | no (local) |
| `multiple-natures/` | MN theory, MNTEST question bank, practitioner network. | reference | no (local) |
| `training/` | RPT practitioner training spec, course content. | course content | no (local) |

## Operations (Steven's infrastructure)

| Folder | What | Scope | Has git remote |
|--------|------|-------|---------------|
| `planning/` | Strategy hub — STATUS.md, handoffs, governance index, todos. | ops | yes |
| `communication/` | Substack repo, MailerLite, partner comms. | ops | partial (substack) |
| `renergence-intelligence/` | Governance engine — ren.py, signals, scores, profiles. | ops | no (local) |
| `skills/` | Claude skill files (SKILL.md per domain). | ops | no (local) |
| `personal/` | Photos, headshots, family docs. | personal | no |
| `career-database/` | MN/MI-tagged job scoring database. | data | no (local) |
| `docs/` | Internal system documentation hub. | meta | no (local) |

## Archive

| Folder | What |
|--------|------|
| `archive/` | Deprecated projects (old Xavigate LMS, maps pre-consolidation, mntest pre-consolidation, old website, documentation, etc.) |

## On M1 (remote)

| Folder | What |
|--------|------|
| `~/.openclaw/workspace/` | Xavi agent — system prompts, knowledge, tools |
| `Projects/xavi-platform/` | Renergence Chat bot, daily DM, course tools |
