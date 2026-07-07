---
slug: test-driven-design
title: Test-Driven Design
type: local
path: ~/.agents/agents-md-detail/test-driven-design.md
fetched: 2026-07-07T14:00:00Z
---

# Test-Driven Design

This document uses RFC 2119 key words (MUST, MUST NOT, SHOULD, SHOULD NOT, MAY) to indicate requirement levels. Lowercase equivalents are not RFC 2119 and carry no normative weight.

## Forward-Pointers

This document defines the universal test strategy. The pre-test inventory (see below) classifies each workflow path against six universal risk categories. Depending on which categories fire for your project, you MUST also read additional spokes for comprehensive coverage:

| Category | If any path touches this... | Forward-pointer |
|----------|---------------------------|-----------------|
| **Safety** | Human life, physical harm, critical infrastructure | `~/.agents/agents-md-detail/safety-critical-testing.md` |
| **Security** | Auth boundaries, encryption, secrets, network perimeters | `~/.agents/agents-md-detail/agent-testing.md` (adversarial section) |
| **Financial** | Money, billing, payments, pricing | `~/.agents/agents-md-detail/test-driven-design.md` (mutation testing + PBT sections) |
| **Privacy** | PII, GDPR, HIPAA, user-generated data | `~/.agents/agents-md-detail/operator-assisted-e2e.md` (credential handling) |
| **Availability** | SLA, uptime, failover, disaster recovery | `~/.agents/agents-md-detail/two-stage-pipelines.md` |
| **Integrity** | Data correctness, idempotency, consistency | `~/.agents/agents-md-detail/event-contracts.md` |

The categories are universal; the thresholds are project-specific. A "high" safety risk in a homelab (e.g., a fan controller) is different from a "high" safety risk in a medical device, but both MUST classify the path against the same category.

If no category fires for any path in your project, this document alone is sufficient.

## Core Principle

Whenever you fix or work around an error in a project with a codebase, make sure to add a test covering that error.

## Test-First for Defects (FIRST RULE)

When you encounter a runtime error, bug, or unexpected failure — whether reported by the user, discovered during development, or surfaced by a tool — your **first action** is to write a test that reproduces the failure. Only then do you implement the fix. The test MUST fail before the fix and pass after it.

This is not optional. It applies to every code change, not just explicit test requests.

### Catch yourself before you fix

The most common failure mode: the agent diagnoses a bug and jumps straight to implementation, skipping the test that would prove the fix works. This produces correct fixes that are not protected against regression.

Recognize the pattern. If you catch yourself writing any of these phrases — in your thinking, in a user-facing response, or in a commit message — STOP and write the failing test first:

- "The fix is to add X"
- "I'll add X to fix this"
- "I'll patch Y"
- "I'll change Z"
- "I'll update Y to resolve this"
- "I need to change Z"
- "To resolve this, I need to..."
- "The solution is..."
- "Root cause is..."
- "The issue is..."
- "The problem is..."
- "Missing: X"
- "Doesn't exist: X"
- "Should be X"

The pattern is: **diagnosis followed by implementation intent, with no test in between.** The test comes before the implementation, always.

> **A test failure is a test failure; you either fix it or decide it's testing the wrong thing.**

**No rationalizing test failures.** A test failure is a test failure. You MUST NOT treat a failing test as "signal," "evidence," "indication," or any other euphemism for an unresolved problem. You either fix it or consciously decide it's testing the wrong thing — there is no third option. Common rationalization patterns to catch yourself on: "X/Y passing is evidence the fix worked for the original failure mode," "case Z is a separate issue," "below threshold," "accept X/Y as signal," "iterate on the prompt instead of fixing the test."

## RGR: Tests Always Run

Tests MUST always execute. A test that cannot run due to missing prerequisites is a **Red** — not a skip, not a deferral, not a "will fix later." The RGR (Red-Green-Refactor) cycle applies to the test execution itself: if the test can't run, the result is Red.

This means:

1. **Missing prereqs = Red.** If a test requires a service, credential, binary, or environment that isn't available, the test fails. The failure is real — it documents a gap in the test infrastructure or environment setup. Silent skips (e.g., `@pytest.mark.skipif` without a documented reason, conditional test discovery) are forbidden.

2. **If the fix is too large for the current session,** the Red is still recorded. The operator MUST:
   - Document the missing prereq as tech debt (entry in `docs/tech-debt/`).
   - Create a sashay or plan to resolve it.
   - Mark the current test pass as **FAILED** — not "passed with exceptions," not "mostly green." A failed test pass is the signal that something needs attention.

3. **No silent fallbacks.** A test that conditionally skips itself (e.g., "skip if no API key") without logging the skip reason is a silent failure. The test MUST either run and pass, run and fail, or explicitly report why it cannot run. "Fail Loud; Never Coerce" applies to test infrastructure as much as to application code.

This is not about test quality — it is about test **honesty**. A suite that reports green while skipping tests is lying. RGR demands truth in test results.

## Inverse-Assertion Requirement

Every test suite MUST include at least one test where the expected outcome is a **failure** — an exception, an error type, a rejected action, a blocked operation. A suite containing only happy-path assertions is incomplete. If you encounter such a suite:

1. Flag it explicitly and ask whether edge cases were accidentally omitted.
2. Never write tests that only confirm the implementation path you already took. The point is to find the cases the implementation didn't handle.

This applies regardless of language or framework. An untested failure path is a latent bug.

## Required Test Coverage

A good project has coverage across **all** of these test types. None are optional.
Each type is labelled by timing — write it **pre-implementation** (red-green-refactor:
write failing test first, then implement) when the contract can be defined before
the code exists, or **post-implementation** when the system MUST exist first.

| Test type | Timing | Rationale |
|-----------|--------|-----------|
| Unit tests | **Pre** (RGR) | Contract for one function is trivial to define before writing it. |
| Integration tests | **Pre** (RGR) | Interface contracts between components can be specified ahead of wiring them up. |
| Behavioral tests | **Pre** (RGR) | BDD scenarios define what the system SHOULD do before it does it. |
| Automated UAT | **Pre** (RGR) | Acceptance criteria define what "done" looks like before implementation starts. |
| Smoke tests | **Post** | Needs the build artifact to exist; verifies deployment/canary readiness. |
| End-to-end tests | **Post** | Needs the full system assembled — can't test a flow that doesn't exist yet. |
| Adversarial tests | **Post** | Explores edge cases and failure modes of an existing implementation. |
| Red-team tests | **Post** | Attempts to break an existing system's security/posture assumptions. |
| Penetration tests | **Post** | Exploits an existing system; always check with operator before running. |

## Pre-test Inventory

Before running any test suite, there MUST be a pre-test inventory — a
coverage matrix that maps workflow paths to coverage classes. This is a
gate, not advisory. The sashay refuses to run tests at step 7 if the
inventory is absent or stale (see `workflow-rituals/pr-sashay.md` step 7).

### Two-tier model: Master Test Plan + Level Test Plan

The inventory is two artifacts, following the IEEE 829 / ISTQB
terminology (see trove `docs/troves/master-test-plan-level-test-plan/`):

1. **Master Test Plan (MTP)** — a living, project-level coverage matrix
   listing all workflow paths × coverage classes × project-calibrated
   blast-radius weights. This is the regression surface. It changes only
   when requirements or test levels change — not per-sashay. The MTP is
   the project's authoritative record of what needs testing.

2. **Level Test Plan (LTP)** — a per-sashay delta identifying which
   workflow paths the current change affects, with **change-relative
   weights**. Every new or modified path is high priority regardless of
   its master weight — untested new code is untested new code. The delta
   provides focus; the master provides the full surface. The delta rolls
   up into the master after the sashay completes.

### Matrix shape: 3-dimensional

The coverage matrix is 3-dimensional:

| Dimension | Values | Source |
|-----------|--------|--------|
| **Rows** | Workflow paths (user-facing flows through the system) | Derived from the codebase — routes, CLI subcommands, state machines, API endpoints |
| **Columns** | Coverage classes: Happy / Sad / Edge / Corner | getautonoma taxonomy (see trove `docs/troves/e2e-test-development-practices/`) |
| **Cell weight** | Blast-radius priority | Binary gate (see below) + optional project-level refinement |

**Coverage class definitions** (from the getautonoma taxonomy):

- **Happy path** — single default flow, every step succeeds.
- **Sad path** — anticipated failures: auth fails, validation rejects,
  500, out of stock. Bugs live in error-handling code.
- **Edge case** — boundary inputs: off-by-one, empty lists, max-length,
  zero-value, first/last of paginated set. May pass through general code
  silently wrong.
- **Corner case** — multiple low-probability conditions simultaneously.
  Combinatorial, hard to enumerate, caught by exploratory testing.

### Blast-radius gate: binary

The global rule is binary, driven by the six universal risk categories defined in the Forward-Pointers section:

> Does this path touch **Safety**, **Security**, **Financial**, **Privacy**, **Availability**, or **Integrity**?

Examples of what "touches" means per category:
- **Safety** — human life, physical harm, critical infrastructure
- **Security** — auth boundaries, encryption, secrets, network perimeters
- **Financial** — money, billing, payments, pricing
- **Privacy** — PII, GDPR, HIPAA, user-generated data
- **Availability** — SLA, uptime, failover, disaster recovery
- **Integrity** — data correctness, idempotency, consistency

- **Yes** → beyond-happy-path coverage (Sad + Edge + Corner) is REQUIRED.
- **No** → happy-path coverage is sufficient.

This is a gate, not a scoring exercise. Projects MAY adopt a 4-level
weight (red/yellow/green/gray) with a 0-10 reliability score as an opt-in
refinement in their AGENTS.md (from the failfastmoveon Agile Coverage
Matrix), but the global rule is the binary gate.

### Location and declaration

The master coverage matrix lives at a project-declared path, declared
in the project's AGENTS.md under a `## Test coverage matrix` section
(parallel to `## Test command`). Default location:
`docs/test-coverage-matrix.yaml` (or `docs/test-coverage-matrix.md`).

Example AGENTS.md declaration:

```markdown
## Test coverage matrix

Master coverage matrix: `docs/test-coverage-matrix.yaml`
```

The sashay discovers the matrix by searching upward from the changed
code's directory, same walk as the test command discovery.

### Per-sashay delta procedure

At each sashay, the orchestrator (or subagent) produces a delta:

1. **Walk the diff** — identify which workflow paths the current change
   affects (new paths, modified paths, deleted paths).
2. **Assign change-relative weights** — every new or modified path is
   high priority regardless of its master weight. Untested new code is
   untested new code.
3. **Self-healing** — if the delta contains a path absent from the
   master, add it to the master (fail-loud at step 7 if the sashay
   completes without adding it). If the master contains a path that no
   longer exists in the codebase, prune it (fail-loud at step 7 if the
   master references deleted paths).
4. **Blast-radius review** — if the diff changed a path's blast radius
   (e.g., a path that previously didn't touch money now does), the
   change-relative weight signals the operator to review and update the
   master weight.

### Manual paths

Operator-assisted / manual paths (e.g., fresh install, idempotent
re-run, upgrade) get full Happy/Sad/Edge/Corner cells with `manual` as
the test mechanism — not a separate section. The E2E exemption exempts
from automated E2E tooling, not from thinking about what needs
verification. A fresh install touches session state and user data → needs
beyond-happy-path coverage even if the verification is a manual
checklist.

### Relationship to the Required Test Coverage table

The existing Required Test Coverage table (unit / integration /
behavioral / UAT / smoke / E2E / adversarial / red-team / pen) is the
**Test Strategy parent** — a taxonomy of test levels. The master coverage
matrix is the **Master Test Plan** that instantiates which levels apply
to which workflow paths, with Happy/Sad/Edge/Corner cells. Path name is
sufficient traceability; explicit RTM-style requirement-ID columns are
optional (aspirational for projects with EPICs/ADRs, not a gate
requirement).

### Drift prevention

The sashay delta is the **primary drift detector**:

- At delta creation, walk the codebase to identify affected paths.
- If a master path no longer exists in code, prune it (fail-loud at
  step 7 if master references deleted paths).
- If a diff changed a path's blast radius, the change-relative weight
  signals the operator to review the master weight.

**Residual risk:** paths never touched by a sashay rot silently. The
spoke documents *how* to conduct a full audit (walk master against
codebase, prune dead paths, recalibrate weights) — same cadence as
plans/musings cleanup (operator-triggered or sashay-closure-adjacent).
No periodic cadence is mandated.

### Format

The master coverage matrix MAY be YAML or markdown table. YAML is
preferred for machine-parseability; markdown is acceptable for small
projects.

**YAML example:**

```yaml
workflow_paths:
  - path: "POST /api/auth/login"
    blast_radius: high  # touches session state
    coverage:
      happy: automated  # pytest
      sad: automated    # pytest (invalid credentials, expired token)
      edge: automated   # pytest (empty password, max-length, sql injection)
      corner: manual     # expired token + concurrent login from new device
  - path: "GET /api/profile"
    blast_radius: low   # read-only, no session mutation
    coverage:
      happy: automated
      sad: automated    # 404 for unknown user
      edge: manual       # pagination boundary
      corner: skip       # read-only, low risk
  - path: "CLI: bootstrap.sh fresh install"
    blast_radius: high  # touches session state + user data
    coverage:
      happy: manual      # operator-assisted bootstrap
      sad: manual         # missing TCC permissions
      edge: manual        # re-run over existing install
      corner: manual      # upgrade from previous version
```

**Markdown table example:**

```markdown
| Workflow path              | Blast radius | Happy | Sad    | Edge   | Corner |
|----------------------------|-------------|-------|--------|--------|--------|
| POST /api/auth/login       | high        | auto  | auto   | auto   | manual |
| GET /api/profile           | low         | auto  | auto   | manual | skip   |
| CLI: bootstrap.sh fresh    | high        | manual| manual | manual | manual |
```

## Automated UAT Protocol

Before declaring any task complete, you MUST:

1. Run the full automated test suite for the project. All MUST pass.
2. Never manually test in a shell what can be automated as a test case. If you find yourself running CLI commands to verify behavior, write a test instead.
3. If a manual UAT reveals a bug, you MUST add an automated test for that bug BEFORE fixing it, then verify the fix makes the test pass.

## E2E Testing Protocol

End-to-end tests MUST be automated and fast:

1. E2E tests MUST be runnable with a single command alongside the rest of the suite (e.g., `go test ./...`, `pytest`, `cargo test`, `npm test`). If the flow has unavoidable manual steps, use the Operator-Assisted E2E Protocol below instead of skipping the test.
2. For CLI/binary projects: E2E tests SHOULD build the artifact once (shared build helper, test fixture, or build step) and test subcommands by invoking the built artifact. Do NOT build per-test.
3. E2E tests MUST cover the complete user-facing flow: initialization, primary operations, output verification, and cleanup/idempotency.
4. If you cannot write an E2E test in the project's primary test framework, write it as a shell script in a `test/` directory with a runner that the CI/suite command executes.

### Operator-Assisted E2E / UAT Protocol

When an E2E or UAT flow has unavoidable manual steps — OAuth flows, hardware interaction, credentials only the operator holds — do not skip the test. Use the operator-assisted protocol instead.

**When to use it.** The protocol applies when the test requires something the agent cannot do: browser-based OAuth consent screens, physical device interaction (YubiKey, smart card), or secrets the agent MUST never hold. If the manual step can be eliminated by a mock, service principal, or test credential, eliminate it first.

**Batching.** Batch all manual steps into a single operator interaction window. The operator SHOULD NOT be interrupted multiple times. Structure the test so that automated setup runs first, then the operator performs all manual actions in one sitting, then automated verification and cleanup run after.

**Full protocol.** The complete protocol — YAML test suite format, state machine, interactive execution, report generation, evidence collection, and retest loop — is defined in the operator-assisted-e2e spoke. Load it before executing any operator-assisted test suite.

Full reference: `~/.agents/agents-md-detail/operator-assisted-e2e.md`.

## Non-Deterministic Testing

E2E, smoke, and UAT tests may need to evaluate visual snapshots, semantic content, or subjective correctness — tasks that are fragile with regex and well-suited to LLM judgement. This section defines when and how to use non-deterministic testing.

### Metric Definitions

This document uses **pass^k** (not pass@k) as the primary reliability metric, following Anthropic's τ-Bench convention.

| Metric | Definition | What it measures |
|--------|------------|-----------------|
| **pass^1** | Per-trial success rate (proportion of individual trials that pass) | Single-attempt reliability. Trivially < 1 for any non-perfect system. |
| **pass^k** (k > 1) | Probability that **all** k independent trials succeed | **Consistency** — how often the agent gets it right every time across multiple attempts. This is the primary metric. |
| **pass@k** | Probability that **at least one** of k trials succeeds | Best-of-k capability. NOT used as a primary metric — it masks inconsistency. |

**Relationship:** If per-trial success rate is p, then pass^k = p^k. pass^1 = p. pass@k = 1 - (1-p)^k.

**Example:** p = 0.81 → pass^1 = 0.81, pass^5 = 0.81^5 ≈ 0.34, pass@5 = 1 - 0.19^5 ≈ 0.9998. pass@5 looks near-perfect while pass^5 reveals the agent fails 66% of 5-trial blocks.

### Permitted `llm` CLI Use

E2E and smoke tests MAY invoke the `llm` CLI for AI-powered judgement calls. The `llm` call MUST return **structured output** (JSON or YAML) that a test script can parse as pass/fail/skip.

Example:

```json
{"result": "pass", "confidence": 0.95, "reason": "Homepage header matches expected brand styling"}
```

```json
{"result": "fail", "confidence": 0.82, "reason": "CTA button 'Get Started' is not visible above the fold"}
```

### Test-Type Spectrum

Non-determinism tolerance varies by test type. The table below defines which test categories may use non-deterministic methods:

| Test type | Non-deterministic permitted? | Rationale |
|-----------|------------------------------|-----------|
| Unit | No — fully deterministic | Contracts are exact; LLM judgement is unnecessary and introduces noise |
| Integration | Limited — deterministic preferred | Use `llm` only for integration contract verification (e.g., "does the response contain valid semantic meaning?") |
| E2E / Smoke | Yes — for visual, semantic, and subjective checks | Screenshot diffs, content alignment, UX flow correctness |
| UAT | Yes — most relaxed | Semantic correctness and behavioral alignment are the primary goal |

### Statistical Rigor

All non-deterministic tests MUST report the following measures:

- **pass^1**: per-trial success rate (proportion of individual trials that passed).
- **pass^k**: probability of passing all k runs (conjunction, computed as `(pass_count / k)^k`). This is the primary metric.
- **pass@k**: reported for reference only (computed as `1 - (1 - pass_count/k)^k`).
- **k = 5** is the recommended standard (matches Anthropic τ-Bench convention, better resolution). **k = 3** is the minimum floor for any statistical claim.
- Results MUST be logged in a structured format (JSON Lines) for CI trend analysis.

### Session-Enriched JSONL Format

Each test invocation MUST produce a session-enriched JSONL log. A session is one complete run of the harness (all cases, k runs each). The log contains three record types, keyed by a shared `session` UUID:

**Session header** (first record):

```jsonl
{"type":"session-start","session":"<uuid>","commit":"<sha>","model":"<model-id>","judge_model":"<model-id>","k":3,"corpus":"<path>","threshold":0.67,"hub_lines":170,"timestamp":"2026-06-22T01:33:53Z"}
```

**Case-run records** (k per case, with raw agent + judge responses for debugging):

```jsonl
{"type":"case-run","session":"<uuid>","test":"<case-id>","run":1,"k":3,"result":"pass","confidence":1.0,"agent_response":"{\"spokes\":[\"parley.md\"]}","judge_response":"{\"result\":\"pass\",\"confidence\":1.0}","timestamp":"2026-06-22T01:34:45Z"}
```

**Case-summary records** (one per case, with pass@k):

```jsonl
{"type":"case-summary","session":"<uuid>","test":"<case-id>","commit":"<sha>","model":"<model-id>","k":3,"pass":3,"pass@k":1.0,"pass^k":1.0,"expected":["parley.md"],"timestamp":"2026-06-22T01:35:00Z"}
```

**Session footer** (last record):

```jsonl
{"type":"session-end","session":"<uuid>","total_cases":11,"total_pass":9,"suite_pass":false,"duration_sec":240,"timestamp":"2026-06-22T01:38:00Z"}
```

**Filename convention**: `test/<suite>/logs/<session-uuid>.jsonl`. A human-readable symlink MAY be created: `<commit>-<model-slug>.jsonl → <session-uuid>.jsonl`.

**Multi-session comparison**: Use `test/<suite>/compare.sh` to read multiple session logs and produce a per-case comparison table across models/commits. See `test/agents-hub/compare.sh` for a reference implementation.

### Legacy Log Format (superseded)

The prior minimal format logged only case summaries without session grouping:

```jsonl
{"test": "homepage-visual", "k": 3, "pass": 2, "pass@k": 0.667, "pass^k": 0.296, "timestamp": "2026-06-20T14:00:00Z"}
```

This format is superseded by session-enriched JSONL. Existing logs MAY be migrated by wrapping them in session-start/session-end records with a synthetic UUID.

### Ratio Guidance

The expected ratio of deterministic to non-deterministic tests SHOULD follow these conventions:

| Test type | Deterministic : Non-deterministic |
|-----------|-----------------------------------|
| Unit | 100 : 0 |
| Integration | ≥ 90 : 10 |
| E2E / Smoke | ≥ 70 : 30 |
| UAT | ≥ 50 : 50 |

These are review-time signals, not build-time constraints. A code review SHOULD flag significant deviation but MUST NOT block on it. The operator may accept a lower ratio when the test type warrants it (e.g., a UAT suite that is entirely LLM-judged for semantic correctness).

### Structured Output Contract

`llm`-backed test assertions MUST return JSON with at least these fields:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `result` | string | yes | `"pass"`, `"fail"`, or `"skip"` |
| `reason` | string | yes | Human-readable explanation of the judgement |
| `confidence` | number | no | Confidence score 0.0–1.0 |

The test runner reads `result` to decide pass/fail and logs `reason` and `confidence` for debugging and trend analysis.

## Test Tiers

Tests have different prerequisites. A unit test needs nothing. An integration test may need a local database. A staging E2E test needs a deployed instance. Running all of them with a single command forces projects to either skip staging tests (silent skip) or fail when no staging deploy exists.

The solution is a **test tier system** that separates tests by prerequisite. Each tier has its own command and its own run condition.

### Tier definitions

| Tier | Label | Prerequisite | Runs when | Declared as |
|------|-------|-------------|-----------|-------------|
| 0 | Unit / lint | None | Always | `## Test command` |
| 1 | Integration | Local services (DB, API, etc.) | Always (local) | `## Test command (integration)` |
| 2 | Staging E2E | Staging deploy exists | Only during sashay closure step 9 | `## Test command (staging)` |

Tier 0 is the default `## Test command` — always runs, no prerequisites. Tier 1 (integration) runs locally but may need services the developer has running. Tier 2 (staging) only runs during the sashay's staging deploy phase — never during local development or CI without a deploy.

### Declaration

Projects declare tiers in their AGENTS.md. Only declared tiers are run. Undeclared tiers are not expected to exist.

```
## Test command
pytest tests/unit/ -x -v

## Test command (integration)
pytest tests/integration/ -x -v

## Test command (staging)
pytest tests/e2e/ -x -v
```

The sashay runs:
- `## Test command` — always, at step 7 (pre-merge validation).
- `## Test command (integration)` — always, at step 7, after tier 0 passes.
- `## Test command (staging)` — only during staging deploy + E2E (step 9), invoked via `scripts/staging/e2e.sh`.

If a tier is not declared, the sashay does not look for it. No silent skip — the absence of a declaration is the explicit signal that the tier does not exist.

### Relationship to staging scripts

Tier 2 (staging) is the bridge between the test-driven-design spoke and the two-stage-pipelines spoke. The `scripts/staging/e2e.sh` script invokes the tier 2 command against the deployed staging instance. The tier 2 command is the test command; `e2e.sh` is the wrapper that provides the staging URL as context.

### Placement

Tier declarations follow the same discovery rules as the base test command: the sashay searches upward from the changed code's directory. The first `## Test command` (and its tier variants) found wins.

## Test Command Declaration

Every project MUST declare at minimum a tier 0 test command in its AGENTS.md under a `## Test command` section. This is the command that runs the always-available test suite. The sashay protocol uses this declaration to discover and run tests. Without it, the sashay cannot proceed past step 7.

### Placement

The test command goes in the AGENTS.md **nearest to the code being tested**:

- **Single-project repos:** the project-root `AGENTS.md`.
- **Monorepos / multi-project repos:** the sub-project's own `AGENTS.md` (e.g., `packages/foo/AGENTS.md`). The root `AGENTS.md` MAY omit a test command if it delegates to sub-projects.

The sashay discovers the test command by searching upward from the directory containing the changed code: first the directory itself, then its parents, up to the project root. The first `## Test command` found wins. If none is found, the sashay fails.

Examples:

```
## Test command
pytest tests/unit/
```

```
## Test command
go test ./... -short
```

```
## Test command
npm test -- --run
```

If a project genuinely cannot have automated tests (e.g., a pure documentation repo, a config-only repo), it MUST declare an exemption:

```
## Test command exemption
This is a pure documentation repository. Validation is handled by markdownlint in CI.
```

### Exemption declarations for other test types

Projects that cannot support certain test types must declare exemptions in their AGENTS.md. The sashay checks for these before failing on missing test infrastructure.

| Test type | Exemption key | Example |
|-----------|--------------|---------|
| Operator-assisted E2E | `## Operator-assisted E2E exemption` | `This project has no manual-interaction flows. All E2E is fully automated.` |
| Staging deploy + E2E | `scripts/staging/README.md` | Document why no staging target exists (see two-stage-pipelines.md). |

Without either the test infrastructure or a documented exemption, the sashay fails at the corresponding step. Silent skips are forbidden — this is a direct application of "Fail Loud; Never Coerce."

## Speed Guidelines

- Build steps that are expensive (compilation, bundling, container builds) MUST happen once per test run, not once per test. Use shared fixtures, `TestMain`, setup functions, or Makefile targets.
- Independent test cases SHOULD run in parallel where the framework supports it (e.g., `t.Parallel()`, `pytest-xdist`, `--parallel`).
- If the full suite takes >60s, flag it in your completion message and suggest where to optimize.

## Language-Specific Patterns

### Go

- E2E tests: use a `buildBinary(t *testing.T)` helper that builds once and caches via `sync.Once` or a package-level variable. Test subcommands with `exec.Command`.
- Run: `go test ./...`
- Integration tests live in `test/integration/`, safety/adversarial in `test/safety/`, smoke in `test/smoke/`. All are picked up by `go test ./...`.

### Python

- E2E tests: use `pytest` with `conftest.py` fixtures to start servers or build artifacts. `subprocess.run()` for CLI testing.
- Run: `pytest` or `pytest -x -v`

### TypeScript / Node

- E2E tests: use `vitest` or `jest` with `beforeAll` to build/start. `child_process.execSync` for CLI testing.
- Run: `npm test` or `pnpm test`

### Rust

- E2E tests: use `[[test]]` in `Cargo.toml` with `tests/` directory. Build once with `cargo build`, test with `std::process::Command`.
- Run: `cargo check` **and** `cargo test`. Both MUST pass before declaring work complete — type-checking alone is insufficient.

### C# / .NET

- E2E tests: use xUnit with `IClassFixture` for shared build. `Process.Start()` for CLI testing.
- Run: `dotnet test`

### Shell scripts

- E2E tests: use `bats-core` or `shelltestrunner`. Source functions, test exit codes and output.
- Run: `bats test/` or `shelltest`

## Triggers

User symptoms: `error`, `bug`, `failure`, `broken`, `crash`, `doesn't work`, `not working`, `fails`, `failed`, `runtime error`, `Traceback`, `Exception`, `AssertionError`, `panic`, `fatal`.

Agent diagnostic / fix-intent language: `the fix is`, `I'll add`, `I'll change`, `I'll patch`, `I'll update`, `I'll fix`, `I'll modify`, `I'll replace`, `I need to add`, `I need to change`, `I need to fix`, `to resolve this`, `the solution is`, `root cause`, `the issue is`, `the problem is`, `missing`, `doesn't exist`, `should be`.

Wrong-output / regression language: `regression`, `wrong output`, `incorrect`, `unexpected result`, `not supposed to`, `used to work`.

Test-suite context: `test`, `testing`, `coverage`, `UAT`, `E2E`, `pytest`, `go test`, `cargo test`, `vitest`, `bats`, `test suite`, `suite requirements`, `test command`, `test command exemption`, `operator-assisted E2E exemption`, `staging exemption`.

Non-deterministic test context: `llm`, `pass@k`, `pass^k`, `non-deterministic`, `semantic test`, `visual test`.

Pre-test inventory context: `pre-test inventory`, `coverage matrix`, `test coverage matrix`, `master test plan`, `level test plan`, `MTP`, `LTP`, `workflow path`, `blast radius`, `happy path`, `sad path`, `edge case`, `corner case`.
