---
slug: two-stage-pipelines
relates-to: agents-hub-and-spoke-framework
relevance: Defines the staging pipeline contract that every project must implement, which is a key workflow pattern referenced by the hub-and-spoke framework's sashay ritual.
selected-because: The two-stage pipeline is a core operational pattern that agents must understand to implement the deployment and E2E testing phases of the sashay closure loop.
aspects-covered:
  - Staging vs production deployment targets
  - Script conventions (setup, deploy, e2e, teardown)
  - Production safety guards for teardown
  - Branch name sanitization (RFC 1123)
  - Cost budget gates
  - SPC fitness functions and anomaly detection
  - Sashay closure loop integration
gaps:
  - Does not cover specific cloud provider implementations
  - Does not address multi-region staging
  - No guidance on staging database seeding strategies
---
