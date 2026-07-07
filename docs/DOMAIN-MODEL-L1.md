# Domain Model — L1 (Prose Glossary)

## Quality Control Domain

### Quality Control
A mechanism that verifies, enforces, or improves the quality of software artifacts or the processes that produce them. Quality controls in this framework operate at two levels: **product quality** (the code, tests, documentation) and **process quality** (how the AI agent works).

### Quality Gate
A checkpoint that blocks progress unless predefined quality thresholds are met. Examples: the blast-radius gate in test planning, the sashay step 7 test suite requirement, the teardown safety guards.

### Behavioral Guard
A rule that governs how the AI agent itself behaves, not what it produces. Examples: "catch yourself before you fix," "no rationalizing test failures," "fail loud; never coerce."

### Coverage Matrix
A 3-dimensional mapping of workflow paths × coverage classes (Happy/Sad/Edge/Corner) × blast-radius priority. The Master Test Plan (MTP) is the living project-level matrix; the Level Test Plan (LTP) is the per-change delta.

### Blast Radius
The risk impact of a workflow path, determined by whether it touches any of six universal risk categories: Safety, Security, Financial, Privacy, Availability, Integrity.

### Sashay
A workflow ritual that takes a defined task from plan through implementation, testing, staging deployment, and merge. The primary quality assurance pipeline.

### Spoke
A detailed reference file linked from the hub AGENTS.md. Spokes contain the authoritative quality control rules; the hub contains trigger keywords and brief summaries.

## Manufacturing Analogues Domain

### Poka-Yoke
Mistake-proofing — designing processes so errors are impossible or immediately detectable. In the framework: catch-yourself triggers, pre-checkout hooks, teardown safety guards.

### Andon
Stop-the-line — when a problem is detected, stop and fix it before continuing. In the framework: fail-loud principle, no silent fallbacks.

### SPC (Statistical Process Control)
Using statistical methods to monitor process stability. In the framework: z-score anomaly detection in staging pipelines, pass^k metrics for non-deterministic tests.

### DMAIC
Define-Measure-Analyze-Improve-Control — the Six Sigma improvement cycle. In the framework: the debugging ritual (8 steps).

### Kaizen
Continuous incremental improvement. In the framework: retro → next actions, muse → plan → sashay pipeline.
