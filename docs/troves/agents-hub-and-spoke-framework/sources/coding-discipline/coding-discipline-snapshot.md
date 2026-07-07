---
slug: coding-discipline
title: Coding Discipline
type: local
path: ~/.agents/agents-md-detail/coding-discipline.md
fetched: 2026-07-07T14:00:00Z
---

# Coding Discipline

Every code change MUST be grounded in three principles. Before writing or editing any file, verify your change against all three.

## 1. Follow the Domain Model & Architecture

Every change must be grounded in the project's context map and domain model (`docs/`). Before writing code:

- Understand the bounded contexts, aggregate boundaries, and ubiquitous language
- Do not introduce concepts that don't exist in the model
- Do not bypass architectural boundaries (e.g., one context reaching directly into another's persistence layer)
- If the model doesn't cover what you need, surface the gap — don't work around it

## 2. Single, Authoritative Source of Truth

Every piece of knowledge — config, schema, business logic, routing — MUST live in exactly one place. No duplication, no drift.

When you need to change something, you change it once. If you find yourself updating the same value in two files, that's a smell. Extract to a canonical source and reference it.

This applies especially to:
- Event contracts (YAML specs in `docs/domain-architecture/events/`)
- Data schemas
- Configuration defaults
- Routing / URL maps
- Business rules / invariants

## 3. The YAGNI Ladder

From [ponytail](https://github.com/ponytail/ponytail)'s YAGNI ladder — a decision framework for when to build generality vs. when to stay specific:

| Rung | Approach | When to use |
|------|----------|-------------|
| 1 | Hard-code it | One use case, no known variant |
| 2 | Parameterize it | Same logic, different values |
| 3 | Abstract it | Multiple implementations of same interface |
| 4 | Plugin it | Third parties need to extend |
| 5 | Language it | Users need to express logic in config |

**Rule:** Start at the lowest rung that works. Only climb when you have evidence (not speculation) that the next rung is needed. Premature abstraction is the root of all evil — but so is premature concreteness when the second variant is already in sight.

## Verification Checklist

Before applying any edit, ask:

1. **Domain model:** Does this change respect bounded context boundaries? Does it use the ubiquitous language?
2. **Source of truth:** Is this value/config/rule already defined somewhere else? Am I creating a duplicate?
3. **YAGNI:** What rung am I on? Do I have evidence for climbing higher, or am I speculating?
