---
slug: two-stage-pipelines
title: Two-Stage Pipelines for All Code Projects
type: local
path: ~/.agents/agents-md-detail/two-stage-pipelines.md
fetched: 2026-07-07T14:00:00Z
---

# Two-Stage Pipelines for All Code Projects

Every code project MUST have at least a 2-stage deployment pipeline: a staging target and a production target. This applies to webapps, CLIs, services, libraries, and everything in between.

## Rationale

- **PR sashays need isolated E2E testing.** When a sashay workflow processes a pull request, it must be able to spin up a staging instance from that branch, run E2E tests against it, and tear it down — all without touching the production target.
- **Production isolation.** Mistakes and experiments belong in staging. Without a staging target, validation happens against prod or a non-representative local dev environment.
- **Universal applicability.** CLIs get staged releases (`npm publish --tag next`, brew test taps). Services get staged deployments to a separate namespace or cluster. Libraries get pre-release tags (`-rc.N`, `-alpha`). Every artifact has a "release candidate" before "release."

## Measures, not means

This contract defines **measures**, not means. Scripts MUST produce the specified signals. How they produce them is the project's business.

The contract is a **measurement surface** — it defines the signals the sashay consumes to make decisions. The project is the **implementation surface** — it produces those signals however it needs to.

| Contract concern | Measure (MUST) | Means (project-owned) |
|---|---|---|
| Setup | Exit 0/non-zero, pass/fail + item name on stdout | Checklist format, JSON, dashboards |
| Budget | Deterministic reconciliation against provider state, exit 0/non-zero | Ledger format, budget file schema, provider API calls |
| Sanitization | Canonical function produces deterministic RFC 1123 output | (N/A — this one is a means, shipped centrally at `~/.agents/scripts/slugify-branch` to prevent drift) |
| Observability | Fitness functions (z-score, 3σ threshold, cold-start defaults) | Data format, instrumentation, baseline storage |
| E2E | Project-local spoke documents regressions guarded and test methods | Test implementation, environment-specific concerns |
| Teardown | Production safety guards (name check + "staging" substring) | Resource naming convention, cloud provider API calls |
| Deploy | Branch name as `$1`, staging URL to stdout | Provisioning method, config rendering, DNS setup |

The only exception is the branch name slugifier (`~/.agents/scripts/slugify-branch`), which is a means shipped centrally because drift across projects is worse than central coupling.

## What each project must provide

Every project MUST expose scripts at well-known paths that the sashay can invoke. The convention is predictable filenames under `<project-root>/scripts/staging/`:

| Script | Required? | Purpose |
|--------|-----------|---------|
| `scripts/staging/setup.sh` | Required | Credential caching, environment validation, budget gate. Accepts branch name as `$1`. Exits 0 on pass, non-zero on failure. |
| `scripts/staging/deploy.sh` | Required | Deploy current branch to staging. Accepts branch name as `$1`. Produces a staging URL or artifact identifier printed to stdout. |
| `scripts/staging/e2e.sh` | Required | Run E2E tests against the staging instance. Accepts a staging URL or identifier as `$1`. Exits 0 on pass, non-zero on failure. |
| `scripts/staging/teardown.sh` | Required | Destroy the staging instance. Accepts branch name as `$1`. MUST include production safety guards. |

### `setup.sh` output contract

`setup.sh` MUST produce pass/fail status and item name for each check on stdout. It MUST exit 0 on all-pass and non-zero on any failure.

It SHOULD use checklist format with `[✓]`/`[✗]` prefixes.

It MAY evolve the format (add timing, resource counts, structured fields) as long as the minimum information — pass/fail + item name — is preserved. The format can grow richer; it cannot grow weaker. Examples of richer evolution: `[✓] credentials cached (1.2s, 3/3 items)`, `[✓] budget check: $2.34/$5.00 remaining this week`, or a JSON-line format like `{"status":"pass","item":"credentials","duration_ms":1200}` — as long as pass/fail and item name are present. The contract defines the floor, not the ceiling.

### Teardown production safety guards

`teardown.sh` MUST include two independent production safety checks before destroying any resource:

1. Computed resource names MUST NOT match known production resource names.
2. Computed resource names MUST contain the literal string `staging`.

Both checks MUST pass before any resource is destroyed. If either check fails, the script MUST exit non-zero with a clear error message and MUST NOT destroy any resources.

### Branch name sanitization

Branch names MUST be sanitized before use as cloud resource identifiers. The canonical slugify tool is at `~/.agents/scripts/slugify-branch`. Agents call or source it; projects do not copy it.

The tool follows **RFC 1123 label** rules (the standard for DNS labels, Kubernetes names, Docker tags, and most cloud resource names):
- Lowercase alphanumeric + `-` only
- MUST start and end with an alphanumeric character
- Max 63 characters

Provider-friendly refinements:
- Replaces `/` and `_` with `-`
- Collapses consecutive dashes into a single dash
- Exits with error if the result is empty

### Cost budget

Every project with cloud resources MUST implement a cost budget gate. The contract does NOT prescribe a budget or ledger schema — the sashay only checks `setup.sh`'s exit code.

`setup.sh` MUST query the cloud provider for actually-running resources, correct the ledger to match reality, then compute budget from open entries. The ledger is never trusted on its own. Reconciliation MUST be deterministic (same inputs → same result).

Provider-specific reconciliation logic lives in `scripts/staging/lib/reconcile.sh`. The budget declaration lives in `scripts/staging/budget.yaml` (project-owned format).

If the budget is exceeded, `setup.sh` MUST exit non-zero with a clear message. The sashay aborts the deploy.

### Project-local E2E spoke

Every project MUST create `.agents/agents-md-detail/staging-e2e.md` with a hub reference (trigger line) in the project's AGENTS.md. This spoke covers:

- What regressions the staging pipeline guards against
- How E2E tests exercise those regressions
- Environment-specific concerns (Let's Encrypt staging CA, rate limits, etc.)
- What "pass" and "fail" mean for each test

The E2E tests themselves are declared as tier 2 in the project's AGENTS.md (`## Test command (staging)`). The `e2e.sh` script invokes the tier 2 command against the deployed staging instance. See `test-driven-design.md` (Test Tiers section) for the full declaration format.

### For projects that cannot deploy to an external target

Some projects (purely local tools, embedded software, single-binary CLIs with no server component) genuinely cannot have a deployable staging instance. In that case, provide a `scripts/staging/README.md` explaining why no staging target exists and what alternative validation the project uses (e.g., prerelease version tags, container image builds, etc.).

### Branch-based staging identity

The staging instance is identified by the branch name. The deploy script creates or updates a staging target scoped to that branch. The sashay records the branch name, so the staging URL/identifier flows naturally through the closure loop.

## How the sashay consumes these scripts

The sashay closure loop (step 9, staging deploy + E2E) is extended with a staging sub-phase:

1. Invoke `scripts/staging/setup.sh <branch>` — credential caching, environment validation, budget gate. If it exits non-zero, abort the deploy.
2. Invoke `scripts/staging/deploy.sh <branch>`.
3. Read the staging URL from stdout.
4. Invoke `scripts/staging/e2e.sh <staging-url>`.
5. If E2E tests fail, dispatch a fix subagent. Loop back to rebase (the fix likely needs code changes, not just test changes).
6. Before merging, invoke `scripts/staging/teardown.sh <branch>`.
7. Proceed with merge.

`setup.sh` always runs. No conditional. If the project has no staging scripts (noted by `scripts/staging/README.md` explaining why), the E2E staging step is skipped.

## SPC fitness functions

The sashay watches for anomalies using Statistical Process Control (SPC) — z-scores against a baseline of μ and σ from the last N successful staging runs. The default anomaly threshold is 3σ (the Western Electric / Nelson standard).

**Cold-start:** Before N runs exist, use hardcoded default thresholds. After N runs, the statistical baseline replaces them. Projects MAY override thresholds explicitly.

The SRE golden signals map to staging equivalents:

| Golden Signal | Staging equivalent | Formula | Default cold-start threshold |
|---|---|---|---|
| Latency | Deploy duration, time-to-healthy | z-score vs baseline | >600s deploy, >120s time-to-healthy |
| Traffic | Resource count provisioned | z-score vs baseline | ±50% from previous run |
| Errors | E2E failures, teardown leaving resources | count > 0 | >0 (binary) |
| Saturation | Cost accrual rate | z-score vs baseline | >2x previous run's cost rate |

## Convention: well-known paths

The staging scripts live at `<project-root>/scripts/staging/` because:

- `scripts/` is language-agnostic — it works for Python, Node, Go, Rust, shell, whatever.
- `staging/` scopes the namespace; deploy scripts for other environments (prod, canary) can sit alongside.
- Predictable names (`setup.sh`, `deploy.sh`, `e2e.sh`, `teardown.sh`) mean the sashay doesn't need a manifest file to discover them.

## New project scaffolding

Every new project scaffold MUST include a `scripts/staging/` directory. For projects that deploy (most projects), fill in real scripts or at minimum stub scripts with TODO comments. For projects that cannot deploy, include `scripts/staging/README.md` with the explanation.

## Related spokes

- `workflow-rituals/pr-sashay.md` — the sashay closure loop that chains into these scripts.
- `new-project-scaffolding.md` — adds the `scripts/staging/` directory to the scaffold.
- `locations.md` — documents the `scripts/staging/` convention as a project location.
- `~/.agents/scripts/slugify-branch` — canonical branch name slugifier (RFC 1123).

## Triggers

`two-stage pipeline`, `staging target`, `production target`, `staging deploy`, `e2e staging`, `pipeline requirement`.
