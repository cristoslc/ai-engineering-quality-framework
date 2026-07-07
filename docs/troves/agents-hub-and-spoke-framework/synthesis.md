# Synthesis: `~/.agents/AGENTS.md` Hub-and-Spoke Framework

**Trove:** agents-hub-and-spoke-framework
**Created:** 2026-07-07
**Sources:** 21 (1 hub + 20 spokes)

---

## Key Findings

The `~/.agents/AGENTS.md` framework implements a comprehensive quality control system for AI-assisted software engineering, organized as a hub-and-spoke documentation architecture. The hub is a single routing table (224 lines) that maps trigger keywords to spoke files; each spoke contains detailed procedural rules for a specific quality dimension.

### 1. The Hub as a Routing and Governance Layer

The hub (`agents-hub`) defines the meta-protocol: scan trigger keywords, load the matching spoke, read it before acting, never act from memory alone. It also establishes RFC 2119 conventions (MUST/SHOULD/MAY) and a binary-question-avoidance rule for collaborative sessions. The Cove section is environment-specific (this machine's local developer platform) and not universal.

### 2. Test Discipline is the Strongest Quality Control

The `test-driven-design` spoke (583 lines — the longest) is the most comprehensive. It mandates:
- **Test-first for defects** — write failing test before any fix, with catch-yourself trigger-word detection
- **Inverse-assertion requirement** — every suite must include failure-expectation tests
- **9 required test types** — unit, integration, behavioral, UAT, smoke, E2E, adversarial, red-team, penetration
- **MTP/LTP two-tier coverage matrix** — Master Test Plan (living) + Level Test Plan (per-sashay delta) with 3D coverage (workflow paths × Happy/Sad/Edge/Corner × blast-radius weight)
- **Blast-radius binary gate** — paths touching Safety/Security/Financial/Privacy/Availability/Integrity require beyond-happy-path coverage
- **RGR cycle enforcement** — tests must always execute; missing prereqs = Red, not skip
- **Non-deterministic testing protocol** — pass^k metric, session-enriched JSONL, k=5 recommended
- **Test tier system** — Tier 0 (unit/lint), Tier 1 (integration), Tier 2 (staging E2E)

### 3. Behavioral Quality Controls (Novel Contribution)

The `debugging-ritual` and `fail-loud-never-coerce` spokes introduce quality controls that have no direct analogue in traditional software QA:
- **Catch-yourself-before-you-fix** — trigger-word detection to prevent skipping the test step
- **No rationalizing test failures** — test failure is a test failure, no euphemisms
- **Fail-loud principle** — reject bad input, surface missing data as errors, no silent fallbacks
- **Crash-over-corruption** — prefer loud failure over silent wrongness

These map conceptually to manufacturing's poka-yoke (mistake-proofing) and andon (stop-the-line) practices.

### 4. Deployment Safety via Two-Stage Pipelines

The `two-stage-pipelines` spoke mandates staging + production targets with well-known script paths (`scripts/staging/{setup,deploy,e2e,teardown}.sh`). Key controls:
- **Teardown safety guards** — two independent checks (not production names + contains "staging")
- **Cost budget gate** — deterministic reconciliation against provider state
- **SPC fitness functions** — z-score anomaly detection on deploy duration, resource count, errors, cost
- **Branch name sanitization** — RFC 1123 via centrally shipped `slugify-branch` script

### 5. Architectural Governance

The `coding-discipline` and `context-map-domain-model` spokes enforce:
- Domain model alignment — changes must respect bounded contexts and ubiquitous language
- Single source of truth — no duplication, canonical sources for config/schema/logic/routing
- YAGNI ladder — 5-rung decision framework (hard-code → parameterize → abstract → plugin → language)
- Living Context Map and Domain Model under `docs/` with L1 (prose) and L2 (ERD) zoom levels

### 6. Error and Warning Management

Three spokes form a triage pipeline:
- `fail-loud-never-coerce` — input validation and error handling at the code level
- `warnings-and-deprecations` — three-category triage (actionable/deferred/not-actionable) with plan-required suppression
- `lsp-errors` — pre-existing error handling with tech debt lifecycle (discovery → conversion → deletion → index)

### 7. Process Models and Workflow Rituals

The `workflow-rituals` spoke defines 8 named protocols (Sashay, Jam, Parley, Retro, Make Fair, Sitrep, Muse, EDA) with trigger-based routing. The `overnight-autonomy` spoke adds a state machine (idle → nudging → autonomous → done) for unattended operation with self-deleting cleanup.

### 8. Agent-Specific Testing

The `agent-testing` spoke defines test types unique to agent systems: orchestration integration tests, skill behavioral contracts (YAML), adversarial input testing (prompt injection, out-of-scope, tool misuse, role confusion), and MCP server integration tests (server-level deterministic + model-integration non-deterministic).

### 9. Infrastructure and Tooling Standards

- `new-project-scaffolding` — standardized two-surface docs tree, mandatory files, LSP config, Git LFS rules
- `worktree-discipline` — pre-checkout hook blocking non-main branch checkouts in root
- `tool-usage` — consolidated conventions (1Password batching, uv-only Python, playwright-first web)
- `todo-discipline` — cross-agent schema reference (OpenCode vs Claude Code)
- `mermaid-usage` — Mermaid-first diagram policy with `mmdc` validation before commit
- `event-contracts` — YAML event specs with meta-schema validation and drift detection

---

## Points of Agreement

All spokes consistently apply RFC 2119 key words and the "Fail Loud; Never Coerce" principle. The test-first mandate is reinforced across `test-driven-design`, `debugging-ritual`, `warnings-and-deprecations`, and `operator-assisted-e2e`. The hub-and-spoke navigation protocol is universal — every spoke is discovered through the same trigger-keyword mechanism.

## Points of Disagreement / Tension

- **Test tier vs. single-command expectation:** The test tier system (Tier 0/1/2) allows different commands per tier, but the sashay protocol expects a single `## Test command` declaration. Projects with only Tier 0 declared skip higher tiers silently — which could mask missing test infrastructure.
- **Verbatim mandate vs. practical truncation:** The swain-search skill's verbatim mandate (snapshots must be full reproductions) conflicts with the user's instruction to use `[...]` truncation for non-applicable sections. This trove follows the verbatim mandate per the skill protocol.

## Gaps

- **No formal code review process** — the framework relies on agent self-discipline; there is no peer review requirement
- **No quantitative quality targets** — no defect density, MTBF, or DORA metrics as explicit objectives
- **No process auditing** — no mechanism to verify that agents actually follow the documented processes
- **No performance/load testing requirement** — speed guidelines exist but no load/stress testing mandate
- **No requirements traceability matrix** — event contracts trace producers/consumers but no requirement-to-test linkage
- **No accessibility or compatibility testing** — not addressed in any spoke
- **No customer feedback integration** — quality system is disconnected from end-user experience
