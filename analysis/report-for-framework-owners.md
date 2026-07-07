# How Does Your Quality Framework Compare?

> **⚠ AI-generated, no human review.** This report was written by an AI language model (DeepSeek V4 Flash). The analysis, the comparisons, the gap assessments — all of it. No human has verified the claims, checked the source material citations, or reviewed the reasoning. The underlying source troves were also collected and normalized by AI. Treat everything here as a starting point for your own investigation, not as authoritative analysis.

You have a quality framework — maybe engineering standards, a QA process, or an agent instruction system. You found this repo and want to know: how does this one differ from mine? What would I gain or lose by adopting pieces of it? And how does it relate to Claydon's argument that code review itself is the wrong artifact to review?

This report answers those questions. It's based on a detailed analysis of an agent instruction framework (`~/.agents/AGENTS.md`) that implements 18 quality control dimensions — benchmarked against ISO 25010, IEEE 829, ISTQB, Six Sigma, TQM, SPC, and ISO 9001.

## What You're Looking At

This repo contains three layers of material:

1. **This report** (`analysis/report-for-framework-owners.md`) — what you're reading now. Written for humans. No ISO standard tables, no agent-internal jargon. It synthesizes the analysis into what matters for someone comparing frameworks.

2. **The full analysis** (`analysis/QUALITY-FRAMEWORK.md`) — a 655-line document that inventories every quality control, maps each to industry standards, and identifies coverage and gaps. It was originally written for machine parsing (agents navigating the framework), but it's the authoritative source if you want the complete picture.

3. **The source material** — verbatim copies of the 21 files that make up the analyzed framework, collected as research troves. The [synthesis](troves/agents-hub-and-spoke-framework/synthesis.md) summarizes what each file contains and how they relate. The individual [snapshot files](troves/agents-hub-and-spoke-framework/sources/) are exact reproductions — read those if you want to see the original rules verbatim rather than through my analysis.

The Claydon comparison is documented separately in [`analysis/review-intent-vs-review-code.md`](review-intent-vs-review-code.md), with the source article captured as a [research trove](troves/review-intent-vs-review-code/).

---

## The Short Version

**This framework is unusually strong in three areas that most quality systems neglect:**

1. **Behavioral quality controls** — rules that govern how the AI agent itself behaves. Catch-yourself-before-you-fix triggers, no-rationalizing test failures, fail-loud principles. These have no analogue in traditional QA. They map to manufacturing's poka-yoke (mistake-proofing) and andon (stop-the-line).

2. **Non-deterministic testing** — a rigorous protocol for tests that involve LLM judgement. The `pass^k` metric (probability that all k independent trials succeed), session-enriched JSONL logging, and ratio guidance for deterministic-to-non-deterministic test mix. This is a novel contribution — most frameworks don't address this at all.

3. **Statistical process control in deployment** — SPC fitness functions (z-score anomaly detection) monitoring deploy duration, resource count, errors, and cost. 3σ threshold. This is rare even in industry practice.

**It has three notable gaps that your framework might already cover:**

- **Formal code/peer review** — there is none. The framework relies on agent self-discipline. (Though Claydon's argument suggests this may be intentional — see below.)
- **Quantitative quality targets** — no defect density, DORA metrics, MTBF, or other standard measures.
- **Performance/load testing** — speed guidelines exist but no load or stress testing mandate.

**And it has a surprising relationship to Claydon's "Stop Reviewing Code" thesis:**

The framework's biggest gap (no code review) and Claydon's central argument (code review is the wrong artifact to review) point in the same direction. The framework's recommendations are incremental — add a self-review checklist, add a static analysis gate. Claydon's proposal is structural — remove code review entirely, replace it with spec-first BDD, human-written tests, executable constraint layers, and an adversarial planning loop. They're complementary: Claydon addresses the human-side bottleneck; this framework addresses the agent-side reliability problem.

---

## What This Framework Does Well

### 1. Test Discipline (the strongest dimension)

The test-driven design spoke (583 lines — the longest by far) is the most comprehensive quality control in the framework. It mandates:

- **Test-first for defects** — write a failing test before any fix. Not optional. The framework includes a list of trigger phrases ("The fix is to add X", "I'll patch Y", "Root cause is...") that should stop you mid-sentence and redirect to writing the test first.
- **Inverse-assertion requirement** — every test suite must include at least one test where the expected outcome is a failure. A suite with only happy-path assertions is considered incomplete.
- **9 required test types** — unit, integration, behavioral, UAT, smoke, E2E, adversarial, red-team, penetration. None are optional.
- **MTP/LTP coverage matrix** — a two-tier system: a Master Test Plan (living project-level matrix) and a Level Test Plan (per-change delta). The matrix is 3-dimensional: workflow paths × coverage classes (Happy/Sad/Edge/Corner) × blast-radius weight.
- **Blast-radius binary gate** — does this path touch Safety, Security, Financial, Privacy, Availability, or Integrity? If yes, beyond-happy-path coverage is required. If no, happy-path is sufficient.
- **RGR cycle enforcement** — tests must always execute. Missing prerequisites = Red, not skip. No silent fallbacks.
- **Test tier system** — Tier 0 (unit/lint, always runs), Tier 1 (integration, local services), Tier 2 (staging E2E, only during deploy).
- **Non-deterministic testing protocol** — `pass^k` as the primary metric (not `pass@k`), k=5 recommended, k=3 minimum. Session-enriched JSONL logging with UUID, case-run records, and session footer.

**What this means for you:** If your framework already has strong test discipline, this will feel familiar but more rigorous in specific areas (the blast-radius gate, the MTP/LTP delta procedure, the non-deterministic protocol). If your framework is weaker on testing, this is the highest-value area to adopt from.

### 2. Behavioral Quality Controls (the most distinctive dimension)

These are rules that govern how the AI agent itself behaves — not what it produces, but how it operates. They have no direct analogue in traditional software QA:

- **Catch-yourself-before-you-fix** — a list of fix-intent phrases that trigger a STOP reflex. The framework recognizes that the most common failure mode is: diagnose a bug → jump to implementation → skip the test. The trigger words are a behavioral guard against this.
- **No rationalizing test failures** — "A test failure is a test failure." You either fix it or decide it's testing the wrong thing. No euphemisms, no "signal," no "evidence," no "indication."
- **Fail-loud principle** — reject bad input at boundaries with specific named errors. No silent fallbacks. No bare except without re-raise. Crash over corruption.
- **Noisy-test discipline** — write tests before attempting fixes. Manual verification can supplement, but automated testing must be drafted first.

**What this means for you:** These are the most portable pieces of the framework. They don't depend on the specific tooling or architecture. You can adopt the catch-yourself trigger list, the fail-loud principle, and the no-rationalizing rule regardless of what framework you use. They're essentially engineering culture encoded as agent instructions.

### 3. Deployment Safety

The two-stage pipelines spoke mandates staging + production targets with well-known script paths:

- **Teardown safety guards** — two independent checks before destroying any resource: resource names must not match production names, and must contain the literal string "staging."
- **Cost budget gate** — deterministic reconciliation against provider state. The ledger is never trusted on its own.
- **SPC fitness functions** — z-score anomaly detection on deploy duration, resource count, errors, and cost. Default 3σ threshold (Western Electric/Nelson standard).
- **Branch name sanitization** — RFC 1123 via a centrally shipped slugify script.

**What this means for you:** The SPC fitness functions are the standout feature here. Most deployment pipelines have monitoring but not statistical process control. The teardown safety guards are a simple but effective pattern that any team could adopt.

### 4. Architectural Governance

- **Domain model alignment** — changes must respect bounded contexts and ubiquitous language. Before writing code, the agent must ensure the Context Map and Domain Model exist and are current.
- **Single source of truth** — every piece of knowledge lives in exactly one place. No duplication, no drift.
- **YAGNI ladder** — a 5-rung decision framework: hard-code → parameterize → abstract → plugin → language. Start at the lowest rung that works. Only climb with evidence.

**What this means for you:** The YAGNI ladder is the most practical piece. It gives you a concrete decision framework for when to build generality vs. when to stay specific. Most teams have this as intuition; this makes it explicit.

---

## Where This Framework Has Gaps

### Critical Gaps (No Coverage)

| Gap | Why It Matters |
|-----|----------------|
| **Formal code/peer review** | No independent verification of code quality. The framework relies entirely on agent self-discipline. (But see Claydon section below — this may be intentional.) |
| **Performance/load testing** | Speed guidelines exist but no load, stress, or volume testing requirement. |
| **Requirements traceability** | Event contracts trace producers/consumers, but there's no requirement-to-test matrix. You can't verify all requirements are tested. |
| **Process auditing** | No mechanism to verify that agents actually follow the documented processes. |
| **Test data management** | No test data strategy or data privacy requirements. |
| **Accessibility testing** | No WCAG or accessibility compliance verification. |
| **Compatibility testing** | No cross-platform, cross-browser, or cross-version testing. |
| **Management review** | No organizational oversight of the quality system. |
| **Customer feedback integration** | The quality system is disconnected from end-user experience. |
| **Formal inspection process** | No structured defect detection in artifacts (no moderator, reader, recorder roles). |

### Moderate Gaps (Partial Coverage)

| Gap | What Exists | What's Missing |
|-----|-------------|----------------|
| **Static analysis gating** | LSP error handling (reactive) | Proactive CI gate blocking on quality thresholds |
| **Quantitative quality metrics** | pass^k, SPC z-scores | Defect density, MTBF, code churn, DORA metrics |
| **Release management** | Two-stage pipeline | Release versioning, rollback strategy, deployment approval |
| **Requirements specification** | PURPOSE.md, domain model | Formal requirements format, validation process |
| **Test design techniques** | Happy/Sad/Edge/Corner | Equivalence partitioning, boundary value analysis, decision tables |
| **Build reproducibility** | Speed guidelines | Build reproducibility verification, SBOM |
| **Usability testing** | Operator-assisted UAT | Usability-specific test cases, heuristic evaluation |
| **Process capability measurement** | SPC monitoring | Cp, Cpk indices, process baseline establishment |

---

## How It Compares to Claydon's "Stop Reviewing Code. Start Reviewing Intent."

Claydon's post (July 2026) proposes removing code review entirely when AI writes most code, replacing it with:

| Artifact | Author | Review required |
|----------|--------|----------------|
| Specification (BDD / acceptance criteria) | Human | Yes |
| Tests | Human | Yes |
| Constraint layer (agent rules, CI gates) | Human | Yes |
| Code | AI | No |

Plus an adversarial planning loop (planning agent + security agent + human arbiter after 5 rounds) and canary deployment as the runtime safety net.

### Where They Converge

Both identify the same core problem: code review volume is unsustainable when AI generates most code. Claydon cites Farley's "12,000 lines/day" stat; this framework's gap analysis flags code review as a critical gap. Both agree human attention should shift upstream to design decisions, not implementation verification.

Both converge on spec-first testing. Claydon's model (BDD specs → human-written tests → AI-generated code) mirrors this framework's requirement that behavioral tests and UAT be pre-implementation (RGR), not post-hoc. Farley's `calculateTax` example (tests generated from existing code inherit bugs) is the same argument this framework's inverse-assertion requirement makes.

### Where Claydon Goes Further

Claydon proposes **removing code review entirely** for a pilot quarter. This framework identifies the gap but recommends a "self-review checklist" — an incremental fix, not a model change.

His **adversarial planning loop** is a concrete mechanism for design review that this framework's Parley ritual gestures at but doesn't specify. A planning agent proposes, a security agent attacks the plan, and if they can't agree after five rounds a human steps in. This is the most directly implementable idea: a bounded adversarial loop before any code exists, catching bad decisions before they become bad code.

His **executable constraint layer** (CI-enforced agent rules with agent-readable error messages) is a specific implementation of this framework's coding discipline and fail-loud principles — but this framework keeps those as behavioral rules in markdown, not as CI gates. The insight: "Tenant field is required on all database queries" in a CI error message lands in the agent's generation loop where it can act on it. A paragraph in a markdown file does not.

### Where This Framework Goes Further

This framework has 18 quality control dimensions; Claydon addresses maybe 4 (test quality, constraint layer, adversarial planning, canary deployment).

The **non-deterministic testing protocol** (pass^k, session-enriched JSONL) has no analogue in his post. If AI writes the code and tests are the definition of correctness, how do you verify tests that involve LLM judgement? He doesn't address this.

The **behavioral quality controls** (catch-yourself, no rationalizing, fail-loud) are entirely absent from his model. He assumes the agent is a reliable implementation layer and focuses on the human side. This is the biggest gap in his proposal: he has no mechanism for agent-level quality assurance.

The **SPC fitness functions** in deployment pipelines, **MTP/LTP coverage matrices** with blast-radius gating, and **test tier system** are all more sophisticated than his "CI green + SAST clean" merge gate.

### The Fundamental Tension

Claydon's thesis: **code review is the wrong artifact to review** — replace it with spec/test/constraint review.

This framework's thesis: **agent behavior itself needs quality controls** — the agent is not a reliable black box.

These are complementary. Claydon addresses the human-side bottleneck; this framework addresses the agent-side reliability problem. A combined system would look like:

1. Claydon's spec-first pipeline (BDD specs → human tests → AI code)
2. Claydon's adversarial planning loop (pre-implementation design review)
3. Claydon's executable constraint layer (CI-enforced agent rules)
4. This framework's behavioral controls (catch-yourself, fail-loud) governing the agent
5. This framework's non-deterministic testing protocol (pass^k) for LLM-judged assertions
6. This framework's MTP/LTP coverage matrix with blast-radius gating
7. This framework's SPC fitness functions monitoring deployment health
8. This framework's test tier system (unit → integration → staging E2E)

### What the Gap Analysis Missed

This framework's gap analysis lists "Formal code/peer review" as a critical gap. Claydon's argument suggests this gap may be **intentional and correct** — not a gap at all, but a category error. The real gap isn't "we need better code review" but "we need different review artifacts."

The framework's recommendations (self-review checklist, static analysis gate) are incremental improvements to the existing model. Claydon's experiment would test whether the model itself is wrong. If he's right, this framework's weakest area (code review) isn't a gap to fill — it's a legacy practice to replace.

---

## What You Can Take From This

### Portable Patterns (Low Context, High Value)

These work regardless of your tooling or architecture:

| Pattern | What It Does |
|---------|-------------|
| **Inverse-assertion requirement** | Every test suite must include a failure-expectation test. A suite with only happy-path assertions is incomplete. |
| **Blast-radius binary gate** | Paths touching Safety/Security/Financial/Privacy/Availability/Integrity require beyond-happy-path coverage. Everything else needs only happy-path. |
| **pass^k metric** | Primary reliability metric for LLM-judged tests. pass^1 × pass^1 × ... (k times). k=5 recommended, k=3 minimum. |
| **Catch-yourself trigger words** | A list of fix-intent phrases that trigger a STOP-and-write-test-first reflex. |
| **Fail-loud principle** | Reject bad input at boundaries. No silent fallbacks. No bare except without re-raise. Crash over corruption. |
| **Three-category warning triage** | Actionable (fix now) / Deferred Actionable (plan + suppress) / Not Actionable (suppress + document). Suppression without a plan is information hiding. |
| **YAGNI ladder** | 5-rung decision framework: hard-code → parameterize → abstract → plugin → language. Start at the lowest rung that works. |
| **MTP/LTP coverage matrix** | Master Test Plan (living project matrix) + Level Test Plan (per-change delta). 3D: workflow paths × Happy/Sad/Edge/Corner × blast-radius weight. |
| **SPC fitness functions** | z-score anomaly detection on deploy duration, resource count, errors, cost. 3σ threshold. |
| **Ship/Show/Ask routing** | From Claydon. Three-tier review routing for specs, tests, and constraint changes. Ship (no review), Show (comment invited), Ask (approval required). |

### Opinionated Patterns (High Context, Adapt Carefully)

These depend on specific tooling or workflow assumptions:

- The hub-and-spoke documentation architecture (works if you have a single entry point for agent instructions)
- The worktree discipline (root on main, branches in `.worktrees/` — assumes git worktree workflow)
- The overnight autonomy state machine (assumes specific OS-level scheduling)
- The Cove developer platform integration (local-only forge/vault — not portable)

### What's Missing That You Might Want to Build

- Formal code review process (identified as a gap — but see Claydon's argument that this gap is intentional)
- Quantitative quality targets (no defect density, DORA metrics, or MTBF objectives)
- Process auditing (no mechanism to verify agents follow the documented processes)
- Performance/load testing requirement (speed guidelines only)
- Requirements traceability matrix (event contracts trace producers/consumers but no requirement-to-test linkage)

---

## How This Report Was Produced

The analysis is based on verbatim source material from 21 files (1 hub + 20 spokes) of the `~/.agents/AGENTS.md` agent instruction framework, collected as a research trove. Each source was captured as a verbatim snapshot and a structured summary. The full analysis document (`analysis/QUALITY-FRAMEWORK.md`) maps every quality control to industry standards and identifies coverage and gaps. The Claydon comparison is documented in [`analysis/review-intent-vs-review-code.md`](review-intent-vs-review-code.md). All source materials are available in `analysis/troves/` for verification.

**Trove references:** `trove: agents-hub-and-spoke-framework@6522010`, `trove: review-intent-vs-review-code@c652d70`
