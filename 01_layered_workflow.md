# Layered workflow

The four places I put information so Claude stays oriented across sessions, without me re-explaining the project every time.

```
┌─────────────────────────────────────────────────────────────────────────┐
│  LAYER                       WHERE                              ROT      │
├─────────────────────────────────────────────────────────────────────────┤
│  1. Cross-cutting rules      ~/<project>/CLAUDE.md              slow    │
│  2. Component facts          ~/<project>/<subdir>/CLAUDE.md     slow    │
│  3. Persistent memory        ~/.claude/projects/<proj>/memory/  medium  │
│  4. Session state            ~/<project>/lore/ + tasks/         daily   │
└─────────────────────────────────────────────────────────────────────────┘
```

Each layer has a different lifespan. Putting fast-rotting info into a slow-rotting layer is how projects get unmaintainable. Putting slow-rotting facts into fast-rotting notes means you keep re-discovering the same architecture.

---

## Layer 1 — Top-level `CLAUDE.md`

Every Claude Code session starts by loading the top-level `CLAUDE.md` in the working directory. So this file is the **load-bearing system prompt** for the project.

**What goes here (cross-cutting rules only):**
- What this project is, in two sentences
- Top-level layout: what each major dir/file is for
- Universal rules: things that apply to all code in the repo (style, anti-patterns, library gotchas)
- Pointers to deeper docs (lore/, tasks/) and to subtree CLAUDE.md files

**What does NOT go here:**
- Anything specific to one component (push it to the subtree CLAUDE.md)
- Anything that changes weekly (push it to lore/ or tasks/)
- Long technical explanations (link to a doc; don't inline)

**Length target:** under 150 lines. Mine is ~100. Every line is a tax paid by every session.

See `examples/CLAUDE_top_level.md` for a real one (my Trader project).

---

## Layer 2 — Subtree `CLAUDE.md`

Claude Code auto-loads any `CLAUDE.md` in the **subdirectory of the file being edited**, not just the top-level. So a `~/Trader/yarrow/CLAUDE.md` only loads when work happens inside `yarrow/`.

This is the killer feature for keeping the top-level lean. **Move component-specific facts down into the component.** The top stays general; the subtrees carry their own context.

**What goes in a subtree CLAUDE.md:**
- What this component does (one paragraph)
- Where files belong (a small table is great)
- Component-specific gotchas, conventions, "do not invent a parallel X — see Y" type rules
- Status (active / parked / experimental)
- Pointers to component-internal docs

See `examples/CLAUDE_subtree.md` for a real one.

---

## Layer 3 — Persistent auto-memory

Claude Code reads a per-project memory directory at session start: `~/.claude/projects/<project-path>/memory/`. The `MEMORY.md` file there is an *index*; other `*.md` files are entries that get pulled in on demand.

This is where you save:
- **Project state that isn't derivable from the code** (findings, regressions confirmed, things that took weeks to learn)
- **User-level feedback** (corrections, preferences, validated approaches)
- **Reference pointers** (where things live in external systems, infrastructure addresses)

Crucially, this is **NOT** the place for things you can re-derive from `git log`, current file contents, or other authoritative sources. Memory is for what the model couldn't figure out by reading the repo.

See `02_memory_layers_explained.md` for the L0..L4 model that governs *which* memory layer a fact belongs in.

---

## Layer 4 — Session state on disk: `lore/` + `tasks/`

The two directories I've found most valuable to actually commit to the repo:

### `lore/notes_YYYY-MM-DD.md`
Daily session notes. Captures:
- What I tried today
- What worked, what didn't, what surprised me
- Mid-session decisions and the reasoning behind them
- Pre-compaction handoffs (before context gets summarized)

These rot fast as "today's news" but become the **historical record** of how the project arrived at its current shape. I mine these later when writing blog posts or recovering decisions I forgot the reason for.

### `tasks/TASK_XXX_short_name.md`
One file per feature/spec/experiment. Captures:
- The problem being solved
- The design decision (with reasoning)
- Implementation status (planned / in progress / shipped / abandoned)
- Results if it shipped

Tasks are stable per task; lore is stable per day.

**Rule of thumb:** if a session ends and you wrote nothing in `lore/` or updated nothing in `tasks/`, you probably did the work and lost the why. The next session (or the next person) will rediscover it.

---

## How the layers interact in a session

A typical session in my workflow:

1. **Claude loads:** top-level `CLAUDE.md` + memory index + maybe subtree CLAUDE.md if I `cd` somewhere.
2. **Claude reads (on its own or when prompted):** specific memory entries, recent `lore/` notes, current task spec.
3. **I work on something.** During the session, Claude may save new memory entries, update a task file, or write a fresh lore note.
4. **End of session:** lore note saved with what happened. If there's a corrected approach or validated choice worth keeping, a memory entry gets added too.

The point isn't to be exhaustive. The point is **the next session walks in oriented**, with the smallest possible context-window tax.

---

## When something contradicts itself across layers

Reality changes. Layers go stale.

**Priority order, source-of-truth:**
1. Current code (most authoritative — read it before claiming anything)
2. `tasks/` current state (architectural intent)
3. Recent `lore/` notes (what just happened)
4. Memory (point-in-time observations, can be old)
5. `CLAUDE.md` (slow to update, may have drift)

When you spot drift, fix it — preferably in the layer where the lie lives. Don't paper over by adding a correcting note at the wrong layer.

---

## What this looks like in practice

A folder structure I'd recommend cloning for a new project:

```
my-project/
├── CLAUDE.md                  # top-level: cross-cutting rules
├── src/
│   └── important-subsystem/
│       └── CLAUDE.md          # subtree: subsystem-specific facts
├── lore/
│   ├── notes_2026-05-20.md
│   └── notes_2026-05-25.md
├── tasks/
│   ├── TASK_001_initial_design.md
│   └── TASK_002_auth_rework.md
└── docs/
    └── architecture.md        # stable reference; CLAUDE.md links here
```

And, separately, an auto-memory store at:
```
~/.claude/projects/-home-you-my-project/memory/
├── MEMORY.md                  # index
├── user_role.md
├── feedback_*.md
├── project_*.md
└── reference_*.md
```

Read `02_memory_layers_explained.md` next for the discipline around the memory store.
