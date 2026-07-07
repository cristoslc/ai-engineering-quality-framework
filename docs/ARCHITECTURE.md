# Architecture

This project is a documentation and analysis artifact. It has no runtime components.

## C4 System Context

The system under analysis is the `~/.agents/AGENTS.md` hub-and-spoke framework — a set of markdown documents that govern AI agent behavior during software engineering tasks.

```mermaid
flowchart TB
    operator["Operator (Human)"]
    agent["AI Agent"]
    framework["~/.agents/ Hub-and-Spoke Framework"]
    codebase["Project Codebase"]
    industry["Industry QA/QC Standards"]

    operator -->|"issues tasks"| agent
    agent -->|"reads rules from"| framework
    agent -->|"modifies"| codebase
    framework -->|"analyzed against"| industry
```

## Technical Stack

This project is pure documentation:
- **Format:** Markdown with Mermaid.js diagrams
- **Version Control:** Git
- **Validation:** `mmdc` for diagram verification, markdownlint for prose
