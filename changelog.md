## 2026-04-17
- **Kernel backfill complete — first non-empty Publishing Engine brief.** New tool `planning/scripts/backfill-kernel-mapping.py` (claude -p Max, batched single-call) tagged 17/24 historical posts in `marketing/01-hopper/published/{2026-W09,2026-W10}/` with `kernel_id`. Distribution: KRN-003 (10), KRN-005 (3), KRN-009 (3), KRN-006 (1), unmapped (7 — Claude declined low-confidence). Engine smoke-tested end-to-end: `engine-collect --days 90` → 100 posts → `engine-learn --weeks 12` → first non-empty brief at `content-system/intelligence/brief-2026-W16.md` (top kernel KRN-003, 4 kernels ranked, patterns counted). `engine-collect.py` `--days` default widened 14 → 90 (local hopper has weeks-old posts that need a wider join window). Engine left PAUSED. Late analytics still 0 impressions (pre-existing gap, orthogonal). Handoff: `planning/handoffs/2026-04-17-11-kernel-backfill.md`.
- **Consolidation Pass 5b — Steven review applied.** 7 directory dispositions executed. Top-level `~/Projects/` 34 → 30 (combined Pass 5+5b: 41 → 30). xavigate-launch archived; jonathan + michael-rudolph moved to `~/Personal/`; ai-spend-tracker folded into `planning/scripts/` (own `.git` removed, single commit absorbed). multiple-natures DEFERRED — Steven wants separate discussion about a top-level `theory/` folder for MN Theory + Renergence Theory. Pass 6+ (radiant-brewing-dolphin sales/marketing engine) UNLOCKED. Handoff: `planning/handoffs/2026-04-17-10-planning-consolidation-pass-5b.md`.
- **Consolidation Pass 5 complete (operational) — directory reorg.** Top-level `~/Projects/` 41 → 34. Archived to `_archive/`: `openclaw-logs/` (M1 is SSOT), `licensing/` (canonical v2 in knowledge/), `security/` (audit complete), `xavi-platform/` (M1 is SSOT). Each has a `WHY-ARCHIVED.md` tombstone. `_archive/README.md` updated with full sub-archive index. Moved out of Projects/: `personal/`, `taxes/` → `~/Personal/`. Relocated `plan.md` (root) → `planning/MAP-RECOVERY-PLAN.md`. `archive/` 28GB legacy folder left in place — 5 live cross-references in active code make rename non-trivial; documented as follow-up. STATUS.md Supporting + Archived tables updated. Steven-review gate pending before Pass 6+ unlocks. Handoff: `planning/handoffs/2026-04-17-09-planning-consolidation-pass-5.md`.
- **Consolidation Pass 4 complete — Publishing Engine relocated to `content-system/`.** `marketing/scripts/engine/` → `content-system/scripts/engine/`; `marketing/intelligence/` → `content-system/intelligence/`. Cross-repo move (deletes in marketing, adds in content-system). `engine_common.py` path constants rewritten (`CONTENT_SYSTEM_DIR`/`MARKETING_DIR`/`INTELLIGENCE_DIR`). Sibling `content-system/scripts/_env.py` added (still loads `marketing/.env`). All 3 scheduled tasks (`engine-collect`, `engine-learn`, `engine-notify`) repointed. Smoke verified end-to-end: collect dry-run pulled 36 posts via Late API; learn dry-run loaded records; pause/resume cycle clean. Engine left in PAUSED state (kernel mapping still pending — known follow-up). New: `planning/scripts/ops-status.py` aggregator (engine + backups + git drift, `--json` mode). Handoff: `planning/handoffs/2026-04-17-08-planning-consolidation-pass-4.md`.
- **T-145 complete — all 6 mm-* scripts on Max plan.** `mm-lp-audit.py`, `mm-generate.py`, `mm-lp-generate.py`, `mm-fix.py` migrated to `claude -p --model opus`. `mm-loop.py` inherits Max path (orchestrator shells to audit + fix). Zero `anthropic` imports or `ANTHROPIC_API_KEY` references remain in `marketing/scripts/`. `marketing/CLAUDE.md` scripts table updated: all 6 rows show `— (Max plan only)`. The marketing pipeline is now fully off the API.
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
