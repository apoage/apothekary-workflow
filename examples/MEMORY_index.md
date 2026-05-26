# Example: MEMORY.md index

_From Bloom project. Demonstrates: index-only (no memory content directly inline), one-line hooks per entry, kept under 200 lines to fit auto-load budget. Each linked file has full YAML frontmatter and content._

---

# Memory index

- [Bloom project purpose](project_bloom_purpose.md) — publishing pathway for Trader, 2-month lag, daily-ish cadence
- [Source material locations](project_source_material.md) — `~/Trader/lore/`, `tasks/`, `docs/`; consult TIMELINE.md first
- [Bloom isolation from Trader](project_isolation_from_trader.md) — Trader CLAUDE.md/lore/tasks/memory NOT auto-loaded; reach by absolute path
- [VPS state pointer](project_vps_state.md) — read `bloom/vps/PAUSE.md` for live state; CX33 (x86), Ghost+Oracvlvm live, port 25 unblocked 2026-05-25, Stalwart planned
- [Apothekary + VPS infra docs](reference_apothekary_vps.md) — locations under `bloom/vps/` (incl. `phase-1/` runbooks) and `bloom/apothekary/theme/`; not Phase-0 anymore
- [Secrets in context](feedback_secrets_in_context.md) — grep specific fields from secret-bearing files, don't full-Read
- [Voice canon](feedback_voice_canon.md) — PERSONA.md rules; botanical frame is not metaphor; never break frame
- [Specimen mapping](feedback_specimen_mapping.md) — Poppy/Bella/Foxglove/Yarrow → real Trader components
- [User role](user_role.md) — Lukas, Trader author; terse, no trailing summaries
- [Pipeline Python](reference_pipeline.md) — `bloom/pipeline/` modules to read before extending
- [Hosts and SSH aliases](reference_hosts.md) — `trader-srv` (training, multi-3090), VPS `178.105.0.215` / `10.10.0.1`, this box `terminus`
