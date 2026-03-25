# Lab Rat Security Checklist

Used by the LLM code review subagent during Layer 2 of the security audit.
**Only security concerns affect the verdict.** Quality observations are informational only.

## Critical (BLOCK if found)
- [ ] Hardcoded API keys, tokens, passwords, or credentials in source code
- [ ] Malicious postinstall/setup scripts that execute arbitrary code
- [ ] Network calls to undocumented/unexpected domains (data exfiltration)
- [ ] Filesystem writes to sensitive paths (~/.ssh, ~/.config, ~/.claude, system dirs)
- [ ] Shell injection vectors (unsanitized external input in exec/spawn/system calls)
- [ ] Obfuscated code (base64 execution, eval() on untrusted/external input)
- [ ] Dependency typosquatting (names similar to popular packages but slightly off)
- [ ] Crypto mining code or unauthorized resource consumption
- [ ] Attempts to modify system PATH, shell profiles, or global config

## Warning (CAUTION if found)
These are genuine security risks that require user judgment before proceeding.
- [ ] eval()/exec() on user-supplied or externally-fetched input (not test frameworks or build tools)
- [ ] Network calls to domains not documented in README (could be legitimate — flag for review)
- [ ] Filesystem writes outside project dir to non-standard locations (not XDG data dirs)
- [ ] Native binaries or compiled artifacts without source (cannot audit)
- [ ] Broad permissions requested without justification (e.g., full disk access)
- [ ] Dependencies with known CVEs (check if actively exploited)

## Informational (note on report card, does NOT affect security verdict)
These are quality signals. They surface as metadata, never as security findings.
- [ ] No lockfile (package-lock.json, poetry.lock, etc.)
- [ ] Large dependency count relative to project scope
- [ ] Missing .gitignore
- [ ] No test suite
- [ ] No CI/CD configuration
- [ ] Outdated dependencies (no known CVEs)
- [ ] Missing LICENSE file
- [ ] README doesn't explain what the project does
- [ ] Deployment scripts that write to expected locations (e.g., ~/.claude/skills/)
- [ ] Vendored dependencies (flag but not a security concern unless obfuscated)
