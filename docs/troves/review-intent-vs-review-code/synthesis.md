# Synthesis: Review Intent vs. Review Code

**Trove:** review-intent-vs-review-code
**Created:** 2026-07-07
**Sources:** 1 (web article)

---

## Key Findings

Claydon's post argues that when AI writes most code, the traditional PR-level code review collapses under volume. His proposed replacement: shift human review upstream to specifications, tests, and constraint layers, and let CI gates (not human approval) verify the code.

### The Core Model

| Artifact | Author | Review required | What it encodes |
|---|---|---|---|
| Specification (BDD / acceptance criteria) | Human | Yes | What the system must do |
| Tests | Human | Yes | What "done" means |
| Constraint layer (agent rules, CI gates) | Human | Yes | What agents are not allowed to do |
| Code | AI | No | An implementation satisfying the above |

### Key Mechanisms

- **Adversarial planning loop** — a planning agent proposes, a security agent attacks the plan, human arbitrates after 5 rounds if no agreement. Replaces design review.
- **Executable constraint layer** — agent instructions as CI-enforced lint rules with agent-readable error messages, not prose in markdown files.
- **Spec-first TDD** — BDD specs written before code, tests written from specs, AI generates code to pass tests. Farley's "calculateTax" example: tests generated from existing code inherit bugs; tests written from specs catch them.
- **Ship/Show/Ask routing** — constraint changes, specs, and tests each have different review requirements depending on scope and risk.
- **Canary deployment as runtime safety net** — roll back in minutes when something wrong gets through.

### Preconditions

1. Constraint layer must be executable (CI-enforced), not documentary
2. Test quality must come first — coverage floor hit by spec-first writing
3. SAST before scope expansion — functional tests don't catch injection/auth bypass
4. Adversarial planning loop replaces design review
5. Canary phase must have meaningful promotion gates

### The Experiment

One quarter setup (choose service, raise test quality, build constraint layer, verify canary gates) → one quarter live (remove approval step, keep CI gate). Measure defect ratio, review wait time, team sentiment.

---

## Points of Agreement

- Code review volume is unsustainable when AI generates most code
- Tests written against existing code inherit bugs (Farley's calculateTax example)
- Human attention should go to design decisions, not implementation verification
- CI gates are more reliable than human rubber-stamping

## Gaps

- Single source — this is one person's proposal, not a surveyed field
- No discussion of non-deterministic testing or statistical reliability metrics
- No discussion of agent behavioral controls (catch-yourself, fail-loud)
- No discussion of test tier systems or coverage matrices
- No discussion of deployment pipeline SPC or statistical process control
