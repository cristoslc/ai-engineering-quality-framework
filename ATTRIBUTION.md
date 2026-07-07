# ATTRIBUTION.md

This repository analyzes an AI-assisted software engineering agent framework. The framework itself, the analysis methodology, and the comparisons all draw on external sources. This file documents what came from where.

## The Analyzed Framework

The `~/.agents/AGENTS.md` hub-and-spoke framework analyzed in this repo was built by the repository owner over months of iterative development. The analysis in `analysis/QUALITY-FRAMEWORK.md` and `analysis/report-for-framework-owners.md` is my reading of that framework, not the framework itself. The verbatim source files in `analysis/troves/agents-hub-and-spoke-framework/sources/` are exact reproductions of the owner's work.

## Research Troves Used in the Framework's Design

The `~/.agents/` framework was informed by research collected in the owner's workstation repository. Key influences:

| Concept | Source | What was adapted |
|---------|--------|-----------------|
| MTP/LTP two-tier coverage matrix | ISTQB glossary, IEEE 829, via `docs/troves/master-test-plan-level-test-plan/` | The Master Test Plan + Level Test Plan protocol in `test-driven-design.md` |
| Happy/Sad/Edge/Corner taxonomy | getautonoma, via `docs/troves/e2e-test-development-practices/` | The coverage class definitions and blast-radius decision framework |
| Blast-radius gate | getautonoma + failfastmoveon Agile Coverage Matrix, via `docs/troves/e2e-test-development-practices/` | The binary risk gate (Safety/Security/Financial/Privacy/Availability/Integrity) |
| Hub-and-spoke progressive disclosure | Anthropic Skills pattern, via `docs/troves/agents-md-best-practices/` | The trigger-keyword routing architecture |
| Instruction budget concept | HumanLayer, AI Hero, ETH Zurich study, via `docs/troves/agents-md-best-practices/` | The spoke-based progressive disclosure to keep the hub under the instruction budget |
| Overnight autonomy scheduling | opencode-scheduler, claude-code-scheduler, via `docs/troves/opencode-claude-overnight-plugins/` | The self-deleting launchd/systemd timer pattern |
| "Fail Fast, Fail Loud" | Christina Lin, via `docs/troves/ai-coding-review-2026/` | Reinforced the fail-loud-never-coerce spoke (the framework had this independently) |
| "Review boundaries, not implementation" | Christina Lin, via `docs/troves/ai-coding-review-2026/` | Influenced the debugging ritual's "check the obvious first" step |
| "Tests as Specification" | Christina Lin, via `docs/troves/ai-coding-review-2026/` | Converges with the framework's test-first mandate and inverse-assertion requirement |
| "Types as Contracts" | Christina Lin, via `docs/troves/ai-coding-review-2026/` | Converges with the coding discipline's single-source-of-truth principle |
| "Vertical Slices, Strong Boundaries" | Christina Lin, via `docs/troves/ai-coding-review-2026/` | Converges with the context-map-domain-model spoke's bounded context discipline |
| pass^k metric and k=5 convention | Anthropic τ-Bench, referenced in `test-driven-design.md` | The non-deterministic testing protocol's primary metric and sample size recommendation |
| YAGNI ladder | [ponytail](https://github.com/ponytail/ponytail), referenced in `coding-discipline.md` | The 5-rung decision framework (hard-code → parameterize → abstract → plugin → language) |
| "Helpful Data Coercion" pattern | Christina Lin, via `docs/troves/ai-coding-review-2026/` | Converges with the fail-loud-never-coerce spoke's "no silent fallbacks" rule |
| "Context rot" concept | Christina Lin, via `docs/troves/ai-coding-review-2026/` | Informs the framework's emphasis on keeping the hub under the instruction budget |

## Research Troves Collected for This Analysis

The following troves were collected via Brave Search LLM Context API specifically for the QUALITY-FRAMEWORK.md analysis. They reference publicly available industry standards:

- `analysis/troves/software-qa-standards.md` — ISO/IEC 25010, IEEE 829, ISTQB, ISO/IEC 29119, CI/CD quality gates
- `analysis/troves/manufacturing-quality-practices.md` — ISO 9001, Six Sigma DMAIC, SPC, TQM, Lean, Poka-Yoke

These are standard references. No single source to attribute beyond the standards bodies themselves.

## Claydon's Thesis

The comparison in `analysis/review-intent-vs-review-code.md` and the report section on Claydon is based on:

- **"Stop Reviewing Code. Start Reviewing Intent."** by Claydon (July 2026). Captured verbatim in `analysis/troves/review-intent-vs-review-code/`.
- The adversarial planning loop concept is attributed to Paul Stack (referenced in Claydon's post).
- The `calculateTax` example and spec-first TDD argument are attributed to Dave Farley (referenced in Claydon's post).
- The Ship/Show/Ask framework is attributed to Martin Fowler.

## What's Original to This Repository

- The quality controls inventory (18 dimensions across 20 spokes, catalogued in `analysis/QUALITY-FRAMEWORK.md`)
- The coverage mapping against ISO 25010, IEEE 829, ISTQB, and manufacturing standards
- The gap analysis from the "textbook complete" QA/QC perspective
- The manufacturing analogues mapping (Six Sigma DMAIC → debugging ritual, poka-yoke → catch-yourself triggers, etc.)
- The recommendations
- The musing comparing Claydon's thesis to the framework
- The report for framework owners
- The self-audit against the humanizer skill patterns

## AI Generation Notice

All content in this repository was generated by an AI language model (DeepSeek V4 Flash). The analysis, the comparisons, the gap assessments, the musings, the troves, and this attribution file. No human has verified the claims, checked the source material citations, or reviewed the reasoning. The verbatim snapshot files in `analysis/troves/` are exact reproductions of their originals and are not AI-generated.

## License

This repository is published for reference and discussion. The analyzed framework (`~/.agents/AGENTS.md`) remains the intellectual property of the repository owner. External sources retain their own licenses. The analysis and report are provided as-is, with no guarantees of accuracy or completeness.
