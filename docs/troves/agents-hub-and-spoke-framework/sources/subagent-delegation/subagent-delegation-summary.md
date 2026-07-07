---
slug: subagent-delegation
relates-to: agents-hub-and-spoke-framework
relevance: Defines the model-tier hierarchy (heavy/medium/light) used by the hub to dispatch subagent tasks, directly informing the delegation protocol in the hub-and-spoke architecture.
selected-because: The hub-and-spoke framework requires a clear model-selection policy for subagent dispatch; this spoke provides the authoritative tier classification.
aspects-covered:
  - Model tier classification (heavy/medium/light)
  - Subagent delegation rules
  - Trigger keywords for delegation
gaps:
  - Does not specify dispatch mechanics (lock-watch, timeout, cleanup)
  - Does not address concurrent subagent management
---
