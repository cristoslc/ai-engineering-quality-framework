---
slug: todo-discipline
relates-to: agents-hub-and-spoke-framework
relevance: Documents the todo/task tool schemas across OpenCode and Claude Code, which agents in the hub-and-spoke framework must use correctly to track work.
selected-because: Todo discipline is a cross-cutting concern that affects all agents working within the hub-and-spoke framework, and the common mistakes documented here are directly relevant to framework users.
aspects-covered:
  - OpenCode todowrite schema (status, priority)
  - Claude Code legacy TodoWrite schema
  - Claude Code new TaskCreate/TaskUpdate/TaskGet/TaskList
  - Cross-agent gotchas comparison table
  - Common mistakes (stringified JSON, wrong schema, missing fields)
gaps:
  - Does not cover task prioritization strategies
  - No guidance on task decomposition or work breakdown
  - Does not address multi-agent task coordination
---
