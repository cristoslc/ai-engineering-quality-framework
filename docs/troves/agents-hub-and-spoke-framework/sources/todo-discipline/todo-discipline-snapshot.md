---
slug: todo-discipline
title: Todo Discipline — `todowrite` / `TodoWrite` / `TaskCreate` Reference
type: local
path: ~/.agents/agents-md-detail/todo-discipline.md
fetched: 2026-07-07T14:00:00Z
---

# Todo Discipline — `todowrite` / `TodoWrite` / `TaskCreate` Reference

## The Cardinal Rule (all agents)

**The `todos` parameter MUST be a native JSON array — NOT a JSON-stringified string.**

### Correct (native array — tool will succeed):

```ts
todos: [
  { content: "Fix login bug", status: "in_progress", priority: "high" },
  { content: "Write tests", status: "pending", priority: "medium" },
]
```

### Wrong (stringified — tool will fail with `SchemaError`):

```ts
todos: "[{\"content\":\"Fix login bug\",\"status\":\"in_progress\",\"priority\":\"high\"}]"
```

**Why:** The LLM tool-calling layer serializes arguments as JSON. If you pass a string that *looks like* a JSON array, it sends as a quoted string literal. The schema validator expects a native array, sees a string, and throws.

---

## OpenCode — `todowrite`

Source: `packages/core/src/tool/todowrite.ts`, schema: `packages/core/src/session/todo.ts`.

```ts
todos: Array<{
  content: string   // Brief description of the task
  status: string    // One of: "pending" | "in_progress" | "completed" | "cancelled"
  priority: string  // One of: "high" | "medium" | "low"
}>
```

| Status | Meaning |
|--------|---------|
| `pending` | Not started |
| `in_progress` | Actively working (exactly one at a time) |
| `completed` | Finished successfully |
| `cancelled` | No longer needed |

| Priority | Meaning |
|----------|---------|
| `high` | Critical path |
| `medium` | Important but not blocking |
| `low` | Nice-to-have |

**Strict schema:** Each item needs exactly `content`, `status`, `priority`. No extra keys (`id`, `position`, `completedAt`, etc.). Rejects partial objects.

---

## Claude Code — Legacy `TodoWrite` / New `TaskCreate`

Two eras exist depending on Claude Code version.

### Legacy (`TodoWrite` + `TodoRead`) — v2.1.141 and earlier

Re-enable with `CLAUDE_CODE_ENABLE_TASKS=0`.

```ts
todos: Array<{
  content: string     // Imperative task description, e.g. "Run tests" (minLength: 1)
  status: string      // One of: "pending" | "in_progress" | "completed"
  activeForm: string  // Present continuous, e.g. "Running tests" (minLength: 1)
}>
```

**Note:** No `priority` field. No `cancelled` status. The `activeForm` field is **required** — every item must have it.

### New (`TaskCreate`, `TaskUpdate`, `TaskGet`, `TaskList`) — v2.1.142+

| Tool | Input |
|------|-------|
| `TaskCreate` | `{ subject, description, activeForm?, metadata? }` |
| `TaskUpdate` | `{ taskId, status?, subject?, description?, activeForm?, addBlocks?, addBlockedBy?, owner?, metadata? }` |
| `TaskList` | (no input — returns snapshot) |
| `TaskGet` | `{ taskId }` |

Status values for Task tools: `"pending"`, `"in_progress"`, `"completed"`, `"deleted"` (set to delete).

---

## Cross-Agent Gotchas

| Concern | OpenCode | Claude Code (legacy) | Claude Code (new) |
|---------|----------|---------------------|-------------------|
| Tool name | `todowrite` | `TodoWrite` | `TaskCreate` |
| Array pitfall | **Yes** | **Yes** | N/A (single object) |
| `priority` field | Required | Not accepted | Not accepted |
| `activeForm` field | Not accepted | Required | Optional |
| `cancelled` status | Supported | Not supported | N/A (use `deleted`) |
| Extra key rejection | Strict | Strict | Lenient (via `metadata`) |

## Common Mistakes

1. **Stringified JSON** — Always pass a bare array, not `JSON.stringify(...)`.
2. **Wrong agent's schema** — Don't put `activeForm` in an OpenCode todo, or `priority` in a Claude Code one.
3. **Missing required fields** — Every agent's schema rejects partial objects.
4. **Forgetting to call** — Agents often complete work without marking items done, leading to stale state.
