# Onboarding a fresh project (the KICKSTART pattern)

When you start Claude Code in a directory for the first time, it has **no memory of this project**. The auto-memory store is empty. The model walks in cold, reads `CLAUDE.md` if present, and that's it.

This is the right time to **seed memory deliberately** — write a small set of foundational entries that bind the project's identity in your head and in the model's working set.

I keep a `KICKSTART.md` file in the project root for this purpose. It's a single document you paste into the first Claude Code session and ask it to execute.

---

## Why bother seeding at all?

Two reasons:

1. **The model can derive a lot from the repo, but not everything.** It can read your code, your docs, your file layout. It cannot read your role, your preferences, your past failures, the reasons behind your conventions, or the agreements you reached with your team. Those need to be told.

2. **Writing the KICKSTART forces you to articulate the project.** If you can't write five sentences about what this project is, who you are in relation to it, and how you'd like Claude to behave — the model isn't going to figure it out either.

---

## What a KICKSTART file looks like

A KICKSTART is two parts:
1. **A short prose intro** for a human (you, or someone else opening the project later) about what this onboarding does.
2. **A prompt block** you paste to Claude in the first session, with explicit "save these to memory" instructions.

The prompt block tells the model:
- What kind of memory entry to write (project / user / feedback / reference — see `02_memory_layers_explained.md`)
- The exact content
- Where this fits in the project ecology

**Example: `examples/KICKSTART.md`** is the real one from my Bloom (publishing-pathway) project. Notice it seeds 8 entries covering project purpose, source material, isolation from parent project, infra references, voice rules, specimen mapping, user role, and pipeline pointers.

---

## A good KICKSTART has these properties

1. **Tells Claude what NOT to save.** Most fresh sessions over-eagerly save things like file listings, current draft contents, transient state. KICKSTART pre-empts that with explicit "don't seed X" guidance.

2. **Includes a session-start checklist.** Mine ends with a recurring checklist Claude should run on every subsequent session start — e.g. *"check `drafts/`, glance at `TIMELINE.md`, look for `PAUSE.md` if touching infra."* This bakes in the project's reading order.

3. **Lists absolute paths.** New-session Claude doesn't know your repo's relative-path conventions. Spell out the full paths to important locations.

4. **Stays under ~80 lines.** If your onboarding doc is 300 lines, the project is too complicated to onboard cleanly; refactor before continuing.

---

## How I write a new project's KICKSTART

1. Make the new project directory, write a minimal top-level `CLAUDE.md` (10–20 lines: what is this, what's the layout).
2. Make `~/.claude/projects/<project-path>/memory/` (just `mkdir -p`).
3. Write `KICKSTART.md` in the project root.
4. Run `claude` from the project root, paste the prompt block from KICKSTART.
5. The model reads the relevant project files and writes its first memory entries.
6. Verify by reading the memory dir — confirm what got written matches what you wanted.

That's it. The next session will load `CLAUDE.md` + the new `MEMORY.md` index and walk in oriented.

---

## When NOT to write a KICKSTART

- For a quick experimental script you'll throw away → don't bother, just open `claude` and go.
- For a project where the README is comprehensive and the model just needs to read it → a small `CLAUDE.md` saying *"read README.md before doing anything"* is enough.
- For a project you'll touch once and never return to → memory is overhead with no payoff.

KICKSTART pays off when:
- The project will span many sessions
- The project has conventions / vocabulary that aren't in code
- You'll want a future session (yours or someone else's) to walk in oriented
- The project is one of several you maintain, and you don't want to remember which is which

---

## The recurring session-start checklist

Every project of mine has a section in `KICKSTART.md` (or CLAUDE.md) that says: *"at the start of each session, do these things."*

For my work it looks something like:

> 1. Check `lore/` for the most recent note — what was the previous session about?
> 2. Check `tasks/` for active task spec — what is being worked on?
> 3. If touching infrastructure, check `<infra-dir>/PAUSE.md` for explicit holds.
> 4. `git log --oneline -10` if there's been activity since the last note.

This costs the model a few `Read`s at session start. The payoff is the model walking in *actually* oriented, not just having loaded a generic CLAUDE.md.

---

## Friend FAQ I'd expect

**"Do I have to ask Claude to save memory manually?"**
No. Once the KICKSTART has run, subsequent sessions auto-load `MEMORY.md` (and pull entries as relevant). The KICKSTART is just for the *initial seeding* — and for situations where you've added a project to a fresh machine with no prior memory.

**"What if I move the project to a new path?"**
Memory is keyed by absolute project path. Moving the project breaks the memory linkage. You either rerun KICKSTART (clean start) or copy the old `~/.claude/projects/<old-path>/` dir to the new path slug.

**"Can I share memory across projects?"**
You can — by reading entries from one project's memory dir while working in another, via absolute path. I don't usually do this; I prefer scoped memory. If two projects truly share the same context, they're probably one project.

**"What if I edit CLAUDE.md and forget to update memory?"**
Drift happens. The fix is the priority order from `01_layered_workflow.md`: current code > tasks > recent lore > memory > CLAUDE.md. The model should treat older layers as suspect when they contradict newer ones.
