---
slug: event-contracts
relates-to: agents-hub-and-spoke-framework
relevance: Defines the machine-checkable event contract system that bounded contexts use to communicate, which is a key integration pattern in the hub-and-spoke framework's domain architecture.
selected-because: Event contracts are the mechanism by which bounded contexts (spokes) communicate, making them essential to understanding cross-context integration in the framework.
aspects-covered:
  - Meta-schema for event spec validation
  - YAML spec file conventions
  - Scalar shorthand for payload fields
  - Validation script and CI integration
  - Drift detection (not yet implemented)
  - Event versioning and deprecation
gaps:
  - No drift detection implementation yet
  - Does not cover event delivery mechanisms (Kafka, RabbitMQ, etc.)
  - No guidance on event schema evolution strategies
---
