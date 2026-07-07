---
slug: "agents-hub"
title: "Global Agent Instructions (hub)"
type: local
path: "~/.agents/AGENTS.md"
fetched: 2026-07-07T14:00:00Z
---

# Global Agent Instructions

!!! IMPORTANT !!! AS AN AGENT YOU MUST ALWAYS FOLLOW THESE INSTRUCTIONS ON EVERY TURN

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

Before starting any task, scan the section headers below. If a section's trigger keywords match your task, load the referenced spoke file. The critical rules at the top of this file apply to every task.

**Watch your question shape during musing, parley, and jam sessions.** If the operator asks a binary question (yes/no, either/or, "should we"), suggest the open alternative.

**Navigation:** Sections use `Full reference:` for terminal spokes (load that file and act) and `Sub-hub index:` for intermediate indexes (load that file, then drill through to the specific spoke it links). You MUST NOT stop at a sub-hub — always follow links to the destination spoke.

## Context Map & Domain Model

Every project MUST keep a living Context Map and Domain Model under `docs/`.

Full reference: `~/.agents/agents-md-detail/context-map-domain-model.md`.

Trigger on: `context map`, `domain model`, `bounded context`, `DDD`, `domain-driven design`, `entity`, `value object`, `aggregate`, `ubiquitous language`, `system boundary`, `integration pattern`, `docs/context-map`, `docs/domain-model`, `docs/CONTEXT-MAP`, `docs/DOMAIN-MODEL`, `docs/domain-architecture`, `DOMAIN-EVENTS`, `domain events`, `cross-context events`, `integration events`, `zoom level`, `L1`, `L2`, `ERD`, `entity-relationship`.

## Technical Architecture

The `docs/` tree splits into two architectural surfaces: **domain** (DDD, ubiquitous language, context maps — see Context Map & Domain Model above) and **technical** (C4, deployment topology, tech stack). They have different audiences and evolve on different cadences — keep them in separate directories. `docs/ARCHITECTURE.md` is the technical surface hub; DDD does not live there.

Full reference: `~/.agents/agents-md-detail/new-project-scaffolding.md` (scaffolding mandates the split).

Trigger on: `technical-architecture`, `C4`, `tech stack`, `deployment topology`, `mount topology`, `container`, `component diagram`, `docs/ARCHITECTURE.md`, `docs/technical-architecture`.

## Mandatory Test Suite Standards

Any test suite you produce MUST include at least one test that expects a failure. Write the failure test BEFORE the fix. Run the full suite before declaring completion.

Full reference: `~/.agents/agents-md-detail/test-driven-design.md`.

Trigger on: `test`, `testing`, `coverage`, `UAT`, `E2E`, `pytest`, `go test`, `cargo test`, `vitest`, `bats`, `test suite`, `suite requirements`, `test command`, `test command exemption`, `operator-assisted E2E exemption`, `staging exemption`, `llm`, `pass@k`, `pass^k`, `non-deterministic`, `semantic test`, `visual test`, `pre-test inventory`, `coverage matrix`, `test coverage matrix`, `master test plan`, `level test plan`, `MTP`, `LTP`, `workflow path`, `blast radius`, `happy path`, `sad path`, `edge case`, `corner case`.

## Operator-Assisted E2E / UAT

Interactive test execution protocol for operator-assisted E2E and UAT. YAML test suites, state machine, interactive wizard, report generation.

Full reference: `~/.agents/agents-md-detail/operator-assisted-e2e.md`.

Trigger on: `operator-assisted`, `manual test`, `interactive test`, `UAT`, `operator e2e`, `walk me through`, `test suite`, `tests/manual/`.

## Debugging Ritual

When you encounter a runtime error, bug, or unexpected failure — from the user, from a tool, or from your own diagnosis — follow the 8-step Debugging Ritual: reproduce, write a failing test, isolate root cause, write additional edge/corner/inverse tests, implement the fix, verify all tests pass, run the full suite, commit with discipline. The tests MUST fail before the fix and MUST pass after it. This applies to all code changes, not just explicit test requests.

**Catch yourself before you fix.** If you catch yourself writing "The fix is to add X", "I'll patch Y", "To resolve this, I need to change Z", or any other fix-intent language, STOP. Write the failing test first. This is the most common failure mode: the agent diagnoses a bug and jumps straight to implementation, skipping the test that would prove the fix works.

> **A test failure is a test failure; you either fix it or decide it's testing the wrong thing.**

**No rationalizing test failures.** A test failure is a test failure. You MUST NOT treat a failing test as "signal," "evidence," "indication," or any other euphemism for an unresolved problem. You either fix it or consciously decide it's testing the wrong thing — there is no third option. Common rationalization patterns to catch yourself on: "X/Y passing is evidence the fix worked for the original failure mode," "case Z is a separate issue," "below threshold," "accept X/Y as signal," "iterate on the prompt instead of fixing the test."

Full reference: `~/.agents/agents-md-detail/debugging-ritual.md`.

Trigger on user symptoms: `error`, `bug`, `failure`, `broken`, `crash`, `doesn't work`, `not working`, `fails`, `failed`, `runtime error`, `Traceback`, `Exception`, `AssertionError`, `panic`, `fatal`.

Trigger on agent diagnostic/fix-intent language: `the fix is`, `I'll add`, `I'll change`, `I'll patch`, `I'll update`, `I'll fix`, `I'll modify`, `I'll replace`, `I need to add`, `I need to change`, `I need to fix`, `to resolve this`, `the solution is`, `root cause`, `the issue is`, `the problem is`, `missing`, `doesn't exist`, `should be`.

Trigger on wrong-output / regression language: `regression`, `wrong output`, `incorrect`, `unexpected result`, `not supposed to`, `used to work`.

## Test Command Declaration

Every project MUST declare its test command in an AGENTS.md under `## Test command`. In single-project repos this is the project-root AGENTS.md. In monorepos it goes in the sub-project's own AGENTS.md (nearest to the code being tested). The sashay discovers it by walking upward from the changed code's directory. Without a declaration, the sashay fails at step 7. Projects that cannot have automated tests must declare a `## Test command exemption` instead.

Full reference: `~/.agents/agents-md-detail/test-driven-design.md`.

Trigger on: `test command`, `how to run tests`, `test runner`, `test suite command`, `test command exemption`.

## Fail Loud; Never Coerce

You MUST reject bad input. You MUST surface missing data as errors. You MUST NOT silently fall back.

Full reference: `~/.agents/agents-md-detail/fail-loud-never-coerce.md`.

Trigger on: `coerce`, `fallback`, `default value`, `bare except`, `catch-all`, `swallow`, `silent failure`, `graceful degradation`.

## Warnings and Deprecations

You MUST NOT solve errors by making them less noisy. You MUST fix the root cause.

Full reference: `~/.agents/agents-md-detail/warnings-and-deprecations.md`.

Trigger on: `warning`, `deprecation`, `suppress`, `silence`, `noisy`, `ignore_errors`.

## Pre-existing LSP Errors

When working on a codebase that has pre-existing LSP errors (type errors, lint violations, etc.):

- **Minor/fast fixes** (single-file, low-risk, clearly correct): fix them in their own commit before the feature/fix commit. The commit message SHOULD start with `chore: fix pre-existing LSP errors in <area>`.
- **Significant/complex errors** (multi-file, risky, unclear root cause): you SHOULD add an entry to `docs/tech-debt/` instead. You MUST NOT fix them inline — that risks scope creep and introducing regressions.

Full reference: `~/.agents/agents-md-detail/lsp-errors.md`.

Trigger on: `LSP`, `type error`, `lint error`, `pre-existing`, `tech debt`, `tech-debt`, `docs/tech-debt/`, `pyright`, `tsconfig`, `gopls`, `rust-analyzer`, `venv`, `uv`.

## Todo List Discipline

Agents frequently fail `todowrite` by passing a JSON-stringified array instead of a native array. The tool expects `todos: Array<{content, status, priority}>` — a bare JSON array, never a string. You MUST NOT pass a stringified array.

Full reference (schema, common mistakes, status values): `~/.agents/agents-md-detail/todo-discipline.md`.

Trigger on: `todo`, `todowrite`, `TodoWrite`, `todo list`, `todo update failed`, `SchemaError.*todos`.

## ACTIONS ALWAYS REQUIRING AUTHORIZATION

Any `gh` action that triggers human processes MUST be explicitly authorized. This covers marking PR ready, requesting reviewers, merging, and closing issues.

## Mermaid.js Usage

You SHOULD use Mermaid.js first for diagrams. You MUST verify with `mmdc` before committing.

Full reference: `~/.agents/agents-md-detail/mermaid-usage.md`.

Trigger on: `mermaid`, `diagram`, `mmdc`, `flowchart`, `sequence diagram`, `graph`, `ASCII chart`, `box-drawing`, `ascii art`.

## Config Editing

When editing files under `~/.config/opencode/` or `~/.agents/`, follow hub-and-spoke conventions and resolve symlinks to the real target first. Sub-hub index: `~/.agents/agents-md-detail/config-editing.md`.

Trigger on: editing `AGENTS.md`, editing a spoke, adding a new rule, editing `~/.config/opencode/`, editing `~/.agents/`, `symlink`, `resolve symlink`, `config file edit`.

## Worktree Discipline

The root of a project MUST always be on `main`/`trunk`. All other branches MUST only live inside `.worktrees/`.

Full reference: `~/.agents/agents-md-detail/worktree-discipline.md`.

Trigger on: `worktree discipline`, `root branch`, `main only in root`, `pre-checkout hook`, `worktree isolation`, `SKIP_WORKTREE_CHECK`.

## Tool Usage

When a tool is reported as broken, you MUST NOT use that same tool to diagnose. You MUST echo the date and time before and after invoking a tool. Sub-hub index: `~/.agents/agents-md-detail/tool-usage.md`.

Trigger on: `op`, `shell`, `bash`, `web`, `python`, `git`, `gh`, `llm`, `playwright`, `WIP`, `draft PR`, `image analysis`, `screenshot evaluation`.

## Subagent Delegation

Choose subagent weight by task complexity. You MUST NOT use `heavy` unless explicitly marked.

Full reference: `~/.agents/agents-md-detail/subagent-delegation.md`.

Trigger on: `subagent`, `delegate`, `delegation`, `heavy`, `medium`, `light`, `sonnet`, `haiku`, `opus`, `kimi`, `minimax`.

## Locations

Personal code projects live in `~/Documents/code/`. Worktrees in `.worktrees/`. Musings under `docs/musings/`. Plans under `docs/plans/`. Sub-hub index: `~/.agents/agents-md-detail/locations.md`.

Trigger on: `worktree`, `personal code`, `musings`, `plans`, `docs/`, `staging`, `deploy`, `e2e`, `pipeline`, `scripts/staging/`.

## New Project Scaffolding

Seed new projects with full scaffold.

Full reference: `~/.agents/agents-md-detail/new-project-scaffolding.md`.

Trigger on: `new project`, `scaffold`, `init`, `AGENTS.md`, `git init`, `git-secrets`, `pre-commit`, `technical-architecture`, `domain-architecture`, `C4`, `bounded context`, `tech stack`, `ubiquitous language`.

## Memory (hub-and-spoke)

Agent memories documenting repeatable patterns. Spoke index: `~/.agents/memories/memory.md`.

## Vision Analysis Dispatch

When a non-vision model needs image work, shell out to `llm` with `kimi-k2.6:cloud`.

Full reference: `~/.agents/agents-md-detail/tool-usage/llm-vision-dispatch.md`.

Trigger on: `image analysis`, `screenshot evaluation`, `visual comparison`, `photo description`, `diagram review`, `look at this`, `-a`.

## Overnight Autonomy & Bedtime Nudging

After midnight US Eastern, shift to overnight mode.

Full reference: `~/.agents/agents-md-detail/overnight-autonomy.md`.

Trigger on: `midnight`, `overnight`, `bedtime`, `late`, `night shift`, `autonomous`, `scheduled`, `cron`, `launchd`, `systemd`.

## Two-Stage Pipelines

Every code project MUST have at least a 2-stage deployment pipeline: a staging target and a production target. Projects expose deploy/E2E/teardown scripts at `scripts/staging/` for the sashay to invoke during PR workflows.

Full reference: `~/.agents/agents-md-detail/two-stage-pipelines.md`.

Trigger on: `two-stage pipeline`, `staging target`, `production target`, `staging deploy`, `e2e staging`, `pipeline requirement`.

## Workflow Rituals

Named protocols for distinct phases: Sashay, Jam session, Parley, Retro, Make fair, Sitrep, Muse, EDA. Rituals are NOT skills. Sub-hub index: `~/.agents/agents-md-detail/workflow-rituals.md`. Trigger on: `sashay`, `jam`, `parley`, `retro`, `make fair`, `sitrep`, `muse`, `muse on`, `muse about`, `muse over`, `musings`, `eda`, `exploratory data analysis`.

## Event Contract Scaffolding

Event contracts use a meta-schema, YAML spec files, and drift detection.

Full reference: `~/.agents/agents-md-detail/event-contracts.md`.

Trigger on: `event contract`, `event spec`, `data contract`.

## Coding Discipline

Every code change MUST be grounded in three principles: follow the domain model and architecture, maintain a single authoritative source of truth, and climb the YAGNI ladder only with evidence.

Full reference: `~/.agents/agents-md-detail/coding-discipline.md`.

Trigger on: `domain model`, `architecture`, `source of truth`, `YAGNI`, `ponytail`, `duplication`, `DRY`, `single source of truth`, `canonical`, `bounded context`, `ubiquitous language`, `premature abstraction`, `over-engineering`, `over-generalization`.

<!-- cove-guidance start -->
## Cove

This machine runs Cove — a local developer platform (forge, vault, CI, registry, pages). All services are offline-first.

- **Forgejo** at `https://git.cove/` (TLS via nginx+mkcert). CLI: `fj` (see `~/.agents/agents-md-detail/fj.md`). PRs with `WIP:` title prefix are drafts and cannot be merged until the prefix is removed.
- **GitHub** CLI: `gh` (see `~/.agents/agents-md-detail/gh.md`). Use `gh pr ready` / `gh pr ready --undo` to toggle draft status.
- **Vault** at `https://vault.cove/` (TLS via nginx+mkcert). Use `cove creds vault-get` / `cove creds vault-put` — never hardcode secrets.
- **Prerequisites (macOS):** `brew install colima mkcert && colima start && mkcert -install`
- **Constraints:** No cloud dependencies. No internet during builds/CI. Cove's own git remotes go to Forgejo at `git.cove`.
- **New projects / Git remotes:** Use `origin` for your primary remote (e.g., GitHub, GitLab, or Cove itself). Add a secondary remote named after the forge: `fj` for Cove/Forgejo, `gh` for GitHub, `gl` for GitLab.

**Full reference:** `~/.agents/agents-md-detail/cove.md`
**Project override:** `.agents/cove/agents-md/cove.md` (if it exists, it augments/overrides the global doc for that repo)

When triggered by any Cove-related keyword (credentials, forgejo, pages, services), load the spoke doc above.
<!-- cove-guidance end -->
