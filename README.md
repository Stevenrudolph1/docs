# Docs — Internal System Documentation

**Purpose:** Single entry point for understanding, operating, and changing the entire project ecosystem. For Steven, Claude, and Xavi. Not user-facing.

**Last updated:** 2026-03-20

---

## How This Works

Every project in `~/Projects/` belongs to one of three scopes. Every significant change gets logged in the changelog. Every system has an architecture page here.

### Scopes

| Scope | What | Projects |
|-------|------|----------|
| **Platform** | The Xavigate product system — what users touch, what makes money | xavigate-site, xavigate-shop-worker, xavigate-training, xavigate-api, diagnostics, content-system, insight-engine, xavistudio, marketing |
| **Framework** | The Renergence/MN intellectual property — books, governance, theory | books, knowledge, renOS, design-system, multiple-natures |
| **Operations** | Steven's infrastructure — planning, agents, personal tools | planning, communication, renergence-intelligence, skills, personal, career-database, training |

### Navigation

| I need to... | Read |
|-------------|------|
| Understand the whole system | `docs/architecture.md` |
| See what changed recently | `docs/changelog.md` |
| Understand a specific system | `docs/systems/` (one file per major system) |
| Find where something lives | `docs/project-map.md` |
| Know what connects to what | `docs/dependencies.md` |

### Rules

1. **Every significant change gets a changelog entry.** Not in STATUS.md (that's strategic decisions). Here (that's what actually changed in the system).
2. **Architecture pages stay current.** When a system changes shape, its `docs/systems/` page gets updated in the same session.
3. **This is the map, not the territory.** Don't duplicate content from READMEs. Point to them. Document the connections between things that no single README can see.
