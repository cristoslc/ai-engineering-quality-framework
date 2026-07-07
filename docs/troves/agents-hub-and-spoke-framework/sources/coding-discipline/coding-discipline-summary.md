---
slug: coding-discipline
relates-to: agents-hub-and-spoke-framework
relevance: Defines the three foundational principles (domain model, single source of truth, YAGNI ladder) that every code change must be verified against before implementation.
selected-because: Provides the architectural and design discipline that complements the testing-focused spokes, ensuring code changes respect domain boundaries and avoid premature abstraction.
aspects-covered:
  - Follow the domain model and architecture (bounded contexts, ubiquitous language)
  - Single authoritative source of truth (no duplication, no drift)
  - YAGNI ladder (5 rungs from hard-code to language)
  - Verification checklist for code changes
gaps:
  - Does not address testing discipline (covered in test-driven-design and debugging-ritual)
  - Does not define how to handle pre-existing violations of these principles
  - No guidance on when to refactor vs. when to accept technical debt
