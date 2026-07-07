---
slug: event-contracts
title: Event Contract Scaffolding
type: local
path: ~/.agents/agents-md-detail/event-contracts.md
fetched: 2026-07-07T14:00:00Z
---

# Event Contract Scaffolding

## Overview

Event contracts give bounded contexts a machine-checkable specification for cross-context events. Each event gets a YAML spec that conforms to a JSON Schema meta-schema; a validation script confirms every spec matches the meta-schema before merge. This catches producer/consumer drift at PR time instead of at runtime.

## Locations

| Path | Purpose |
|---|---|
| `docs/domain-architecture/events/schemas/event-spec.schema.yaml` | Meta-schema defining valid event spec shape |
| `docs/domain-architecture/events/<event-name>.yaml` | One YAML spec per event (kebab-case filename) |
| `scripts/validate-event-contracts.sh` | Validator script run during sashay closure |
| `~/.agents/agents-md-detail/event-contracts.md` | This spoke |

The meta-schema's `$id` (currently `https://example.com/swain/event-spec.schema.yaml`) is a stable identifier — do not change it without a version bump, since external validators may cache by `$id`.

## Meta-schema

`event-spec.schema.yaml` is JSON Schema draft-07 expressed in YAML. Required fields:

| Field | Type | Constraint |
|---|---|---|
| `event` | string | Pattern `^[A-Z][a-zA-Z0-9]+(-[A-Z][a-zA-Z0-9]+)*$` — PascalCase, optionally hyphenated for compound names like `Order-Submitted` |
| `version` | integer | Minimum 1, monotonically increasing |
| `producer` | string | Name of the bounded context that produces the event |
| `consumers` | array of strings | Minimum 1, must be unique |
| `payload` | object | Minimum 1 property; each value's type must be one of `string`, `integer`, `number`, `boolean`, `array`, `object` |

Optional fields:

| Field | Type | Constraint |
|---|---|---|
| `description` | string | Human-readable description of when the event fires |
| `since` | integer | Minimum 1 — version in which this event was introduced |
| `deprecated` | integer | Minimum 1 — version in which this event was deprecated (not yet removed) |

## Event spec files

One YAML file per event under `docs/domain-architecture/events/`. Filename is the kebab-case form of `event` (e.g., `OrderSubmitted` → `order-submitted.yaml`). The meta-schema does not enforce filename shape, but the convention is mandatory for validator discovery and operator scanability.

### Scalar shorthand

Payload fields use scalar shorthand: the field name maps directly to its type as a string.

```yaml
event: OrderSubmitted
version: 1
producer: OrderService
consumers:
  - InventoryService
  - BillingService
  - NotificationService
payload:
  orderId: string
  customerId: string
  items: array
  total: number
  currency: string
description: Fired when a customer successfully submits an order through the checkout flow
since: 1
```

Valid type tokens for the scalar shorthand: `string`, `integer`, `number`, `boolean`, `array`, `object`. For nested structures (e.g., an array of objects with typed fields), the spec must be expanded into an explicit JSON Schema fragment under that payload key — the shorthand alone cannot express element types.

## Validation

`scripts/validate-event-contracts.sh` walks every `*.yaml` file in `docs/domain-architecture/events/` (excluding the `schemas/` subdirectory) and runs `check-jsonschema --schemafile <meta-schema> <spec>`. The script exits 0 if every spec validates, 1 otherwise. It prints a per-spec OK/FAIL line and, on failure, the validator's error output indented under the failing spec.

### Prerequisites

- `check-jsonschema` — install via `pip install check-jsonschema` (Python tool; no fallback)
- `bash` with `set -euo pipefail` semantics

### When to run

Run during sashay closure, after rebase and before merge. See `workflow-rituals/pr-sashay.md` for the full closure sequence.

### Drift detection

Not yet implemented. The script prints a placeholder message after validation:

```
=== Drift detection not yet implemented ===
Future: scan implementation code for event classes/structs and compare field names/types.
```

When drift detection ships, it will scan producer/consumer code for matching classes or structs and compare field names/types against the spec. Until then, validation against the meta-schema is the only machine-checkable guard.

## Writing a new event spec

1. Create `docs/domain-architecture/events/<event-name>.yaml` using the scalar shorthand pattern above.
2. Set `version: 1` and `since: 1` for a new event. When revising, bump `version` and leave `since` unchanged.
3. Marking an event as `deprecated` requires setting `deprecated: <version>` and keeping the spec in place; do not delete deprecated specs — consumers may still be on the old version.
4. Run `scripts/validate-event-contracts.sh` locally before opening the PR. CI runs the same script during sashay closure.

## Triggers

`event contract`, `event spec`, `data contract`.
