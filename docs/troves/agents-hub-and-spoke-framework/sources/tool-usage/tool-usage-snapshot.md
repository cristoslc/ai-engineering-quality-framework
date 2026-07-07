---
slug: tool-usage
title: Tool Usage — Consolidated Reference
type: local
path: ~/.agents/agents-md-detail/tool-usage.md
fetched: 2026-07-07T14:00:00Z
---

# Tool Usage — Consolidated Reference

## General Rules

When a tool or service is reported as broken, do NOT attempt to use that same tool to diagnose the problem. Use alternative diagnostic approaches (logs, config inspection, API calls, etc.). ALWAYS echo the current date/time (using system functions, don't hard-code the datetime) prior to AND after invoking a tool. Bookending is important to determine time elapsed.

## 1Password CLI (`op`)

Every `op` call MUST be batched under `op run -- bash -c '...'` — never call `op` bare. Full reference: `~/.agents/agents-md-detail/tool-usage/1password.md`.

Trigger on: `1password`, `op`, `credential`, `secret`, `token`, `vault`, `op item`, `op read`, `op run`.

## Shell

Read files with `read`, find with `glob`, edit with `edit`/`write`, search with `grep` — never use `cat`/`find`/`sed`/`awk`/`perl` directly. Every `bash` command MUST start AND end by echoing current date & time. Full reference: `~/.agents/agents-md-detail/tool-usage/shell.md`.

Trigger on: `bash`, `zsh`, `shell`, `cat`, `find`, `ls`, `sed`, `awk`.

## Web Browsing

Prefer `playwright` over `webfetch` for higher-fidelity scrapes. Ask before invoking Chrome debugger. Full reference: `~/.agents/agents-md-detail/tool-usage/web-browsing.md`.

Trigger on: `web`, `browsing`, `scrape`, `playwright`, `webfetch`, `chrome`, `fetch`, `URL`.

## Python

Always use `uv` — never `pip`/`pip3`/`python3 -m pip`. One-off: `uv run --with <pkg> python3 -c "..."`. Full reference: `~/.agents/agents-md-detail/tool-usage/python.md`.

Trigger on: `python`, `uv`, `pip`, `pytest`, `pyright`, `venv`, `package`, `dependency`.

## Git

Use `gh` for GitHub, `fj` for Forgejo (`fj` requires `zsh -i -c`). Never use `git commit --no-verify` autonomously — prompt the user first. Full reference: `~/.agents/agents-md-detail/tool-usage/git.md`.

Trigger on: `git`, `github`, `gh`, `forgejo`, `fj`, `pre-commit`, `git-secrets`, `leak`, `no-verify`, `--no-verify`.

## Work-in-Progress Pull Requests

Draft PRs from plans MUST have a `WIP: ` title prefix and be created in draft status. Full reference: `~/.agents/agents-md-detail/tool-usage/git.md`.

Trigger on: `WIP`, `draft PR`, `work in progress`, `plan PR`.

## `llm` CLI — Bare AI Call

One-shot LLM with no scaffolding — write prompt to temp file, reference with `-f`. All calls logged to SQLite. Full reference: `~/.agents/agents-md-detail/tool-usage/llm-cli.md`.

Trigger on: `llm`, `one-shot`, `classify`, `extract`, `summarize`, `vision`, `pipe`, `fragment`.

## Vision Analysis Dispatch

When a non-vision model needs image evaluation, shell out to `llm` with `kimi-k2.6:cloud` using `-a` for image attachments and `-f` for prompt fragments. Full reference: `~/.agents/agents-md-detail/tool-usage/llm-vision-dispatch.md`.

Trigger on: image analysis, screenshot evaluation, visual comparison, photo description, diagram review, "look at this", `-a` with image attachments.
