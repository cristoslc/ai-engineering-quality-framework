---
slug: debugging-ritual
relates-to: agents-hub-and-spoke-framework
relevance: Defines the 8-step debugging ritual that agents must follow when encountering runtime errors — from reproduction through test-first fix to commit discipline.
selected-because: The debugging ritual is the operationalization of the test-first mandate, providing a concrete step-by-step protocol that agents execute during bug fixes.
aspects-covered:
  - 8-step debugging ritual (reproduce, write failing test, isolate root cause, write additional tests, implement fix, verify, full suite, commit)
  - Test-first mandate for all bug fixes
  - Catch-yourself-before-you-fix pattern recognition
  - Debugging techniques (git bisect, assertions over prints, isolation, check obvious first)
  - Trigger keywords for user symptoms and agent fix-intent language
gaps:
  - Does not define the pre-test inventory or coverage matrix (covered in test-driven-design)
  - Does not address non-deterministic testing or pass^k metrics
  - Does not cover operator-assisted E2E protocol
