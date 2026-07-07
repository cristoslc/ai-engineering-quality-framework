---
slug: agent-testing
relates-to: agents-hub-and-spoke-framework
relevance: Defines the complete testing taxonomy for agent systems — orchestration integration, skill behavioral contracts, adversarial inputs, MCP server tests, and non-deterministic output validation — all directly applicable to testing the hub-and-spoke framework.
selected-because: The hub-and-spoke framework requires a rigorous testing strategy for subagent dispatch, skill execution, and orchestration protocols; this spoke provides the authoritative test types and protocols.
aspects-covered:
  - Orchestration integration tests (dispatch, lock-watch, turn sequencing)
  - Skill behavioral contracts (YAML contract format, schema validation)
  - Adversarial input testing (prompt injection, out-of-scope, tool misuse)
  - MCP server integration tests (server-level + model integration)
  - Non-deterministic output with structured contracts
  - Cost model for LLM-invoking tests
  - Relationship to universal test types
gaps:
  - Does not provide concrete test implementations for the hub-and-spoke framework
  - Does not address performance or load testing of subagent dispatch
---
