# Lab Rat — Claude Code Skill Project

## What This Is
A Claude Code skill that acts as a personal tech scout — triages Readwise GitHub bookmarks,
experiments with them in a sandbox, and serves findings through an ADHD-friendly HTML portal.

## Project Structure
```
~/.claude/skills/lab-rat/          # Skill files (SKILL.md + ref/)
~/.local/share/lab-rat/            # Data (XDG pattern)
  reports.json                     # Exploration data
  portal/index.html                # HTML dashboard
~/workplace/playground/            # Cloned repos sandbox
~/workplace/lab-rat/               # This repo (source + openspec)
```

## Tech Stack
- **Skill:** Claude Code Agent Skill (SKILL.md + ref/ modules)
- **Portal:** Single HTML file — Tailwind CDN + Alpine.js (zero build step)
- **Data:** JSON file at ~/.local/share/lab-rat/reports.json
- **Security:** GitHub MCP secret scanning + LLM code review subagent

## Key Conventions
- Follow XDG Base Directory Spec for all data paths
- ADHD-friendly design: verdict first, details collapsed, one action per card
- Portal must work with just `open index.html` — no build, no server
- Security gate MUST pass before any code enters sandbox
- Nothing touches ~/.claude/skills/ without explicit user promotion

## MCP Dependencies
- readwise (topic_search, list_documents, update_document)
- github (run_secret_scanning, get_file_contents, search_repositories)

## Testing
- Test the skill by running `/lab explore` on known repos
- Test the portal by opening index.html in browser
- Test security gate with a repo containing a known leaked key pattern
