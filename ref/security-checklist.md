# Lab Rat Security Checklist

Used by the LLM code review subagent during Layer 2 of the security audit.

## Critical (BLOCK if found)
- [ ] Hardcoded API keys, tokens, passwords, or credentials
- [ ] Suspicious postinstall/setup.py/setup.cfg scripts that execute arbitrary code
- [ ] Network calls to unexpected domains (data exfiltration patterns)
- [ ] Filesystem writes outside the project directory (especially to ~/.ssh, ~/.config, ~/.claude)
- [ ] Shell injection vectors (unsanitized user input in exec/spawn/system calls)
- [ ] Obfuscated code (base64 encoded strings executed, eval() on dynamic strings)
- [ ] Dependency typosquatting (packages with names similar to popular ones but slightly off)
- [ ] Crypto mining code
- [ ] Attempts to modify system PATH or shell profiles

## Warning (CAUTION if found)
- [ ] Overly broad filesystem permissions requested
- [ ] Dependencies without lockfile (package-lock.json, poetry.lock, etc.)
- [ ] Large number of dependencies relative to project scope
- [ ] Network calls that could be legitimate but warrant review
- [ ] Use of eval() or dynamic code execution (may be legitimate)
- [ ] Missing .gitignore (secrets could be accidentally committed)

## Informational (Note but proceed)
- [ ] No test suite
- [ ] No CI/CD configuration
- [ ] Outdated dependencies (check for known CVEs if possible)
- [ ] Missing LICENSE file
- [ ] README doesn't explain what the project does
