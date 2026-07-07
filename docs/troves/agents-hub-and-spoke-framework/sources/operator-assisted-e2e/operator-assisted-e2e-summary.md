---
slug: operator-assisted-e2e
relates-to: agents-hub-and-spoke-framework
relevance: Defines the complete interactive protocol for operator-assisted E2E and UAT testing, including YAML suite format, state machine, and report generation.
selected-because: Provides the detailed implementation of the operator-assisted E2E protocol referenced by test-driven-design, essential for tests requiring human interaction.
aspects-covered:
  - YAML test suite schema and format
  - State machine (DRAFT, READY, IN-PROGRESS, PASSED, FAILED, BLOCKED, SKIPPED, RETEST)
  - Interactive execution protocol with operator prompts
  - Report generation format
  - Test-first mandate for operator-assisted testing
  - Retest loop for blocker and standard failures
  - Evidence collection on failure
gaps:
  - Does not define the automated E2E testing protocol (covered in test-driven-design)
  - Does not cover non-deterministic testing or LLM-based judgement
  - No guidance on integrating operator-assisted tests with CI pipelines
