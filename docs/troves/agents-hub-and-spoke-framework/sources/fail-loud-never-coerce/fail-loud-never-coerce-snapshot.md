---
slug: fail-loud-never-coerce
title: Fail Loud; Never Coerce
type: local
path: ~/.agents/agents-md-detail/fail-loud-never-coerce.md
fetched: 2026-07-07T14:00:00Z
---

# Fail Loud; Never Coerce

The AI's instinct is to be helpful — silently coerce bad data into a working shape, substitute defaults for missing values, catch and swallow errors so things "just work." This instinct is exactly wrong. Silent recovery hides the signal the operator needs and replaces it with confident wrongness.

## Rules

### Validate at boundaries

Reject bad input at the entry point with a specific, named error. Do not coerce, substring-match, or silently default your way past it.

### No silent fallbacks

Missing data, failed calls, and unexpected shapes must surface as errors. Never substitute guessed values or return empty results when the real data is absent.

### No bare except / catch-all without re-raise

The only valid catch-all pattern is logging + re-raise, or wrapping in a domain-specific error with structured context (relevant IDs, inputs, state). Swallowing exceptions silently is forbidden.

### When in doubt, fail visibly

A loud failure is debugged in seconds. A silent coercion becomes a production incident discovered weeks later. Prefer crash-over-corruption.

## Language-Specific Patterns

### Python

```python
# WRONG — silent coercion
def get_count(raw: str) -> int:
    try:
        return int(raw)
    except Exception:
        return 0  # silently returns 0 for garbage input

# RIGHT — raise a specific error
class InvalidCountError(ValueError):
    pass

def get_count(raw: str) -> int:
    try:
        return int(raw)
    except ValueError:
        raise InvalidCountError(f"count must be an integer, got {raw!r}") from None
```

### JavaScript / TypeScript

```typescript
// WRONG — silent fallback to default
function parseUserRole(input: unknown): UserRole {
  if (!input || typeof input !== "string") {
    return { role: "guest", permissions: [] }; // attacker can force guest access
  }
  if (input.includes("admin")) {
    return { role: "admin", permissions: ["all"] }; // substring match is dangerous
  }
  return { role: "user", permissions: ["read"] };
}

// RIGHT — exact match, reject unknowns
type UserRole = { role: "admin" | "user"; permissions: string[] };
const VALID_ROLES = new Set(["admin", "user"]);

function parseUserRole(input: unknown): UserRole {
  if (typeof input !== "string" || !VALID_ROLES.has(input)) {
    throw new TypeError(`invalid role: expected "admin" or "user", got ${String(input)}`);
  }
  // role determines permissions, not input guessing
  return { role: input, permissions: input === "admin" ? ["all"] : ["read"] };
}
```

### Go

```go
// WRONG — swallowing the error with a zero value
func getCount(raw string) int {
    n, err := strconv.Atoi(raw)
    if err != nil {
        return 0 // garbage in, silent 0 out
    }
    return n
}

// RIGHT — bubble up the error
func getCount(raw string) (int, error) {
    n, err := strconv.Atoi(raw)
    if err != nil {
        return 0, fmt.Errorf("getCount: invalid number %q: %w", raw, err)
    }
    return n, nil
}
```

### Rust

```rust
// WRONG — coerces missing config to an empty default
fn get_db_url(cfg: &Config) -> String {
    cfg.get("db_url").unwrap_or_default()
}

// RIGHT — fails on missing config, surfaces context
fn get_db_url(cfg: &Config) -> Result<String, ConfigError> {
    cfg.get("db_url")
        .ok_or_else(|| ConfigError::MissingField { field: "db_url".into() })
        .map(String::from)
}
```

### Shell

```bash
# WRONG — silently skipping invalid input
process_file() {
    local file="$1"
    [[ -f "$file" ]] || return 0   # silently exits, caller thinks it succeeded
    do_work "$file"
}

# RIGHT — fail with a clear message and non-zero exit
process_file() {
    local file="$1"
    if [[ ! -f "$file" ]]; then
        echo "process_file: not a regular file: $file" >&2
        return 1
    fi
    do_work "$file"
}
```

## Triggers

`coerce`, `fallback`, `default value`, `bare except`, `catch-all`, `swallow`, `silent failure`, `graceful degradation`.
