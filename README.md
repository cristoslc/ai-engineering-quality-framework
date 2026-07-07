# AI Engineering Quality Framework

> **⚠ AI-generated, no human review.** Every word in this repository was written by an AI language model (DeepSeek V4 Flash). The analysis, the report, the musings, the troves, the README — all of it. No human has verified the claims, checked the citations, or reviewed the reasoning. Treat everything here as a starting point for your own investigation, not as authoritative analysis.

A quality control analysis of an AI-assisted software engineering agent framework, benchmarked against industry standards (ISO 25010, IEEE 829, ISTQB, Six Sigma, TQM, SPC, ISO 9001) and compared against Claydon's "Stop Reviewing Code. Start Reviewing Intent." thesis.

---

## Personas

### Primary: Framework Owner

You already have a quality framework — maybe a set of engineering standards, a QA process, or an agent instruction system. You found this repo and want to know: how does this one differ from mine? What would I gain or lose by adopting pieces of it? And how does it relate to Claydon's argument that code review itself is the wrong artifact to review?

**Start here:** [`analysis/report-for-framework-owners.md`](analysis/report-for-framework-owners.md) — a human-readable report written for you. It covers what this framework does well, where it has gaps, how it compares to Claydon's thesis, and what patterns you can take from it. No tables of ISO standards, no agent-internal jargon.

**For the Claydon comparison:** [`analysis/musings/review-intent-vs-review-code.md`](analysis/musings/review-intent-vs-review-code.md) — a structured compare-and-contrast. The short version: Claydon addresses the human-side bottleneck (shift review from code to specs/tests/constraints); this framework addresses the agent-side reliability problem (behavioral controls, non-deterministic testing, SPC monitoring). They're complementary. The framework's biggest gap — "no formal code review" — may not be a gap at all if Claydon is right.

**Key questions this analysis answers:**
- What quality controls does this agent framework actually enforce? (18 dimensions catalogued)
- Which industry standards does it meet, exceed, or miss? (ISO 25010, IEEE 829, ISTQB mapped)
- Where are the gaps that matter? (performance testing, requirements traceability, process auditing)
- How does it compare to manufacturing QA? (Six Sigma DMAIC, poka-yoke, SPC, andon — all present)
- What would Claydon's model add or change? (adversarial planning loop, executable constraint layer, spec-first pipeline)

### Secondary: Agent Assisting a Framework Owner

You're an AI agent helping someone navigate this repo. Your user wants to understand how this framework compares to theirs, or whether Claydon's argument changes the analysis. Here's how to orient yourself:

- **`analysis/QUALITY-FRAMEWORK.md`** — the primary artifact. 655 lines. Executive summary, quality controls inventory (20 spokes catalogued), industry standards reference, coverage mapping, gap analysis, recommendations. Read the executive summary first, then drill into the sections relevant to the user's questions.
- **`analysis/troves/agents-hub-and-spoke-framework/`** — verbatim source material. 21 snapshot files of the original agent instruction files. Use these when the user asks about a specific control mechanism — the snapshot is the authoritative source.
- **`analysis/troves/review-intent-vs-review-code/`** — verbatim snapshot of Claydon's blog post plus synthesis.
- **`analysis/musings/review-intent-vs-review-code.md`** — the compare-and-contrast analysis. Use this when the user asks how Claydon's thesis relates to the framework analysis.

**Common user questions and where to find answers:**
- "How does this compare to my framework?" → Read the quality controls inventory (section 2) and coverage mapping (section 4). Ask the user what dimensions their framework covers and compare.
- "Should I adopt Claydon's model?" → Read the musing. The answer depends on whether the user's bottleneck is human review throughput (Claydon helps) or agent reliability (this framework helps).
- "What can I excerpt for my own framework?" → See the tertiary persona below.
- "What's missing from this analysis?" → The gap analysis (section 5) and recommendations (section 7) are the best starting points.

### Tertiary: Framework Excerptor

You maintain your own agent instruction system or quality framework and want to know what pieces of this one are portable. This repo is an analysis of a specific framework (`~/.agents/AGENTS.md`), not a framework itself — but the analysis identifies patterns you can adopt.

**What's portable (low-context, high-value):**

| Pattern | Source | What it does |
|---------|--------|-------------|
| **Inverse-assertion requirement** | `test-driven-design.md` | Every test suite must include a failure-expectation test. A suite with only happy-path assertions is incomplete. |
| **Blast-radius binary gate** | `test-driven-design.md` | Paths touching Safety/Security/Financial/Privacy/Availability/Integrity require beyond-happy-path coverage. Everything else needs only happy-path. |
| **pass^k non-deterministic metric** | `test-driven-design.md` | Primary reliability metric for LLM-judged tests. pass^1 × pass^1 × ... (k times). k=5 recommended, k=3 minimum. |
| **Catch-yourself trigger words** | `debugging-ritual.md` | A list of fix-intent phrases that trigger a STOP-and-write-test-first reflex. |
| **Fail-loud principle** | `fail-loud-never-coerce.md` | Reject bad input at boundaries. No silent fallbacks. No bare except without re-raise. Crash over corruption. |
| **Three-category warning triage** | `warnings-and-deprecations.md` | Actionable (fix now) / Deferred Actionable (plan + suppress) / Not Actionable (suppress + document). Suppression without a plan is information hiding. |
| **YAGNI ladder** | `coding-discipline.md` | 5-rung decision framework: hard-code → parameterize → abstract → plugin → language. Start at the lowest rung that works. |
| **MTP/LTP coverage matrix** | `test-driven-design.md` | Master Test Plan (living project matrix) + Level Test Plan (per-change delta). 3D: workflow paths × Happy/Sad/Edge/Corner × blast-radius weight. |
| **SPC fitness functions** | `two-stage-pipelines.md` | z-score anomaly detection on deploy duration, resource count, errors, cost. 3σ threshold. |
| **Ship/Show/Ask routing** | Claydon (via musing) | Three-tier review routing for specs, tests, and constraint changes. Ship (no review), Show (comment invited), Ask (approval required). |

**What's opinionated (high-context, adapt carefully):**
- The hub-and-spoke documentation architecture (works if you have a single entry point for agent instructions)
- The worktree discipline (root on main, branches in `.worktrees/` — assumes git worktree workflow)
- The overnight autonomy state machine (assumes specific OS-level scheduling)
- The Cove developer platform integration (local-only forge/vault — not portable)

**What's missing that you might want to build:**
- Formal code review process (identified as a gap — but see Claydon's argument that this gap is intentional)
- Quantitative quality targets (no defect density, DORA metrics, or MTBF objectives)
- Process auditing (no mechanism to verify agents follow the documented processes)
- Performance/load testing requirement (speed guidelines only)
- Requirements traceability matrix (event contracts trace producers/consumers but no requirement-to-test linkage)

---

## Structure

```
analysis/
  QUALITY-FRAMEWORK.md          # Primary analysis (655 lines)
  report-for-framework-owners.md    # Human-readable report
  musings/
    review-intent-vs-review-code.md  # Claydon comparison
  troves/
    agents-hub-and-spoke-framework/  # 21 verbatim source files
    review-intent-vs-review-code/    # Claydon blog post source
    software-qa-standards.md         # Industry standards reference
    manufacturing-quality-practices.md  # Manufacturing analogues
docs/                          # Project documentation (scaffold)
  ARCHITECTURE.md
  DOMAIN-MODEL-L1.md
  DEVELOPER-WORKFLOWS.md
  USER-EXPERIENCE.md
  agents-detail/
```

## Quick Start

1. **Framework owner:** Read [`analysis/report-for-framework-owners.md`](analysis/report-for-framework-owners.md) — the human-readable report. If you want the full technical analysis with all industry mappings, `analysis/QUALITY-FRAMEWORK.md` is available but was written for machine parsing.
2. **Claydon-curious:** Read [`analysis/musings/review-intent-vs-review-code.md`](analysis/musings/review-intent-vs-review-code.md) — then decide whether to read the full framework analysis.
3. **Excerptor:** Scan the portable patterns table above, then drill into the relevant trove source files for the full specification.
