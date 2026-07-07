---
slug: warnings-and-deprecations
relates-to: agents-hub-and-spoke-framework
relevance: Defines the three-category system for handling warnings (actionable, deferred actionable, not actionable) that agents must follow instead of suppressing errors.
selected-because: Complements the fail-loud-never-coerce principle by providing a structured approach to warnings that avoids silent suppression while allowing practical deferral.
aspects-covered:
  - Three-category warning classification (actionable, deferred actionable, not actionable)
  - Deferred actionable protocol (plan file, targeted suppression, re-evaluate trigger)
  - Not actionable permanent suppression with documentation
  - Suppression mechanisms by tool/language
  - Noisy-test discipline
  - Trigger keywords
gaps:
  - Does not address LSP-specific error handling (covered in lsp-errors)
  - Does not define when a warning crosses into actionable territory (left to agent judgment)
  - No guidance on warning severity classification
