# Lab Rat — Design Document

**Date:** 2026-03-25
**Status:** Approved — ready for implementation

## Identity

A personal tech scout that triages your Readwise GitHub bookmarks, experiments with
them in a sandbox, and serves findings through an ADHD-friendly HTML portal.
Evaluates everything through the lens of "how does this fit Vishnu's workflow?"

## Pipeline

```
Readwise Bookmarks
  → Classify (Skill / Tool / Reference / Inspiration)
  → Security Audit (GitHub secret scan + LLM code review)
  → Sandbox Test (~/workplace/playground/)
  → Personalized Relevance Scoring (against vishnu-profile.json)
  → Report to Portal + Conversational Feedback
  → Promote / Archive / Reference
```

## Classification Categories

| Category    | Criteria                              | Action                                      |
|-------------|---------------------------------------|---------------------------------------------|
| Skill       | Installable Claude Code skill         | Clone → audit → sandbox test → promote      |
| Tool        | MCP server, CLI, or dev utility       | Clone → audit → test → add to toolchain     |
| Reference   | Book, awesome-list, learning resource | Save summary to portal → tag for later      |
| Inspiration | Cool idea, not directly installable   | Extract patterns → note in portal           |

## Security Gate (Two-Layer)

### Layer 1: GitHub Secret Scanning
- Use `mcp__github__run_secret_scanning` on repo file contents
- Catches: leaked API keys, tokens, credentials, passwords

### Layer 2: LLM Code Review
- Spawn subagent to read source code looking for:
  - Malicious postinstall/setup scripts
  - Unexpected network calls (data exfiltration)
  - Filesystem writes outside expected paths
  - Dependency supply chain risks (typosquatting)
  - Shell injection vectors

### Verdicts
- **SAFE** — No findings, proceed to sandbox
- **CAUTION** — Minor findings, attach note for user review
- **BLOCK** — Critical findings, never enters sandbox

## Sandbox

- **Location:** `~/workplace/playground/{repo-name}/`
- **Isolation:** Nothing touches `~/.claude/skills/` or global config until explicit promotion
- **Promotion path:** Skill tested + approved → symlink or copy to `~/.claude/skills/`
- **Cleanup:** Archived repos can be deleted from playground on demand

## Invocation Modes

### On-Demand: `/lab explore <target>`
```
/lab explore https://github.com/mvanhorn/last30days-skill
/lab explore that journaling repo I bookmarked
/lab explore <Readwise document title or keyword>
```
- Runs full pipeline immediately
- Reports inline + updates portal
- Captures conversational feedback (liked/disliked)

### Batch: `/lab digest`
- Fetches all unprocessed Readwise GitHub bookmarks
- Classifies and triages each one
- Full pipeline for Skills/Tools, summary-only for Reference/Inspiration
- Updates portal with all findings

### Scheduled: RemoteTrigger (Phase 2)
- 5am daily trigger
- Runs `/lab digest` autonomously
- Results waiting in portal when user wakes up
- Mention in daily-copilot morning briefing

## Portal

### Tech Stack
- **HTML + Tailwind CDN + Alpine.js** — zero build step
- **Location:** `~/.local/share/lab-rat/portal/index.html`
- **Data:** `~/.local/share/lab-rat/reports.json`
- **Open:** `open ~/.local/share/lab-rat/portal/index.html`

### ADHD-Friendly Design Principles
1. **Verdict in 1 second** — Color-coded badge (green INSTALL / yellow LATER / red SKIP / blue REFERENCE)
2. **"For You" front and center** — Personal relevance before technical description
3. **Collapsed details** — Security audit, test output, integration plan all expandable
4. **Single action button** — Not a menu, one clear next step
5. **Timeline visualization** — When saved, scanned, tested, reviewed
6. **Minimal text** — Bullet points, not paragraphs

### Card Data Model
```json
{
  "id": "unique-id",
  "repo": "owner/name",
  "source_url": "https://github.com/...",
  "readwise_id": "01kmj...",
  "saved_at": "2026-03-25T15:27:07Z",
  "classification": "skill|tool|reference|inspiration",
  "security": {
    "verdict": "safe|caution|block",
    "findings": [],
    "scanned_at": "2026-03-25T..."
  },
  "sandbox": {
    "cloned": true,
    "deps_installed": true,
    "tests_ran": true,
    "tests_passed": true,
    "demo_output": "...",
    "tested_at": "2026-03-25T..."
  },
  "relevance": {
    "score": 0.85,
    "for_you": "Complements your deep-research skill...",
    "overlaps_with": ["deep-research", "interview-coach"],
    "extends": ["daily-copilot"],
    "new_capability": "Real-time trend analysis from social media"
  },
  "verdict": "install|skip|later|reference",
  "liked": ["clean API", "solves X problem"],
  "disliked": ["heavy dependencies"],
  "status": "new|auditing|testing|reviewed|installed|archived",
  "timeline": [
    {"event": "bookmarked", "at": "2026-03-25T15:27:07Z"},
    {"event": "classified", "at": "..."},
    {"event": "security_passed", "at": "..."},
    {"event": "sandbox_tested", "at": "..."},
    {"event": "reviewed", "at": "..."}
  ],
  "notes": ""
}
```

## Personalized Relevance Scoring

The skill consults `ref/vishnu-profile.json` which contains:
- Active projects and their domains
- Full skill ecosystem map (37+ skills)
- Tech preferences (XDG, elegance, ADHD-friendly)
- Pain points and repeated problems
- Interests (running, life metrics, AI agents, etc.)

### Scoring Dimensions
1. **Overlap** — Does this duplicate something already built? (negative signal)
2. **Extension** — Does this add capability to an existing skill? (strong positive)
3. **Gap Fill** — Does this solve a known pain point? (strong positive)
4. **Aesthetic Fit** — Does it match Vishnu's tech taste? (moderate signal)
5. **Learning Value** — Even if not installable, is there a pattern worth extracting? (reference signal)

## Interaction Model

### During Exploration (Conversational)
```
Agent: "I explored last30days-skill. It researches Reddit + X trends.
        FOR YOU: Pair it with deep-research for pre-coaching session prep.
        Verdict: INSTALL — clean code, no security issues, solves a real gap."
User:  "love it, the Reddit integration is exactly what I needed"
Agent: [records liked: "Reddit integration", updates portal]
```

### Post-Exploration (Portal)
- Open portal to see all explored repos
- Filter by verdict, category, date
- Click into any card for full details
- Add notes, change verdict, trigger install

## File Structure

```
~/.claude/skills/lab-rat/
  SKILL.md                    # Command router + skill definition
  ref/
    vishnu-profile.json       # User persona for relevance scoring
    security-checklist.md     # What the LLM reviewer looks for
    portal-template.md        # How to update the portal

~/.local/share/lab-rat/
  reports.json                # All exploration data
  portal/
    index.html                # The dashboard (Tailwind + Alpine.js)
  playground/ → ~/workplace/playground/  # Symlink

~/workplace/playground/
  last30days-skill/           # Cloned repos live here
  context-mode/
  ...
```

## Integrations

| System          | How Lab Rat uses it                                    |
|-----------------|--------------------------------------------------------|
| Readwise MCP    | Source bookmarks, archive processed ones               |
| GitHub MCP      | Secret scanning, repo metadata, starring good finds    |
| daily-copilot   | Morning briefing mentions new findings                 |
| obsidian-agent  | Optional cross-reference to vault notes                |
| Mem0            | Remember user preferences and past verdicts            |

## Implementation Phases

### Phase 1: Core Pipeline (build now)
- [ ] Create SKILL.md with /lab explore and /lab digest commands
- [ ] Build vishnu-profile.json from memory + skill ecosystem
- [ ] Implement classify → audit → sandbox → report pipeline
- [ ] Build portal HTML (Tailwind + Alpine.js)
- [ ] Wire up reports.json read/write
- [ ] Test with on-demand exploration of 2-3 bookmarked repos

### Phase 2: Polish + Scheduling
- [ ] Add RemoteTrigger for 5am daily runs
- [ ] Integrate with daily-copilot morning briefing
- [ ] Add "promote to global skill" workflow
- [ ] Portal: add filtering, search, bulk actions

### Phase 3: Intelligence
- [ ] Learn from past verdicts to improve classification
- [ ] Auto-suggest Readwise bookmarks to explore based on recent work
- [ ] Cross-reference with GitHub trending for proactive discovery

## Follow-Up: Global Secret Scanning Hook
- Separate from Lab Rat but identified during design
- PostToolUse hook on Write|Edit that scans changed files
- Catches accidentally committed secrets across ALL projects
