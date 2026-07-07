---
slug: tool-usage
relates-to: agents-hub-and-spoke-framework
relevance: Defines the tool-invocation conventions (shell, git, web, python, llm) that subagents must follow when dispatched by the hub, ensuring consistent tool usage across the framework.
selected-because: The hub-and-spoke framework must specify which tools subagents can use and how; this spoke provides the authoritative tool-usage rules for the agent ecosystem.
aspects-covered:
  - Tool diagnostic protocol (don't use broken tool to diagnose)
  - 1Password CLI usage rules
  - Shell command conventions
  - Web browsing tool preferences
  - Python/uv dependency management
  - Git and Forgejo CLI conventions
  - WIP/draft PR protocol
  - LLM CLI one-shot usage
  - Vision analysis dispatch
gaps:
  - Does not cover MCP server tool usage
  - Does not address tool permission scoping for subagents
---
