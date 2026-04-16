## 2026-04-17
- **LLM policy locked: Max plan first, API never without justification.** `marketing/scripts/xavi-post.py` and `marketing/scripts/mm-audit.py` now invoke Claude via `claude -p` subprocess (Max subscription) instead of the `anthropic` SDK. Canonical helper shape: `CLAUDE_BIN + -p + --model + --append-system-prompt + --disallowedTools "*" + --dangerously-skip-permissions + --output-format text`. Memory: `memory/feedback_default_to_claude_max.md`. Todo T-145 (P1) tracks migration of remaining 5 mm-* scripts (`mm-lp-audit`, `mm-generate`, `mm-lp-generate`, `mm-fix`, `mm-loop`).
- Consolidation Pass 3 Phase B complete. `xavi-post generate` is now end-to-end: brief → Claude generation → char-count validation → banned-phrase gate → post JSON with full provenance (`kernel_id`, `brief_id`, `generator`, `model_used`, `generated_at`, `gate_failures`). New flags `--skeleton-only`, `--model`. BRF-001 collision fixed via standalone counter at `content-system/briefs/_counter.json` (was scanning `publications.jsonl` which never stored briefs). `content_system.next_brief_id()` rewritten. Smoke test green on KRN-001 → 582-char LinkedIn post, W20 mon_linkedin slot.
- `marketing/CLAUDE.md` scripts table: `mm-audit.py` row updated — no longer needs `ANTHROPIC_API_KEY` (runs on Max plan via `claude -p`).

## 2026-04-16
- Added GOV-22 (Plain Language Standard) to governance system. New gate for voice/clarity quality on all consumer-facing content. Content production triad → quartet. Auto-load trigger wired in CLAUDE.md. Applied to 8 xavigate-site product pages. Phrase-map replacements updated.
- Doc engine built (`planning/doc-engine/`). 9 missing READMEs generated. 12 repos got .gitignore. Sensitive files untracked from planning/. 32-item accuracy roadmap.
- Backup D1 export fixed — wrangler API token lacked D1 perms, added `env -u CLOUDFLARE_API_TOKEN` fallback to OAuth.

## 2026-04-12
- Map intake: embedded 76-question Insights quiz inline in Step 2. Shop worker score sync fixed. Checkout auth fix for cookie-only sessions.

## 2026-04-11
- Career Collapse book published. Stripe checkout ($7.99). Epub via epub-builder. Direct Stripe buy buttons on 10 site pages.

## 2026-04-05
- M4 auto-sync cron (every 15 min, pulls 7 repos) + pre-commit hooks added.
- Session-close protocol created (`planning/SESSION-CLOSE-PROTOCOL.md`).

## 2026-04-04
- GOV-02, GOV-03, GOV-10 locked. Core Brief v1.1.

## 2026-04-01
- Course framework simplified: Standard ($600) + With Live Instructor ($1,500). RPT = 1 course, 7 modules. France live cohort removed.

## 2026-03-25
- **New product:** Xavigate Map Update v1 built end-to-end.
  - API: /map/update-points, /intake/map-update endpoints added to shop worker.
  - Database: map_updates + map_summary_points tables (migration 017).
  - Stripe: map-followup checkout redirects to update intake page.

## 2026-03-01
- Brand pivot: Xavigate = product brand, Renergence = framework. Full site code update.

## 2026-02-27
- Stripe wired into site. Gumroad fully removed. 20 products, 7 HTML files updated.

## 2026-02-25
- Knowledge/ repo created as canonical SSOT. 59 files consolidated from 3 locations.
- Telegram 409 fixed (webhook mode). Xavi workspace backed up.

## 2026-02-21
- renOS repo created. Compliance system consolidated. Architecture spec locked.

## 2026-02-19
- Design system created. Root visual spec with inheritance chain.

## 2026-02-18
- MailerLite CRM built. 291 contacts, 10 segments. Governed Agent Architecture.

## 2026-02-17
- Saurabh partnership agreed. Profit share + SAFE. Steven owns 100% IP.
- Xavigate App shut down. Database exported.
- Commerce = Stripe only.

## 2026-02-11
- STATUS.md created. Emergent direction named.
