---
slug: subagent-delegation
title: Subagent Delegation
type: local
path: ~/.agents/agents-md-detail/subagent-delegation.md
fetched: 2026-07-07T14:00:00Z
---

# Subagent Delegation

In the Claude ecosystem, subagents should never use `heavy` unless the task is explicitly marked heavy. Otherwise, choose `medium` or `light` based on the complexity of the task — the parent is responsible for making that call when there is no explicit hint.

- Heavy models: Claude Opus, GLM-5.1, GPT-5.x
- Medium models: Claude Sonnet, Kimi-K2.6, Minimax-M2.7
- Light models: Claude Haiku, Minimax-M2.5, GPT mini
- Other models: if uncertain, check litellm and other internet sources to classify available models or ask the operator for guidance

## Triggers

`subagent`, `delegate`, `delegation`, `heavy`, `medium`, `light`, `sonnet`, `haiku`, `opus`, `kimi`, `minimax`.
