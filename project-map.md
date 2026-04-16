# Project Map

Every folder in `~/Projects/` and what it is.

**Last updated:** 2026-04-16

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
| `knowledge/` | Canonical SSOT for all framework knowledge (GOV-00–21, publishing, framework). | governance | yes |
| `renOS/` | Constraint Operating System. 4 compliance checkers. | governance engine | no (local) |
| `design-system/` | Root visual design spec: palette, typography, marks, covers. | design | no (local) |
| `multiple-natures/` | MN theory, MNTEST question bank, practitioner network. | reference | no (local) |

## Operations (Steven's infrastructure)

| Folder | What | Scope | Has git remote |
|--------|------|-------|---------------|
| `planning/` | Strategy hub — STATUS.md, handoffs, governance index, todos. | ops | yes |
| `communication/` | Substack repo, MailerLite, partner comms. | ops | partial (substack) |
| `renergence-intelligence/` | Governance engine — ren.py, signals, scores, profiles. | ops | no (local) |
| `skills/` | Claude skill files (SKILL.md per domain). | ops | no (local) |
| `personal/` | Photos, headshots, family docs. | personal | no |
| `training/` | RPT practitioner training: 1 course, 7 modules, 2 tiers ($600/$1,500). | course ops | no (local) |
| `docs/` | Internal system documentation hub. | meta | no (local) |
| `pulse/` | Daily pulse system. | ops | no (local) |
| `security/` | Security configuration and audit. | ops | no (local) |
| `backups/` | Backup scripts and snapshots. | ops | no (local) |
| `translation/` | French translations, i18n pipeline. | ops | no (local) |
| `openclaw/` | OpenClaw agent configuration, plugins. | ops | no (local) |
| `openclaw-logs/` | OpenClaw session logs. | ops | no (local) |
| `partners/` | Partner communications and agreements. | ops | no (local) |
| `ai-spend-tracker/` | AI API spend monitoring. | ops | no (local) |
| `licensing/` | IP licensing and agreements. | ops | no (local) |
| `sr-governance-mcp/` | Governance MCP server. | ops | no (local) |
| `xavigate-launch/` | Launch roadmap and checklists. | ops | no (local) |
| `xavigate-training-worker/` | Training portal Cloudflare Worker backend. | backend | no (local) |
| `voice-feed/` | Voice content pipeline. | content producer | no (local) |
| `xavi-shorts/` | Short-form video content. | content producer | no (local) |
| `xavi-platform/` | Renergence Chat bot, daily DM, Xavi products. | platform | no (local) |
| `references/` | Reference materials. | reference | no (local) |
| `jonathan/` | Jonathan project files. | personal | no |
| `michael-rudolph/` | Michael Rudolph project files. | personal | no |

## Archive

| Folder | What |
|--------|------|
| `archive/` | Deprecated projects (old Xavigate LMS, maps pre-consolidation, mntest pre-consolidation, old website, documentation, etc.) |

## On M1 (remote)

| Folder | What |
|--------|------|
| `~/.openclaw/workspace/` | Xavi agent — system prompts, knowledge, tools |
| `Projects/xavi-platform/` | Renergence Chat bot, daily DM, course tools |
