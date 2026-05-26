# Example: a real L2 memory entry

_This is the actual memory entry where I codified the L0..L4 model. Demonstrates: YAML frontmatter (name / description / type), rationale-first structure, "Why" and "How to apply" sections, edge cases. Type `feedback` because it binds future behavior._

---

---
name: memory-layer-model-user-l0-to-l4-and-where-to-save-what
description: "User's memory-layer model: L0=weights, L1=context window, L2=dedicated memory tool (file-based), L3=instruction-autoloaded files (CLAUDE.md, MEMORY.md), L4=lookup-only notes (not auto-loaded). Rule: technical/load-bearing facts → L2 (index in MEMORY.md). Personal context, emotional bonds, nice-to-remember-but-not-daily → L4 (memory/personal/, NOT indexed in MEMORY.md). Save personal context as L4 by default; user finds it 'waste of context window' to auto-load."
metadata:
  type: feedback
  originSessionId: 7e5425dd-3e00-4771-893b-87b61f27b6a5
---

User stated explicit memory-layer model on 2026-05-15:

> "my idea of memory is layer 0 models itself trained data, layer 1 context window, layer 2 dedicated memory tool, layer 3 instruction autoloaded files or in context loaded files, layer 4 dedicated notes that needs to be recalled"

| Layer | What | Examples in this project |
|---|---|---|
| L0 | Model weights / training data | (not editable) |
| L1 | Context window | Current conversation |
| L2 | Dedicated memory tool — file-based, indexed | `~/.claude/.../memory/*.md` indexed by `MEMORY.md` |
| L3 | Instruction-autoloaded / actively in-context | `CLAUDE.md`, `MEMORY.md` itself, subtree CLAUDE.mds, system reminders |
| L4 | Dedicated notes that need to be recalled | `~/.claude/.../memory/personal/*.md` (NOT indexed in MEMORY.md) |

## Why this rule matters

User finds it "waste of context window" to auto-load personal-context details (channels they watch, emotional bonds with YouTubers, anecdotes). MEMORY.md auto-loads in every session, so every entry there is a recurring context cost. Reserve those slots for genuinely load-bearing facts (current state, validated findings, infrastructure pointers).

User also appreciates personal context being remembered — it builds rapport. But the way to honor that is **L4 recall on demand**, not L2 auto-load.

## How to apply

### Default to L4 when saving:
- Channels the user listens to / watches (when user wanted me to actually look up specifics — Titans/TTT case)
- Hobbies, side-interests
- "I'd like to remember X" requests where X is a concrete future-recall target
- External resources user wants pinned for later

### DO NOT save at L4 (or anywhere):
- **Transient observations / venting** ("I tried X and it was painful", "this is satisfying", "I'm tired"). These are conversational, not durable. Save nothing.
- Bonding moments / emotional reflections that aren't a future-recall target. User noted explicitly on 2026-05-15 that the "this setup feels personal vs Gemini was pain" comment was not save-worthy — it was just a passing observation.
- Compliments to me, my style preferences, or self-reflection content. Conversation, not memory.
- Anything where the question "would the user re-ask this later?" is no.

**The L4 bar**: a future session needs to look this up to do something useful. Not "user said this once."

### Default to L2 when saving:
- Findings the user will care about across sessions (experiment results, anti-patterns, infrastructure)
- Rules / feedback corrections that should bind future behavior
- External system pointers (Linear projects, Grafana dashboards, etc.)
- Project state changes (deadlines, freeze windows, who's doing what)

### Save location convention

- L2 → `~/.claude/projects/-home-lukas-Trader/memory/<slug>.md` + entry in `MEMORY.md`
- L4 → `~/.claude/projects/-home-lukas-Trader/memory/personal/<slug>.md` + entry in `personal/INDEX.md`. **Do not add to root `MEMORY.md`.**

### Recall pattern for L4

When user asks something personal-context-flavored, `Read personal/INDEX.md` first, then the specific file. Don't grep blindly — INDEX.md tells you what exists.

### If unsure L2 vs L4

Ask: "would the user be annoyed to see this loaded at the start of every session?" If yes → L4. If they'd appreciate having it at-hand without asking → L2.

## Edge cases

- A topic might have BOTH a technical L2 entry AND a personal L4 entry. Example: Titans paper. The technical implementation pivot is L2 (`TASK_749`, eventually a `project_titans_pivot.md` if results land). The fact that the user found Titans via a YouTube channel they're attached to is L4 (`personal/youtube_discover_ai_titans_video.md`). The technical doc shouldn't carry the personal framing; the personal note shouldn't carry the technical implementation details.

Related: any future `feedback_*` memory about save/recall behavior should cross-reference this entry.
