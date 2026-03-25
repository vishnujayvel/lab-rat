# Lab Rat — Proposal

## Problem Statement

I bookmark 10-15 GitHub repos per week in Readwise Reader — mostly Claude Code skills,
MCP servers, AI agent tools, and reference materials. They pile up unread because
evaluating each one takes 15-30 minutes (clone, read README, install, test, decide
if it's useful for my workflow). I have ADHD, so context-switching to evaluate repos
means I rarely get back to them.

## Desired Outcome

An autonomous Claude Code skill ("Lab Rat") that acts as my personal tech scout:
1. Reads my Readwise GitHub bookmarks
2. Classifies each repo (Skill / Tool / Reference / Inspiration)
3. Runs a security audit before touching anything
4. Experiments with promising repos in a sandbox
5. Evaluates relevance to MY specific workflow (37+ skills, 15+ MCP servers)
6. Serves findings through an ADHD-friendly HTML portal with animations
7. Captures my feedback conversationally and in the portal

## Key Constraints

- **Safety first:** Two-layer security gate (GitHub secret scanning + LLM code review).
  Nothing enters sandbox without passing. Nothing touches global config without explicit promotion.
- **ADHD-friendly:** Verdict in 1 second. "For You" before technical details. Collapsed sections.
  One action button per card. No walls of text.
- **Personalized:** Consults a profile of who I am, what I've built, my tech taste, and my
  pain points. Not generic repo evaluation — personal relevance scoring.
- **Three invocation modes:**
  - `/lab explore <url or keyword>` — on-demand, right now
  - `/lab digest` — batch process all unread bookmarks
  - RemoteTrigger at 5am — overnight autonomous (phase 2)
- **Portal:** Local HTML dashboard (Tailwind CDN + Alpine.js, zero build step) at
  `~/.local/share/lab-rat/portal/index.html`. Shows all explored repos as filterable cards
  with timeline, verdict, likes/dislikes, and integration suggestions.

## Integrations

- **Readwise MCP** — source bookmarks, archive processed ones
- **GitHub MCP** — secret scanning, repo metadata
- **daily-copilot** — morning briefing mentions new findings
- **obsidian-agent** — optional vault cross-reference
- **Mem0** — remember past verdicts and preferences

## Sandbox

- Location: `~/workplace/playground/{repo-name}/`
- Promotion path: tested + approved → `~/.claude/skills/{name}/`
- Data: `~/.local/share/lab-rat/` (XDG pattern)

## User Profile

The skill must understand who I am to evaluate relevance:
- Staff-level engineer, deep in Claude Code ecosystem
- 37+ custom skills across interview prep, life management, dev tools, finance
- Loves: XDG convention, organizational frameworks, elegance, ADHD-friendly design
- Active: interview prep, job hunt, coaching, personal knowledge management
- Runner — health/fitness data is always relevant
- Aesthetic: simple > complex, one clear action > menu of options

## Rough Design Notes (from brainstorming session)

See `~/.local/share/lab-rat/design.md` for the full brainstorm output including:
- Pipeline flow, classification categories, security gate details
- Portal card data model and ADHD design principles
- File structure and integration points
- Implementation phases

## Success Criteria

1. I can say `/lab explore <url>` and get a personalized verdict in under 2 minutes
2. `/lab digest` processes all unread GitHub bookmarks and updates the portal
3. The portal loads instantly, shows verdicts at a glance, and doesn't overwhelm
4. Security audit catches obviously malicious repos before they enter sandbox
5. Relevance scoring references my actual skills and projects, not generic advice
