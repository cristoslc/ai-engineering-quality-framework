# Musing: What We Borrowed — Tracing the Sources

**Date:** 2026-07-07

This repo analyzes an agent instruction framework. But the framework itself, and the analysis methodology, both stand on borrowed ground. Before writing the attribution file, I need to trace what came from where.

## From the workstation troves

The `~/.agents/` framework was built using research collected in the workstation repo's swain-search troves. The QUALITY-FRAMEWORK.md analysis then used those same troves as reference material. The chain is:

**MTP/LTP two-tier coverage matrix** — from `docs/troves/master-test-plan-level-test-plan/`. The ISTQB glossary definition, the IEEE 829 section outline, the TryQA and Testriq explainers, and the Aqua Cloud agile adaptation all fed into the MTP/LTP protocol in `test-driven-design.md`. The synthesis in that trove explicitly maps the agent protocol to the MTP/LTP concepts.

**Happy/Sad/Edge/Corner taxonomy** — from `docs/troves/e2e-test-development-practices/`, specifically the getautonoma source. The blast-radius decision framework (money/session-state/user-generated-data = high blast radius) is also from getautonoma. The failfastmoveon coverage matrix (red/yellow/green/gray weights, reliability scores) influenced the blast-radius gate design.

**AGENTS.md best practices** — from `docs/troves/agents-md-best-practices/`. The hub-and-spoke progressive disclosure pattern, the instruction budget concept (150-200 instructions), the "shorter is better" consensus, and the auto-generation-is-harmful finding from ETH Zurich all shaped the framework's architecture. The trigger-keyword routing pattern is our own invention, but the progressive disclosure mechanism is directly from the Anthropic Skills pattern documented in that trove.

**Overnight autonomy** — from `docs/troves/opencode-claude-overnight-plugins/`. The opencode-scheduler and claude-code-scheduler plugins informed the overnight autonomy spoke's design. The self-deleting launchd timer pattern is adapted from those schedulers.

**AI coding review practices** — from `docs/troves/ai-coding-review-2026/`. Christina Lin's "review the boundaries, not the implementation" and "look for helpful data coercion" patterns influenced the fail-loud-never-coerce spoke and the debugging ritual's "check the obvious first" step.

## From the `~/.agents/` framework itself

The framework being analyzed was built by the user over months. The analysis in this repo is my reading of it. The attribution file should make clear that the framework is the user's work, not mine.

## From the manufacturing and software QA troves

The `analysis/troves/manufacturing-quality-practices.md` and `analysis/troves/software-qa-standards.md` files were collected via Brave Search LLM Context API specifically for this analysis. They reference ISO 25010, IEEE 829, ISTQB, ISO 9001, Six Sigma DMAIC, SPC, TQM, and Lean. These are standard industry references — no single source to attribute, but the synthesis and application to the framework is my analysis.

## From Claydon's blog post

The `analysis/troves/review-intent-vs-review-code/` trove captures Claydon's "Stop Reviewing Code. Start Reviewing Intent." post verbatim. The comparison in `analysis/review-intent-vs-review-code.md` and the report section are my analysis of his thesis against the framework.

## What's original

- The quality controls inventory (cataloguing 18 dimensions across 20 spokes)
- The coverage mapping against industry standards
- The gap analysis from the "textbook complete" perspective
- The manufacturing analogues mapping
- The recommendations
- The musing comparing Claydon's thesis to the framework
- The report for framework owners

## What needs clear attribution

1. The MTP/LTP protocol — ISTQB/IEEE 829 terminology, adapted from the workstation trove
2. The Happy/Sad/Edge/Corner taxonomy — getautonoma
3. The blast-radius gate — getautonoma + failfastmoveon
4. The hub-and-spoke architecture — Anthropic Skills pattern
5. The instruction budget concept — HumanLayer, AI Hero, ETH Zurich
6. The overnight autonomy pattern — opencode-scheduler, claude-code-scheduler
7. The fail-loud principle — Christina Lin's "Fail Fast, Fail Loud" (though the framework had this independently)
8. The `~/.agents/` framework itself — the user's work, not mine
9. Claydon's thesis — his blog post
10. ISO 25010, IEEE 829, ISTQB, Six Sigma, SPC, TQM, ISO 9001 — industry standards, public knowledge
