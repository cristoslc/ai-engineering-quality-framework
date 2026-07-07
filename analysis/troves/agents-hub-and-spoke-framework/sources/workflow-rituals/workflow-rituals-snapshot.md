---
slug: workflow-rituals
title: Workflow Rituals
type: local
path: ~/.agents/agents-md-detail/workflow-rituals.md
fetched: 2026-07-07T14:00:00Z
---

# Workflow Rituals

Named protocols for distinct phases. Rituals are NOT skills — do NOT invoke them with the `skill` tool. They have no SKILL.md files.

## Routing protocol (MUST follow)

When a trigger keyword matches, you MUST READ the corresponding spoke file before acting. Do NOT act on ritual knowledge from memory alone — READ the file.

1. Match the trigger keyword to a ritual below.
2. READ the linked spoke file with the `read` tool.
3. Apply the protocol described in that spoke file.

## Trigger → spoke routing

**Sashay** — `sashay`, `pr-sashay`, `pr-work`, `handoff`, `branch+PR+dispatch`, `plan-to-PR`, `chronicle`, `draft PR handoff`, `backgrounded PR work`, `sashay closure`, `sashay closed`, `end the sashay`, `close the sashay`, `sashay teardown`, `sashay cleanup`, `sashay complete`
→ MUST READ `~/.agents/agents-md-detail/workflow-rituals/pr-sashay.md`

**Jam session** — `jam`, `jam session`, `let's jam`, `jam on`, `jammed up`
→ MUST READ `~/.agents/agents-md-detail/workflow-rituals/jam-session.md`

**Parley** — `parley`, `let's parley`, `parley on`, `talk it through`, `discuss`, `what do you think`, `weigh in`
→ MUST READ `~/.agents/agents-md-detail/workflow-rituals/parley.md`

**Retro** — `retro`, `retrospective`, `reflect`, `debrief`, `what did we learn`, `post-mortem`, `lessons learned`, `session close`, `wrap up`
→ MUST READ `~/.agents/agents-md-detail/workflow-rituals/retro.md`

**Make fair** — `fair`, `make fair`, `make-fair`, `walk away`, `neutral state`, `tidy the repo`, `secure the repo`, `close out`, `before I go`
→ MUST READ `~/.agents/agents-md-detail/workflow-rituals/make-fair.md`

**Sitrep** — `sitrep`, `situation report`, `what's going on`, `catch me up`, `where were we`
→ MUST READ `~/.agents/agents-md-detail/workflow-rituals/sitrep.md`

**Muse** — `muse`, `muse on`, `muse about`, `muse over`, `musings`, `musing`, `stream of consciousness`, `note to self`, `docs/musings/`
→ MUST READ `~/.agents/agents-md-detail/workflow-rituals/musings.md`

**EDA** — `eda`, `exploratory data analysis`, `data analysis`, `data profiling`, `visualize`, `visualize data`, `statistical test`, `data cleaning`, `feature engineering`, `dataset`, `data analysis report`, `data exploration`, `load dataset`, `profile dataset`
→ MUST READ `~/.agents/agents-md-detail/workflow-rituals/eda.md`

## Quick reference

| If you want to... | Use | What you end with |
|---|---|---|
| Hand a defined task to the background | Sashay | Merged PR + chronicle |
| Iterate live on an ill-defined problem | Jam session | Squashed commits + running log |
| Conversationally align on a design decision | Parley | Aligned decisions + updated shared language |
| Reflect on what a session surfaced | Retro | Structured retro doc + routed next actions |
| Leave the repo walk-away clean | Make fair | Clean tree + gap report |
| Catch up after being away | Sitrep | Brief state summary |
| Capture a half-formed idea or observation | Muse | Committed musing + optional plan + sashay |
| Run a data analysis workflow | EDA | `data/` datasets + `docs/data-analysis/` report |
