---
slug: test-driven-design
relates-to: agents-hub-and-spoke-framework
relevance: Defines the universal test strategy including test-first discipline, pre-test inventory, coverage matrix, and test tier system that all agent spokes must follow.
selected-because: Core procedural document governing how agents write tests, handle failures, and validate changes — foundational to the hub-and-spoke framework's quality controls.
aspects-covered:
  - Test-first discipline for defects
  - Pre-test inventory and coverage matrix (MTP/LTP)
  - Blast-radius risk categories (Safety, Security, Financial, Privacy, Availability, Integrity)
  - Inverse-assertion requirement
  - Required test coverage types and timing
  - Non-deterministic testing with pass^k metrics
  - Test tier system (unit/integration/staging)
  - Test command declaration protocol
  - Language-specific testing patterns
gaps:
  - Does not define the operator-assisted E2E interactive protocol (delegated to operator-assisted-e2e spoke)
  - Does not cover safety-critical testing specifics (delegated to safety-critical-testing spoke)
  - Does not address event contract testing (delegated to event-contracts spoke)
