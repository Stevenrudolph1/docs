# Agents

**Locations:** M1 `~/.openclaw/workspace/agents/`, M4 `~/Projects/planning/SSOT-ARCHITECTURE.md`
**Last updated:** 2026-03-17

---

## What It Is

Dual-machine agent infrastructure. Xavi (executive AI) and Beacon (course instructor) run on M1 via OpenClaw, while M4 is the canonical source of truth. ACP (Agent Control Protocol) enables structured task dispatch from Xavi to Claude Code.

## Agents

| Agent | Location | Model | Purpose |
|-------|----------|-------|---------|
| **Xavi** | M1 `agents/xavi/` | GPT-5.4 | Executive partner: 3 modes (CFO/Insight/Governance), ACP dispatch, debugging, decision logging |
| **Beacon** | M1 `agents/beacon/` | GPT-5.4 | Course instructor: Telegram bot, auto-enrollment, invite codes, lesson delivery |
| **Claude Code** | M4 | Claude Opus 4.6 | Implementation: receives ACP tasks, builds/fixes/deploys |

## Xavi Capabilities

- **Three Operating Modes:** CFO (revenue), Insight (pattern recognition), Governance (doctrine)
- **ACP Dispatch:** Structures tasks for Claude Code with envelope (TASK_ID, project, expected duration)
- **Debugging Operator:** Monitors Telegram debugging group, SSHes to M4, frames fixes, dispatches
- **Email:** Hardcoded allowlist via `tools/send-mail`
- **Image Generation:** DALL-E (20/day)
- **Insight Logging:** Captures scenes/patterns for Insight Engine

## Beacon Capabilities

- **Auto-Enrollment:** Single course + NOT_ENROLLED = immediate enroll (no asking)
- **Access Gating:** Invite codes via `access.py` (CODE_REQUIRED flow)
- **Course Tools:** `course_status`, `course_list`, `course_start`, `course_lesson`, `course_submit`, `course_certificate`
- **Inline Buttons:** Mandatory for all course messages

## ACP Infrastructure

- Backend: acpx 0.3.0 + OpenClaw 2026.3.13
- Config: `~/.openclaw/openclaw.json` — `permissionMode: "approve-all"`
- `--approve-all` flag goes BEFORE agent name: `acpx --approve-all claude exec "..."`
- ANTHROPIC_API_KEY in M1 `~/.zshrc`

## M1 ↔ M4 (SSOT Architecture)

| Machine | Role | IP |
|---------|------|-----|
| M4 (MacBook) | SSOT — all repos, governance, todos, planning | 192.168.1.12 |
| M1 (Mac Mini) | Runtime — agents, bots, cron, email | 192.168.1.43 |

- SSH both ways: `ssh m1` / `ssh m4`
- M4→M1 sync: rsync governance every 6 hours (read-only on M1, chmod 444)
- M1 writes go through SSH: `ssh m4 'command'`
- Sync script: `planning/scripts/push-governance-m4-to-m1.sh`
- Healthcheck: `planning/scripts/ssot-healthcheck.sh`

## Key System Prompts (M1)

| File | What |
|------|------|
| `agents/xavi/system/XAVI_SYSTEM_PROMPT_v1.md` | Full Xavi behavior |
| `agents/xavi/system/ACP_DISPATCH_PROTOCOL_v1.md` | Task envelope structure, Claude obligations |
| `agents/xavi/system/DEBUGGING_GROUP_PROTOCOL_v1.md` | Bug investigation workflow |
| `agents/beacon/system/BEACON_SYSTEM_PROMPT_v1.md` | Course behavior, auto-enrollment, access gating |

## Telegram

| Bot | Purpose | Chat IDs |
|-----|---------|----------|
| Xavi | Executive partner | Steven: `tg:1192545368`, Shriya: `tg:6951374540` |
| Beacon | Course instructor | Students via invite codes |
| Debugging group | Bug reports | `-5232643789` |

## Tools on M1

`send-mail`, `generate-image`, `generate-pdf`, `knowledge-search`, `access`, `todo-tracker` (SSH wrapper), `course-engine`, `insight-tool`, `m4-exec`, `restart-gateway`, `m1-infra-audit.sh`
