---
slug: fail-loud-never-coerce
relates-to: agents-hub-and-spoke-framework
relevance: Establishes the core error-handling philosophy that agents must reject bad input rather than silently coerce — foundational to the framework's quality controls.
selected-because: This principle is referenced by multiple other spokes (test-driven-design, debugging-ritual) and governs how agents handle errors across all code changes.
aspects-covered:
  - Validate at boundaries with specific named errors
  - No silent fallbacks for missing data
  - No bare except/catch-all without re-raise
  - Prefer crash-over-corruption
  - Language-specific patterns (Python, JS/TS, Go, Rust, Shell)
  - Trigger keywords for detecting coercion patterns
gaps:
  - Does not address warning/deprecation handling (covered in warnings-and-deprecations)
  - Does not define test-specific error handling (covered in test-driven-design)
  - No guidance on when coercion is acceptable (intentionally — the rule is absolute)
