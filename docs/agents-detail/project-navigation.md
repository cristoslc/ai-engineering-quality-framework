# Project Navigation

## Starting points

- `PURPOSE.md` — one-paragraph outcome
- `README.md` — user-facing landing page
- `AGENTS.md` — this file, plus `docs/agents-detail/` for agent-specific guidance

## Hubs and spokes

The `docs/` tree splits into two architectural surfaces plus cross-cutting hubs:

Technical architecture (how the quality control system is structured):
- `docs/ARCHITECTURE.md` → `docs/technical-architecture/`
- `docs/technical-architecture/c4/{context,container,component,deployment}.md`
- `docs/technical-architecture/tech-stack.md`

Domain architecture (the quality control domain model):
- `docs/DOMAIN-MODEL-L1.md` — L1 prose glossary
- `docs/domain-architecture/CONTEXT-MAP.md` — bounded context relationships
- `docs/domain-architecture/DOMAIN-EVENTS.md` — cross-context integration events
- `docs/domain-architecture/DOMAIN-MODEL-L2.md` — L2 ubiquitous language

Cross-cutting:
- `docs/DEVELOPER-WORKFLOWS.md` → `docs/developer-workflows/`
- `docs/USER-EXPERIENCE.md` → `docs/user-experience/`
- `AGENTS.md` → `docs/agents-detail/`
- `docs/adr/` — numbered decision records
- `docs/plans/` — implementation plans and specs
- `docs/musings/` — pre-artifact thought capture
- `docs/retros/` — post-session reflection docs
- `docs/tech-debt/` — pre-existing issues
- `docs/troves/` — research source materials

Read the hub first, then drill into spokes when you need detail.
