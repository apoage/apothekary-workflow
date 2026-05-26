# Sharing my Claude Code workflow

This is the package I share when someone asks *"how do you actually instruct and onboard Claude Code on a project?"* It's not a how-to-use-Claude-Code tutorial — it's how **I** structure long-lived work so the model walks in oriented every session.

Author: Lukas (apoage@gmail.com) — apothekary.dev

---

## What's in here

```
share_structure/
├── README.md                              ← you are here
├── 01_layered_workflow.md                 ← the four pillars: CLAUDE.md hierarchy + memory + lore/ + tasks/
├── 02_memory_layers_explained.md          ← L0..L4 memory model — what to save where
├── 03_onboarding_new_project.md           ← KICKSTART pattern — seeding a fresh project's memory
├── 04_settings_and_retention.md           ← settings.json, hooks, transcript retention
└── examples/
    ├── CLAUDE_top_level.md                ← real top-level CLAUDE.md (cross-cutting rules)
    ├── CLAUDE_subtree.md                  ← real subtree CLAUDE.md (component-specific facts)
    ├── KICKSTART.md                       ← real first-session onboarding doc
    ├── MEMORY_index.md                    ← real MEMORY.md index
    ├── memory_entry_meta.md               ← real L2 memory entry (the meta one, about the memory model itself)
    └── settings.json                      ← my actual settings.json (sanitized)
```

---

## The core idea in one paragraph

Claude Code has several persistence layers — context window, in-project `CLAUDE.md` files, an auto-memory directory it reads each session, plus whatever discipline you bring to documenting work on disk. None of these are magic. **What matters is which layer you put which fact in**, and being honest about what rots fast vs what stays stable. If you mix "I tried X today and it failed" with "X is the way this project is structured" in the same place, you'll keep paying context-window cost for stale narrative *and* poisoning future sessions with outdated assumptions.

So I split it into four layers, and the discipline is just: **put the right thing in the right layer, and trust subsequent sessions to read it.**

Start with `01_layered_workflow.md`.

---

## Things this package will not teach

- How to install Claude Code (it's in the official docs)
- Slash-command authoring (see `~/.claude/skills/` docs)
- Anything project-specific to my work (Trader is a research codebase; the *structure* generalizes, the *content* doesn't)

Things I deliberately do NOT do, that you might:

- I don't use Cursor rules or per-language linter integration — happy with Claude Code's built-ins
- I don't have a CI/CD pipeline asking Claude for code review on every PR — my use is interactive
- I don't share memory across projects globally — each project has its own memory store

Your mileage will vary by project shape (research repo vs product codebase vs infra-as-code vs personal notebook). The *layering* is the lesson; the *contents* are mine.
