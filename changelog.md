## 2026-04-18
- **New training product: MN Practitioner Trainer.** Three-month one-on-one apprenticeship with Steven Rudolph that certifies experienced MN Practitioners (3+ yrs, 60+ clients) to deliver the all-new MN Practitioner Training. Price EUR 4,999 primary / USD $5,799 equivalent. Only 3 slots for 2026, first come first served from May 1. 12 hours direct contact (redistributed across 3 months). Payment plan: full pay or 3 installments, no surcharge. **Four new web pages** on xavigate.com: program page (EN + FR) + program agreement/T&C (EN + FR, `noindex` with discreet `Preview the agreement` link). OG social cards rendered via new `images/og/render-trainer-og.js` using canonical `og-template.html`. Schema.org Course markup. Training hub callouts linked (EN + FR). Agreement covers fees, refund policy, license scope (materials as-is, no derivatives, no commercial AI products exposing canon), NJ jurisdiction, Multiple Natures International at 1512 Palisade Ave. #12N Fort Lee NJ 07024. **Ongoing Trainer Status** model baked in: certify ≥1 cohort/calendar year = active (free canon updates + quarterly calibration with Steven + directory listings); dormant year options: €300 per major update OR €1,000/yr maintenance; 24mo inactive → short re-certification required. Course curriculum drafted as 25 lessons / 125 pulses at `training/mn-practitioner-trainer/course.md` (5 modules: Foundation + Observation, Co-Delivery, Solo + Certification, Building Your Student Base, AI + Automation — AI module governed by GOV-08 hierarchy). Earning potential stated honestly at €20-30k/yr based on Sacha precedent. Application email-based (14 structured questions to steven@xavigate.com). Handoff: `planning/handoffs/2026-04-18-01-training-mn-practitioner-trainer.md`. Deferred work (tracked in todo-tracker): T-153 Stripe payment link + 3-installment invoice system (P1), T-154 acceptance email template (P1), T-155 trainer directory pages on multiplenatures.com + xavigate.com (P2), T-156 cohort reporting form for active trainers (P2), T-157 quarterly calibration infrastructure (P2). 11 commits to xavigate-site main between `8216c5a` and `828d292`.

## 2026-04-17
- **Privacy audit follow-up on xavigate-site.** Full site sweep after the maps leak — no other real diagnostic data found. Three low-severity fixes shipped in `ba65856`: (1) "Shriya" first-name references on 3 public pages (`admin-recovery.html`, `test-checkout.html`, `fr/test-checkout.html`) replaced with "our team" / "notre équipe"; (2) stray `sample-report-fr/index.html.backup` removed; (3) gitignore allowlist widened — `maps/generated/` now covers all 19 fictional profiles, new `download/*map*.{pdf,md,html}` default-deny with sample/fictional allow, new `student-sample-report/*` protection. Gate + 12-case verification matrix all pass. Audit also confirmed clean on emails, secrets, admin hardcoded data, and robots.txt coverage.
- **Privacy gate: new default-deny allowlist added to xavigate-site/.gitignore.** After 4 real-person map files (map-steven-rudolph-*) were found publicly served on xavigate.com, added `maps/generated/*` default-deny with explicit allow-listed exceptions for the 8 fictional validation profiles from CLAUDE.md (Elena Vasquez, James Kowalski, Marcus Cole, Helen Okonkwo, Amara Osei, Anika Patel, Kenji Nakamura, Oluwaseun Adeyemi). Defense-in-depth addition to the existing `diagnostics/gates/client-privacy-gate.sh` content scan. Gitignore blocks commit; content gate blocks push. Both now required. Real maps relocated to `diagnostics/recovery/steven-personal-maps/`. Commit `xavigate-site@5b4f950`. Git history NOT rewritten (T-152 filed as P2). See handoffs/2026-04-17-23-xavigate-site-privacy-leak-closed.md.
- **Diagnostics repo: history rewrite to expunge 144MB SQLite (T-148).** GitHub's 100MB file-size limit (applies to ALL account tiers, not free-only) blocked all pushes for ~20 commits. Per Steven's call (Option 1: don't track the DB), used `git filter-repo --path program-matcher-india/db/colleges.db --invert-paths --force` to rewrite 99 commits. `.git` size 203MB → 17MB. Force-pushed (`739ea6f..20852d9`); previously-stuck Pass 7 SOURCE-OF-TRUTH commit + others all landed. `.gitignore` updated to permanently exclude `db/*.db`, `*-shm`, `*-wal`, `*.backup-*`. Working DBs restored from `/tmp` backup (gitignored). `program-matcher-india/README.md` now documents the regenerate-from-CSVs script sequence. **Cross-machine impact:** anyone with an existing diagnostics clone (e.g. Xavi on M1) must re-clone OR `git fetch && git reset --hard origin/main` to align with rewritten history.
- **Theory folder corralling (Pass 7).** New top-level `~/Projects/theory/` directory gathers foundational frameworks scattered across `multiple-natures/` and `renOS/`. Layout: `theory/{foundations,mn,renergence,asp,constraint-os,clusters,_scratch}` plus `mn/archetypes/` for future expansion. ~25 files moved; `multiple-natures/theory/canon-docs/` → `multiple-natures/canon/` (renamed because content is brand application, not theory). 5 live cross-references repointed in `renOS/checkers/{mn,alignment,structure,positioning}/SKILL.md` + `diagnostics/docs/SOURCE-OF-TRUTH.md`. Tombstone READMEs at `multiple-natures/THEORY-MOVED.md` and `renOS/THEORY-MOVED.md`. Top-level dir count 30 → 31 (intentional). Handoff: `planning/handoffs/2026-04-17-12-theory-folder-corralling.md`.
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
