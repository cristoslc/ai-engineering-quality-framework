---
slug: context-map-domain-model
title: Context Map & Domain Model
type: local
path: ~/.agents/agents-md-detail/context-map-domain-model.md
fetched: 2026-07-07T14:00:00Z
---

# Context Map & Domain Model

Every project MUST maintain a living Context Map and Domain Model under `docs/`. These are not one-time artifacts — they evolve with the code.

This spoke covers the **domain** surface (DDD artifacts — context maps, ubiquitous language, domain events). The **technical** surface (C4, deployment topology, tech stack) is a separate concern that lives under `docs/ARCHITECTURE.md` → `docs/technical-architecture/`. See `~/.agents/agents-md-detail/new-project-scaffolding.md` for the full two-surface scaffold.

The domain model uses zoom levels to serve two audiences: L1 for domain experts and non-technical stakeholders, L2 for developers and agents.

## Ubiquitous Language - L1 (`docs/DOMAIN-MODEL-L1.md`)

Prose glossary of domain terms, aggregate narratives, and business rules in plain language. Uses Mermaid `flowchart` diagrams for conceptual groupings and data flow direction - no formal ERD conformance, no cardinality notation, no attribute tables. Readable by domain experts, PMs, and non-technical stakeholders.

**Elements to capture:**
- Domain terms and their plain-language definitions.
- Aggregate narratives (what each aggregate represents, in prose).
- Business rules expressed in natural language.
- Flowchart diagrams showing conceptual groupings and data flow direction.

## Domain Architecture (`docs/domain-architecture/`)

Architectural artifacts that describe system structure and cross-context concerns. These are expressed in terms of the ubiquitous language but are structural, not vocabulary.

### Context Map (`docs/domain-architecture/CONTEXT-MAP.md`)

A Mermaid `flowchart` diagram of bounded contexts and their relationships. Shows system boundaries and integration patterns.

**Relationship types:**
- Upstream/downstream (supplier/consumer).
- Partnership (peer-to-peer).
- Shared kernel (shared subset of domain model).
- Conformist (downstream conforms to upstream).
- Anticorruption layer (translation layer).
- Open host service / published language.

### Domain Events - Cross-Context (`docs/domain-architecture/DOMAIN-EVENTS.md`)

Integration events that cross bounded context boundaries. Each event records the producing context, consuming context(s), payload shape, and delivery mechanism. Internal domain events (within a single bounded context) belong in the bounded context's README.md leaf, not here.

### Ubiquitous Language - L2 (`docs/domain-architecture/DOMAIN-MODEL-L2.md`)

Full technical semantics for developers and agents. Uses Mermaid ERD (entity-relationship) diagrams with cardinality, attribute tables, event tables, invariants, and aggregate boundaries.

**Elements to capture:**
- Entities (identity-based, mutable).
- Value objects (attribute-based, immutable).
- Aggregates (consistency boundaries).
- Domain events (things that happened) - internal events only; cross-context events go in DOMAIN-EVENTS.md.
- Repositories (collection access).
- Services (stateless operations).
- Factories (creation logic).
- Invariants and business rules with formal annotations.

## Bounded Context Leaves

Each bounded context SHOULD have a README.md leaf under `docs/domain-architecture/bounded-contexts/<name>/` that captures context-internal conventions, domain events, entity details, and team-specific guidance.

These leaves are **hub-referenced**, not auto-loaded by the agent harness. Agent harnesses discover AGENTS.md files only at project root or known top-level paths; deeply nested leaves under `docs/domain-architecture/bounded-contexts/` are invisible to automatic scanning. Each leaf is referenced manually from the root AGENTS.md hub table.

`README.md` is chosen for its conventional recognition — GitHub renders it automatically, and both agents and humans instinctively look for it when navigating a directory. The Events table listing all internal domain events for that context SHOULD be included.

## Discipline

Before writing any implementation code, the agent MUST ensure the Context Map and Domain Model exist and are up to date for the area being modified. If they don't exist, create them. If they're stale, update them. If the change introduces a new bounded context or entity, add it.

## Location

```
docs/
  DOMAIN-MODEL-L1.md                    - UL: prose glossary + flowchart groupings/data flow
  domain-architecture/
    CONTEXT-MAP.md                      - BC relationships (flowchart)
    DOMAIN-EVENTS.md                    - cross-context integration events
    DOMAIN-MODEL-L2.md                  - UL technical: ERDs, event tables, invariants
    bounded-contexts/
      <name>/
        README.md                         - hub-referenced leaf per bounded context
  technical-architecture/
    context.md                            - C4 system context diagram
    container.md                          - C4 container diagram
    component.md                          - C4 component diagram
```

## Verification

After any update, run `mmdc` to validate all Mermaid diagrams render correctly.

## Triggers

`context map`, `domain model`, `bounded context`, `DDD`, `domain-driven design`, `entity`, `value object`, `aggregate`, `ubiquitous language`, `system boundary`, `integration pattern`, `docs/context-map`, `docs/domain-model`, `docs/CONTEXT-MAP`, `docs/DOMAIN-MODEL`, `docs/domain-architecture`, `DOMAIN-EVENTS`, `domain events`, `cross-context events`, `integration events`, `zoom level`, `L1`, `L2`, `ERD`, `entity-relationship`. For C4 and tech-stack concepts, see the Technical Architecture hub.
