# The L0..L4 memory model

This is a mental model I evolved to decide *where* a fact should live, given that Claude has multiple persistence mechanisms with different costs.

```
┌──────┬─────────────────────────────────┬──────────────────────────────────────┐
│  L0  │ Model weights / training data   │ Not editable. What the model knows.  │
├──────┼─────────────────────────────────┼──────────────────────────────────────┤
│  L1  │ Context window                  │ This conversation. Volatile.         │
├──────┼─────────────────────────────────┼──────────────────────────────────────┤
│  L2  │ Auto-memory tool (file-based)   │ ~/.claude/.../memory/*.md            │
│      │ Indexed by MEMORY.md            │ Loaded on demand by relevance        │
├──────┼─────────────────────────────────┼──────────────────────────────────────┤
│  L3  │ Auto-loaded files               │ CLAUDE.md, MEMORY.md, system prompt  │
│      │                                 │ Loaded EVERY session, every turn     │
├──────┼─────────────────────────────────┼──────────────────────────────────────┤
│  L4  │ Lookup-only notes               │ memory/personal/*.md (NOT indexed)   │
│      │                                 │ Loaded only when explicitly read     │
└──────┴─────────────────────────────────┴──────────────────────────────────────┘
```

The whole game is **picking the right layer for each fact**. Get it wrong in either direction:
- **Put a personal/situational note at L3 → context tax forever, every session.**
- **Put load-bearing infra at L4 → next session has no idea it exists.**

---

## L3 — Auto-loaded (most expensive)

Loaded into every session, every turn. So every line costs context every time.

**Reserve for:**
- The project's identity (what is this, what's the rule set)
- Pointers to where deeper info lives
- Universal anti-patterns ("do not do X — see Y for why")

**Do NOT put here:**
- Anything that could go in a memory entry instead
- Current task state, recent results, in-flight work (those are `lore/` and `tasks/`)
- Component-specific facts (push to subtree CLAUDE.md, which only loads when relevant)

In Claude Code: top-level `CLAUDE.md` + per-project `MEMORY.md` index + any subtree `CLAUDE.md` that matches the working directory.

---

## L2 — Indexed memory (load-on-demand)

Files in `~/.claude/projects/<project>/memory/`. Only the `MEMORY.md` index loads every session (so it's effectively L3). The individual entries get pulled in by Claude when they look relevant.

**Reserve for:**
- **Project findings** — things confirmed across sessions that aren't derivable from code (regressions, validated approaches, anti-patterns specific to the domain)
- **User feedback** — corrections, preferences, decisions that should bind future behavior
- **External pointers** — Linear/JIRA project names, Grafana URLs, infrastructure addresses, where the secrets are kept
- **Project state changes** — deadlines, freeze windows, who owns what (decay-prone; revisit)

**Naming conventions I use:**
- `project_<topic>.md` — current state observations
- `feedback_<topic>.md` — rule the user established, with a `Why:` and `How to apply:`
- `reference_<topic>.md` — external pointer
- `user_<topic>.md` — user role / context
- `<concrete_topic>.md` — anything else, named for what it's about

Each entry has YAML frontmatter (`name`, `description`, `type`) so the model can decide relevance from the index alone before deciding whether to load the full entry.

**See `examples/memory_entry_meta.md`** — a real L2 entry (which happens to be the entry that defined this whole model).

---

## L4 — Personal / lookup-only (not indexed)

Files at `memory/personal/`, indexed by `personal/INDEX.md` (not the top-level `MEMORY.md`).

Why this exists: a lot of nice-to-remember context isn't load-bearing. The YouTube channels I watch, hobbies, small biographical context, a phrase someone used that I want to remember. None of this should auto-load. But it's still worth keeping somewhere recoverable.

**Put at L4:**
- Personal details (channels watched, hobbies, side-interests)
- "I'd like to remember X for later" requests, where X is concrete future-recall material
- External resources to pin (but not auto-load)
- Small biographical / relational context

**Do NOT save at all:**
- Transient observations and venting ("I tried X today and it was painful")
- Compliments, style preferences, conversation
- "I'm tired" / passing emotions
- Anything where the question *"would the user re-ask this later?"* is no

**The L4 bar:** a future session needs to look this up to do something useful. Not "user said this once."

---

## The recall pattern

L2 entries get auto-discovered (Claude sees the `MEMORY.md` index and decides what's relevant). L4 entries do not — they only come into context when explicitly read.

**The pattern:** when the user asks something personal-context-flavored, the model `Read`s `personal/INDEX.md` first, finds the right entry by description, then reads it. INDEX.md tells you what exists. Don't grep blindly.

---

## If unsure: L2 or L4?

Ask: *"would the user be annoyed to see this loaded at the start of every session?"*

- Yes → L4 (or don't save at all)
- They'd appreciate it being at-hand without re-asking → L2

A single topic can have **both** an L2 and an L4 entry. Example: a research paper I'm pivoting toward. The technical implementation work is L2 (will affect every session). The fact that I found the paper via a particular YouTube channel I'm attached to is L4 (recall on demand, never loaded by default).

---

## Anti-patterns I've personally fallen into

1. **Saving everything to L3** because it feels durable. → Five sessions later, top-level `CLAUDE.md` is 600 lines of stale narrative. Fix: move historical context to a `docs/` file; CLAUDE.md links to it.

2. **Saving working notes as L2 memory.** ("I tried approach X and it worked.") → Memory bloats with point-in-time observations that contradict each other over time. Fix: `lore/` for that. Memory is for *validated* findings.

3. **Treating L2 as a journal.** → Memory should be findings, not feelings. Validated rules, not exploration logs.

4. **Forgetting L4 exists.** → All "remember this for me" requests pile up in L2, and `MEMORY.md` grows past its 200-line auto-load cap. Fix: ask "is this load-bearing?" before saving.

---

## What this gives you

After a few months of discipline, opening any project gets you:
- A clean top-level `CLAUDE.md` that explains what the project is in 100 lines
- Memory entries that are genuinely useful and not noise
- A clear separation between "what is true about this project" and "what happened on Tuesday"

The model walks in oriented, you walk in oriented, and reading the structure tells someone else how to think about the work.
