# Settings, hooks, and transcript retention

The small operational pieces that make Claude Code comfortable to live with long-term.

---

## `~/.claude/settings.json`

This is the global config. Mine is intentionally minimal — I lean on the defaults for most things. See `examples/settings.json` for the actual file.

The fields I care about:

```json
{
  "permissions": {
    "allow": [ ...specific Bash patterns... ],
    "defaultMode": "default"
  },
  "effortLevel": "medium",
  "theme": "light-daltonized",
  "cleanupPeriodDays": 3650
}
```

### `permissions.allow` — fewer permission prompts

By default, Claude Code asks permission for every Bash command. I add specific frequently-used patterns to the allowlist so I'm not approving the same `ls` or `grep` every five minutes.

I add patterns *carefully*: never broad wildcards like `Bash(*)`. Each allowed pattern is a tool I've judged safe to run without confirmation in this project. The `/fewer-permission-prompts` skill helps generate this list by scanning transcripts for common reads.

### `effortLevel`

Controls how hard the model thinks before each tool call. `medium` is fine for most work; bump to `high` or `max` for deep design / planning / debugging. Bumpable per-session with `/effort`.

### `cleanupPeriodDays`

**The transcript retention knob.** Default is **30 days** — Claude Code purges session transcripts older than that. If you ever want to mine your past conversations (for personal-agent distillation, post-hoc analysis, or just nostalgia), this default loses them.

I set it to `3650` (10 years) — effectively disables purging. The transcripts live at `~/.claude/projects/<project-path>/*.jsonl` and grow slowly (mine is a few hundred MB after ~half a year of heavy use).

**Even with retention extended, don't rely on the Claude Code dir as your archive.** It's a working cache. For genuine archival, set up a daily rsync of `~/.claude/projects/` to a stable backup location. See the "Backup script" section below.

---

## Per-project settings (`.claude/settings.json`)

You can override global settings per-project by dropping a `.claude/settings.json` in the project root. Useful for:
- Project-specific permission allowlists (broader trust on a personal repo, stricter on a shared one)
- Project-specific environment variables
- Project-specific hooks

I rarely use this — global is usually fine.

---

## Hooks — automating "from now on when X..."

Hooks are shell commands the harness executes in response to events (tool calls, session start/stop, etc.). They're how you build true "every time X happens, do Y" automation.

I don't use many hooks personally. The two I'd reach for if I needed them:
- `Stop` hook → run something at end of every session (commit, push, notify)
- `PreToolUse` hook → veto specific operations on specific paths

If you want automated behaviors ("each time Claude edits a file in `secrets/`, alert me") — hooks are the mechanism, not memory or preferences. Configure via the `update-config` skill or hand-edit `settings.json`.

---

## Transcript backup script

Mine lives at `~/bin/chat-bcup.sh`. Append-only rsync (no `--delete`) so even if a tool prunes its own cache, the archive keeps history:

```bash
#!/usr/bin/env bash
set -u
DEST="$HOME/chat-bcup"
STAMP="$(date +%Y-%m-%d_%H%M%S)"
LOG="$DEST/_log.txt"
mkdir -p "$DEST"

backup() {
  local name="$1" src="$2"
  if [[ -e "$src" ]]; then
    mkdir -p "$DEST/$name"
    rsync -a --no-links "$src"/ "$DEST/$name"/ 2>>"$LOG"
    echo "[$STAMP] OK  $name <- $src" >> "$LOG"
  else
    echo "[$STAMP] SKIP $name (no $src)" >> "$LOG"
  fi
}

backup claude-code     "$HOME/.claude/projects"
backup claude-code-sessions "$HOME/.claude/sessions"
# ...add gemini-cli, kimi, etc. if you use multiple AI CLIs
```

Daily cron entry: `30 3 * * * /home/<you>/bin/chat-bcup.sh`

Why append-only: a tool might purge its cache (yours or someone else's), and you don't want that propagating to your archive. Trades off some disk for being unable to lose history.

---

## Skills (`~/.claude/skills/`)

Skills are user-defined slash commands. They live in `~/.claude/skills/<skill-name>/` with a `SKILL.md` describing what the skill does.

Notable ones from the ecosystem:
- `chandoff` — pre-compaction handoff document (saves what's in your head right before context gets summarized)
- `code-review` — multi-effort review of current diff
- `update-config` — hook + permission management
- `loop` — repeat a slash command on an interval

Build your own when you find yourself repeating an instruction. The barrier is low.

---

## Putting it together

Combined, a comfortable setup is:
- Global `settings.json` with a sensible permission allowlist + extended retention
- A daily transcript rsync to a non-tool-managed backup location
- A small set of skills for repeated operations
- Per-project memory + CLAUDE.md hierarchy (see other docs in this package)

You'll iterate. The first month is mostly noticing what you keep approving manually and adding it to the allowlist. After that the friction disappears.
