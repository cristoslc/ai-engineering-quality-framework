# Plan: Humanizer Rewrite of Analysis Files

**Source:** `docs/musings/humanizer-skill-self-audit.md`
**Scope:** Apply the AI output humanizer skill's patterns to the two primary analysis files.

## Inventory of files to edit

| File | Lines | Priority | Rationale |
|------|-------|----------|-----------|
| `analysis/QUALITY-FRAMEWORK.md` | 655 | High | Most visible analysis artifact. Heaviest concentration of AI tells. |
| `analysis/report-for-framework-owners.md` | 230 | High | Primary entry point for framework owners. First impression. |
| `analysis/review-intent-vs-review-code.md` | 63 | Medium | Less visible but still an analysis artifact. |
| `README.md` | 97 | Low | Structural content, fewer tells. Worth a quick pass. |

## Patterns to fix (per humanizer skill)

### P0 — Credibility killers (fix in all files)
- **Em dashes** — ZERO in final output. Replace with commas, periods, or restructure.
- **Significance inflation** — "notable," "distinctive," "surprisingly," "critical" used as evaluators. State the fact, let the reader judge.

### P1 — Obvious AI smell (fix in all files)
- **Tier 1 vocabulary** — Replace on sight: delve, landscape, robust, comprehensive, leverage, pivotal, underscores, seamless, utilize, actionable, impactful, serves as, groundbreaking, transformative, cornerstone, invaluable.
- **Transition phrases** — Cut "Notably," "Furthermore," "Moreover," "In conclusion," "It's worth noting."
- **Hedging** — Cut "may be," "suggests," "arguably," "tends to," "in many cases."
- **Copula avoidance** — Replace "serves as," "represents," "constitutes" with "is."
- **Formulaic openings** — "In today's rapidly evolving..." type constructions.

### P2 — Stylistic polish (fix where time allows)
- **Uniform paragraph length** — Vary deliberately. Include some 1-2 sentence paragraphs.
- **Numbered list inflation** — Only use when content genuinely has that many discrete items.
- **Missing first-person perspective** — Where appropriate, use first person, state preferences.

## Approach

1. Edit each file in place with minimal, targeted changes. Do NOT rewrite entire documents.
2. Focus on P0 and P1 patterns. P2 is nice-to-have.
3. Preserve all factual content, citations, and structure.
4. Self-audit after each file: re-read and confirm patterns are resolved.

## Files NOT to edit

- `docs/musings/humanizer-skill-self-audit.md` — already written in human voice intentionally
- `docs/ARCHITECTURE.md`, `docs/DOMAIN-MODEL-L1.md`, `docs/DEVELOPER-WORKFLOWS.md`, `docs/USER-EXPERIENCE.md` — scaffold, minimal content
- `AGENTS.md`, `PURPOSE.md`, `CHANGELOG.md` — minimal content
- `analysis/troves/` — verbatim reproductions, must not be altered
