# Musing: Review Intent vs. Review Code — Claydon's Thesis vs. Our Framework

> **⚠ AI-generated, no human review.** This musing was written by an AI language model. No human has verified the claims, checked the source material citations, or reviewed the reasoning.

**Date:** 2026-07-07
**Source:** `trove: review-intent-vs-review-code@c652d70`
**Related:** `analysis/QUALITY-FRAMEWORK.md`, `analysis/troves/agents-hub-and-spoke-framework@6522010`

---

Claydon's "Stop Reviewing Code. Start Reviewing Intent." proposes removing code review entirely when AI writes most code, replacing it with spec-first BDD, human-written tests, executable constraint layers, and an adversarial planning loop. Our quality framework analysis identified "Formal code/peer review" as a critical gap. These two positions are complementary, not contradictory — but they reveal a deeper question about what we're optimizing for.

## Convergence

Both identify the same core problem: code review volume is unsustainable when AI generates most code. Claydon cites Farley's "12,000 lines/day" stat; our gap analysis flags code review as a critical gap with no coverage. Both agree human attention should shift upstream to design decisions, not implementation verification.

Both converge on spec-first testing. Claydon's model (BDD specs → human-written tests → AI-generated code) mirrors our Required Test Coverage table where behavioral tests and UAT are pre-implementation (RGR), not post-hoc. Farley's `calculateTax` example (tests generated from existing code inherit bugs) is the same argument our inverse-assertion requirement makes: a suite with only happy-path assertions is incomplete.

## Where Claydon Goes Further

Claydon proposes **removing code review entirely** for a pilot quarter. Our framework identifies the gap but recommends a "self-review checklist" — an incremental fix, not a model change.

His **adversarial planning loop** (planning agent + security agent + human arbiter after 5 rounds) is a concrete mechanism for design review that our Parley ritual gestures at but doesn't specify. This is the most directly implementable idea: a bounded adversarial loop before any code exists, catching bad decisions before they become bad code.

His **executable constraint layer** (CI-enforced agent rules with agent-readable error messages) is a specific implementation of our coding discipline and fail-loud principles — but our framework keeps those as behavioral rules in markdown, not as CI gates. The insight: "Tenant field is required on all database queries" in a CI error message lands in the agent's generation loop where it can act on it. A paragraph in a markdown file does not.

## Where Our Framework Goes Further

Our framework has 18 quality control dimensions; Claydon addresses maybe 4 (test quality, constraint layer, adversarial planning, canary deployment).

Our **non-deterministic testing protocol** (pass^k, session-enriched JSONL) has no analogue in his post. If AI writes the code and tests are the definition of correctness, how do you verify tests that involve LLM judgement? He doesn't address this.

Our **behavioral quality controls** (catch-yourself, no rationalizing, fail-loud) are entirely absent from his model. He assumes the agent is a reliable implementation layer and focuses on the human side. This is the biggest gap in his proposal: he has no mechanism for agent-level quality assurance.

Our **SPC fitness functions** in deployment pipelines, **MTP/LTP coverage matrices** with blast-radius gating, and **test tier system** are all more sophisticated than his "CI green + SAST clean" merge gate.

## The Fundamental Tension

Claydon's thesis: **code review is the wrong artifact to review** — replace it with spec/test/constraint review.

Our framework's thesis: **agent behavior itself needs quality controls** — the agent is not a reliable black box.

These are complementary. Claydon addresses the human-side bottleneck; our framework addresses the agent-side reliability problem. A combined system would look like:

1. Claydon's spec-first pipeline (BDD specs → human tests → AI code)
2. Claydon's adversarial planning loop (pre-implementation design review)
3. Claydon's executable constraint layer (CI-enforced agent rules)
4. Our behavioral controls (catch-yourself, fail-loud) governing the agent
5. Our non-deterministic testing protocol (pass^k) for LLM-judged assertions
6. Our MTP/LTP coverage matrix with blast-radius gating
7. Our SPC fitness functions monitoring deployment health
8. Our test tier system (unit → integration → staging E2E)

## What Our Gap Analysis Missed

Our gap analysis lists "Formal code/peer review" as a critical gap. Claydon's argument suggests this gap may be **intentional and correct** — not a gap at all, but a category error. The real gap isn't "we need better code review" but "we need different review artifacts."

Our recommendations (self-review checklist, static analysis gate) are incremental improvements to the existing model. Claydon's experiment would test whether the model itself is wrong. If he's right, our framework's weakest area (code review) isn't a gap to fill — it's a legacy practice to replace.

## Open Questions

- Can the adversarial planning loop be integrated into the sashay protocol? A pre-implementation gate before any code is written?
- How do we reconcile Claydon's "no code review" with our framework's test-first mandate? If the agent writes code from specs, the tests are the review — but who reviews the tests?
- Claydon's Ship/Show/Ask routing for specs/tests/constraints maps well onto our workflow rituals. Could the sashay protocol adopt this routing?
- The executable constraint layer is the most actionable takeaway. Our coding discipline spoke could specify that agent rules must be CI-enforceable, not just documentary.
