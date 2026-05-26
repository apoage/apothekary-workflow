# Example: settings.json

_My actual `~/.claude/settings.json` (no sensitive values — Claude Code itself stores no secrets here). Demonstrates: minimal config, a specific Bash allowlist, extended `cleanupPeriodDays` for long-term transcript retention._

---

```json

{
  "permissions": {
    "allow": [
      "Bash(ls:*)",
      "WebSearch",
      "Bash(timeout 120 python train_jax_fast.py:*)",
      "Bash(python -m py_compile:*)",
      "Bash(python -c:*)",
      "Bash(cat:*)",
      "Bash(fi)",
      "Bash(done)",
      "Bash(grep:*)",
      "Bash(source:*)",
      "Bash(timeout 90 python:*)"
    ],
    "defaultMode": "default"
  },
  "effortLevel": "medium",
  "theme": "light-daltonized",
  "cleanupPeriodDays": 3650
}

```
