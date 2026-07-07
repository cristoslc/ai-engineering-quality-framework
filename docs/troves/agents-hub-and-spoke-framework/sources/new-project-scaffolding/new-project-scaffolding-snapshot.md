---
slug: new-project-scaffolding
title: New Project Scaffolding
type: local
path: ~/.agents/agents-md-detail/new-project-scaffolding.md
fetched: 2026-07-07T14:00:00Z
---

# New Project Scaffolding

Seed every new project with the following files.

## Hub-and-spoke upper-case files

The `docs/` tree is split into **two architectural surfaces** — domain architecture (DDD artifacts) and technical architecture (C4, deployment, tech stack). They have different audiences and evolve on different cadences; keep them in separate directories so they don't drift into each other.

| File / Dir | Surface | Purpose |
|---|---|---|
| `docs/ARCHITECTURE.md` | technical | C4 model references (context → container → component → deployment), tech stack per container, deployment topology, mermaid C4 + sequence diagrams. DDD does NOT live here — see domain surface below. |
| `docs/technical-architecture/` | technical | Spoke directory for `ARCHITECTURE.md`. Holds C4 diagrams (`c4/{context,container,component,deployment}.md`), shared tech-stack detail (`tech-stack.md`), infrastructure decisions. |
| `docs/DOMAIN-MODEL-L1.md` | domain | L1 prose glossary of domain terms, aggregate narratives, business rules. Uses Mermaid `flowchart` for conceptual groupings. Readable by domain experts and non-technical stakeholders. Sits at the surface — no hub file above it. |
| `docs/domain-architecture/` | domain | Spoke directory for the domain surface. Holds `CONTEXT-MAP.md` (BC relationships), `DOMAIN-EVENTS.md` (cross-context integration events), `DOMAIN-MODEL-L2.md` (L2 ubiquitous language with ERDs and invariants), and `events/` (event contract YAML specs). |
| `docs/DEVELOPER-WORKFLOWS.md` | cross-cutting | Build, test, deploy, CI/CD, release, local dev |
| `docs/USER-EXPERIENCE.md` | cross-cutting | UI conventions, design patterns, user journeys, accessibility |
| `AGENTS.md` | cross-cutting | Project-specific agent guidance, includes PURPOSE.md reference |

The legacy single-architecture layout (`docs/architecture/` holding both C4 and DDD, plus `docs/UBIQUITOUS-LANGUAGE.md` and `docs/TECH-STACK.md` hubs) is no longer scaffolded. Existing projects on that layout should migrate — see `~/.agents/agents-md-detail/context-map-domain-model.md` for the L1/L2 zoom split and the canonical domain surface shape.

### Hub file conventions

Hub files live under `docs/` alongside their spoke directories for colocation and cleanliness. Each hub file should:
- Be concise (1-3 paragraphs per topic, pointers to detail files)
- Index the spokes in its `docs/` subdirectory
- Be readable at a glance — an agent or human should grok the shape from the hub alone
- Stay inside one surface. A `docs/ARCHITECTURE.md` hub that mixes C4 (technical) with bounded contexts (domain) is a smell — split it.

### Contents guidance per hub

**`docs/ARCHITECTURE.md`** (technical surface)
- C4 model references (context → container → component → code → deployment)
- Per-container tech stack (e.g. "the API container uses Python + FastAPI + Postgres, the worker container uses Node + Redis")
- Deployment topology: where each container runs, network boundaries, mount topology
- Key mermaid diagrams (C4 levels, sequence diagrams for cross-container flows)

**`docs/DOMAIN-MODEL-L1.md`** (domain surface, at the top of `docs/`)
- Prose glossary of domain terms organized by bounded context
- Aggregate narratives (what each aggregate represents, in prose)
- Business rules expressed in natural language
- Mermaid `flowchart` diagrams showing conceptual groupings and data flow direction (no formal ERD, no cardinality, no attribute tables at L1)
- "Avoid" notes for frequently misused synonyms
- Stable vocabulary — ephemeral decisions go in ADRs instead

**`docs/domain-architecture/CONTEXT-MAP.md`** (domain surface)
- Mermaid `flowchart` of bounded contexts and their relationships
- Relationship types: upstream/downstream, partnership, shared kernel, conformist, anticorruption layer, open host service / published language

**`docs/domain-architecture/DOMAIN-EVENTS.md`** (domain surface)
- Integration events that cross bounded context boundaries
- Each event records producing context, consuming context(s), payload shape, delivery mechanism
- Internal domain events (within a single BC) belong in that BC's `README.md` leaf, not here

**`docs/domain-architecture/DOMAIN-MODEL-L2.md`** (domain surface)
- L2 ubiquitous language for developers and agents
- Mermaid ERD diagrams with cardinality, attribute tables, event tables, invariants, aggregate boundaries
- Entities, value objects, aggregates, repositories, services, factories
- Domain events (internal events only; cross-context events go in `DOMAIN-EVENTS.md`)

**`docs/technical-architecture/tech-stack.md`** (technical surface, spoke)
- Overarching language versions and toolchain (e.g. Python 3.12, Node 22)
- Framework/library selections that span across containers
- Infrastructure: hosting provider, CI/CD platform, container runtime, observability stack
- Database, message queue, cache decisions (the shared infrastructure, not per-container assignments)

**`docs/technical-architecture/c4/`** (technical surface, spoke)
- `context.md` — system context (actors, external systems, the system under design)
- `container.md` — runtime containers / processes
- `component.md` — components inside a container
- `deployment.md` — deployment topology and environments

**`docs/DEVELOPER-WORKFLOWS.md`**
- Build: `npm run build`, `cargo build`, etc.
- Test: `pytest tests/`, `go test ./...`, which suites to run
- Deploy: staging vs production, release branching, tags
- CI/CD: where pipelines live, how to trigger them
- Local dev: hot-reload, environment setup, secrets

**`docs/USER-EXPERIENCE.md`**
- Installation and onboarding flow (download, setup, first run, configuration)
- UX Principles — key design promises baked into the product fabric (e.g. "no config needed", "works offline", "results in under 1s")
- Quality Attributes — measurable non-functional requirements (performance, availability, deployability, etc.)
- UI conventions and design system references
- User journey maps or flow diagrams
- Accessibility targets and testing approach
- Key interaction patterns (search, navigation, forms, etc.)

**`AGENTS.md`**

- Project-specific agent guidance
- Must include/include-ref of `PURPOSE.md`
- Spokes go in `docs/agents-detail/`

### Default AGENTS.md spoke: project navigation

Every scaffolded project should get a `docs/agents-detail/project-navigation.md` spoke that tells agents how to orient themselves:

```markdown
# Project Navigation

## Starting points

- `PURPOSE.md` — one-paragraph outcome
- `README.md` — user-facing landing page
- `AGENTS.md` — this file, plus `docs/agents-detail/` for agent-specific guidance

## Hubs and spokes

The `docs/` tree splits into two architectural surfaces plus cross-cutting hubs:

Technical architecture (how the system is built and deployed):
- `docs/ARCHITECTURE.md` → `docs/technical-architecture/`
- `docs/technical-architecture/c4/{context,container,component,deployment}.md`
- `docs/technical-architecture/tech-stack.md`

Domain architecture (what the system means to its domain experts):
- `docs/DOMAIN-MODEL-L1.md` — L1 prose glossary (at the surface, no hub above it)
- `docs/domain-architecture/CONTEXT-MAP.md` — bounded context relationships
- `docs/domain-architecture/DOMAIN-EVENTS.md` — cross-context integration events
- `docs/domain-architecture/DOMAIN-MODEL-L2.md` — L2 ubiquitous language (ERDs, invariants)
- `docs/domain-architecture/events/` — event contract YAML specs

Cross-cutting:
- `docs/DEVELOPER-WORKFLOWS.md` → `docs/developer-workflows/`
- `docs/USER-EXPERIENCE.md` → `docs/user-experience/`
- `AGENTS.md` → `docs/agents-detail/`
- `docs/adr/` — numbered decision records (no hub file)
- `docs/plans/` — implementation plans and specs (no hub file)
   - `docs/musings/` — pre-artifact thought capture (no hub file)
   - `docs/retros/` — post-session reflection docs from the Retro ritual (no hub file)
   - `docs/tech-debt/` — pre-existing LSP errors and other tech debt (no hub file)

Read the hub first, then drill into spokes when you need detail.
All docs directories (`docs/`, `docs/technical-architecture/`, `docs/domain-architecture/`, `docs/developer-workflows/`, `docs/user-experience/`, `docs/adr/`, `docs/plans/`, `docs/musings/`, `docs/retros/`, `docs/tech-debt/`, etc.) have a `README.md` explaining that directory's contents and purpose — start there when entering a new directory.
```

## Standalone files (no spoke dir)

- `AGENTS.md` – project-specific guidance for agents, supplements/supersedes `~/.agents/AGENTS.md`
- `PURPOSE.md` — max 1 paragraph (5 sentences) about intent and outcome
- `README.md` — new-user landing page, informally an outcome-driven spec
- `CHANGELOG.md` — keep-a-changelog format with Unreleased section; every PR adds its entry here

## Other requirements

- `.gitignore` — covers Python, Node, Go, Rust, Flutter, Swift, macOS, Linux, Windows defaults, plus `.worktrees/`
- `.worktrees/` — directory in project base dir for all worktrees
- `scripts/staging/` — scripts for staging deploy (`deploy.sh`), E2E testing (`e2e.sh`), and teardown (`teardown.sh`), using branch name as staging identity. See `~/.agents/agents-md-detail/two-stage-pipelines.md` for the full mandate and script conventions.
- `docs/adr/` — architecture decision records, numbered files (e.g. `0001-decision-title.md`) with context, options, and rationale
- `docs/musings/` — pre-artifact thought capture. Use this for raw ideas, half-formed concepts, exploratory notes, and fragments that may later coalesce into ADRs, specs, spikes, or other artifacts. Files here follow no fixed format — they are freeform markdown. The directory name signals content maturity: musings are less structured than plans, which are less structured than ADRs.
- `docs/retros/` — post-session reflection docs produced by the Retro ritual. One file per retro, dated and topic-slugged (`YYYY-MM-DD-{topic-slug}.md`). Structured: what happened, what worked, what didn't, what surfaced, next actions. See `~/.agents/agents-md-detail/workflow-rituals/retro.md` for the full ritual protocol.
- `docs/musings/AGENTS.md` — thin pointer to the Muse ritual.
- Every `docs/` subdirectory gets a `README.md` explaining its contents and purpose, including `docs/` itself. Content guidance per location:

  | Directory | Surface | README should cover |
  |---|---|---|
  | `docs/` | both | Overview of all spoke directories grouped by surface (technical vs domain vs cross-cutting), link to AGENTS.md, link to PURPOSE.md |
  | `docs/technical-architecture/` | technical | Purpose (C4 diagrams, deployment topology, tech stack detail), link to ARCHITECTURE.md hub |
  | `docs/technical-architecture/c4/` | technical | Purpose (C4 level diagrams — context, container, component, deployment), link to ARCHITECTURE.md hub |
  | `docs/domain-architecture/` | domain | Purpose (DDD context map, cross-context events, L2 ubiquitous language, event contracts), link to DOMAIN-MODEL-L1.md at the surface |
  | `docs/domain-architecture/events/` | domain | Purpose (event contract YAML specs), link to DOMAIN-EVENTS.md for cross-context event narrative |
  | `docs/developer-workflows/` | cross-cutting | Purpose (build, test, deploy, CI/CD), link to DEVELOPER-WORKFLOWS.md hub |
  | `docs/user-experience/` | cross-cutting | Purpose (UI conventions, journeys, accessibility), link to USER-EXPERIENCE.md hub |
  | `docs/adr/` | cross-cutting | Purpose (numbered decision records), numbering convention, lifecycle (proposed → adopted → superseded) |
  | `docs/plans/` | cross-cutting | Purpose (implementation plans), lifecycle rules — see `~/.agents/agents-md-detail/locations/plans.md` for full plan management policy, and `~/.agents/agents-md-detail/workflow-rituals/pr-sashay.md` for sashay-context deletion criteria |
  | `docs/musings/` | cross-cutting | Purpose (pre-artifact thought capture), AGENTS.md requirement (commit and push immediately), lifecycle rules — see `~/.agents/agents-md-detail/locations/musings.md` for deletion policy |
  | `docs/retros/` | cross-cutting | Purpose (post-session reflection docs from the Retro ritual), file naming (`YYYY-MM-DD-{topic-slug}.md`), lifecycle — see `~/.agents/agents-md-detail/workflow-rituals/retro.md` for the ritual protocol |
  | `docs/tech-debt/` | cross-cutting | Purpose (pre-existing LSP errors, lint violations, type errors, and other tech debt too complex to fix inline), lifecycle rules — see `~/.agents/agents-md-detail/lsp-errors.md` for entry format, deletion policy, and running index |
  | `docs/agents-detail/` | cross-cutting | Purpose (project-specific agent spoke guidance), relationship to AGENTS.md hub |

Git init and offer to create a repo via `gh` or `fj`. Remotes named after their CLI tool. Forgejo has create-on-push enabled.

## LSP config files

During scaffolding, create the appropriate LSP config file for the project's language(s). This ensures the LSP works correctly from the start and avoids "missing config" errors during development.

| Language | Config file | Default content |
|----------|------------|-----------------|
| Python | `pyrightconfig.json` | `{ "venvPath": ".", "venv": ".venv" }` (or add `[tool.pyright]` to `pyproject.toml`) |
| TypeScript / JavaScript | `tsconfig.json` | Basic strict config with `ES2022` target, `bundler` module resolution |
| Go | `go.mod` | Created by `go mod init <module>` |
| Rust | `Cargo.toml` | Created by `cargo init` |
| C / C++ | `compile_commands.json` | Generated by build system (CMake, etc.) |
| Java | `pom.xml` or `build.gradle` | Standard Maven/Gradle project file |
| C# | `.csproj` | Created by `dotnet new` |
| Dart | `pubspec.yaml` | Created by `dart create` |
| Elixir | `mix.exs` | Created by `mix new` |
| Haskell | `hie.yaml` | Basic cradle config |
| Swift / ObjC | `Package.swift` | Created by `swift package init` |
| Zig | `build.zig` | Created by `zig init` |
| Gleam | `gleam.toml` | Created by `gleam new` |
| Nix | `flake.nix` | Basic flake template |
| Deno | `deno.json` | Basic Deno config |
| Prisma | `schema.prisma` | Basic Prisma schema |
| Terraform | (no config file needed) | — |
| Svelte | `svelte.config.js` | Basic Svelte config |
| Vue | `vue.config.js` or `vite.config.ts` | Basic Vue/Vite config |
| Astro | `astro.config.mjs` | Basic Astro config |
| PHP | `composer.json` | Created by `composer init` |
| Lua | (no config file needed) | — |
| Kotlin | (no config file needed) | — |
| Bash | (no config file needed) | — |
| YAML | (no config file needed) | — |
| Typst | (no config file needed) | — |

For multi-language projects, create config files for each language. See `~/.agents/agents-md-detail/lsp-errors.md` for the full LSP troubleshooting reference.

**Never set `credential.helper` in repo-level `.git/config`.** An empty `credential.helper=` clears the global/system helper cascade, leaving no credential provider at all — GCM silently stops running and all HTTPS pushes fail with "unable to get password from user" or "Device not configured." If a repo-local credential override is truly needed, it must reference a concrete helper (never empty/null).

## Git LFS

Large files MUST go through Git LFS. Two triggers — either is sufficient:

1. **File type:** Binary data files are ALWAYS LFS-tracked regardless of size:
   `.parquet`, `.duckdb`, `.sqlite`, `.db`, `.h5`, `.npy`, `.npz`, `.pkl`, `.feather`, `.arrow`, `.png`, `.jpg`, `.jpeg`, `.gif`, `.bmp`, `.tiff`, `.webp`, `.mp4`, `.mov`, `.avi`, `.mkv`, `.webm`, `.zip`, `.tar`, `.gz`, `.bz2`, `.7z`, `.zst`, `.pdf`
2. **Size limit:** Any file > 50MB MUST be LFS-tracked regardless of type. Hard cutoff — the agent MUST refuse to commit files over 50MB that are not LFS-tracked. Files between 10-50MB get a warning advising LFS.

CSV is NOT LFS-tracked by file type — it is text and diffs are useful. Large CSVs (> 50MB) hit the size rule and MUST be LFS-tracked.

### Scaffolding procedure

1. Scaffold a `.gitattributes` file with default LFS rules:
   ```
   # Binary data formats
   *.parquet filter=lfs diff=lfs merge=lfs -text
   *.duckdb filter=lfs diff=lfs merge=lfs -text
   *.sqlite filter=lfs diff=lfs merge=lfs -text
   *.db filter=lfs diff=lfs merge=lfs -text
   *.h5 filter=lfs diff=lfs merge=lfs -text
   *.npy filter=lfs diff=lfs merge=lfs -text
   *.npz filter=lfs diff=lfs merge=lfs -text
   *.pkl filter=lfs diff=lfs merge=lfs -text
   *.feather filter=lfs diff=lfs merge=lfs -text
   *.arrow filter=lfs diff=lfs merge=lfs -text
   # Images
   *.png filter=lfs diff=lfs merge=lfs -text
   *.jpg filter=lfs diff=lfs merge=lfs -text
   *.jpeg filter=lfs diff=lfs merge=lfs -text
   *.gif filter=lfs diff=lfs merge=lfs -text
   *.bmp filter=lfs diff=lfs merge=lfs -text
   *.tiff filter=lfs diff=lfs merge=lfs -text
   *.webp filter=lfs diff=lfs merge=lfs -text
   # Video
   *.mp4 filter=lfs diff=lfs merge=lfs -text
   *.mov filter=lfs diff=lfs merge=lfs -text
   *.avi filter=lfs diff=lfs merge=lfs -text
   *.mkv filter=lfs diff=lfs merge=lfs -text
   *.webm filter=lfs diff=lfs merge=lfs -text
   # Archives
   *.zip filter=lfs diff=lfs merge=lfs -text
   *.tar filter=lfs diff=lfs merge=lfs -text
   *.gz filter=lfs diff=lfs merge=lfs -text
   *.bz2 filter=lfs diff=lfs merge=lfs -text
   *.7z filter=lfs diff=lfs merge=lfs -text
   *.zst filter=lfs diff=lfs merge=lfs -text
   # Documents
   *.pdf filter=lfs diff=lfs merge=lfs -text
   ```

2. After `git init`, run `git lfs install` (repo-local).

3. **Cove/Forgejo note:** Forgejo LFS is server-side enabled. No special client config needed beyond `git lfs install`. The default LFS endpoint follows the remote. Do NOT configure `lfs.url` explicitly — it resolves automatically.

### Open gaps (documented, not blocking)

1. **CSV handling:** No LFS by file type for CSV. The 50MB size rule catches large CSVs. Operators can override in project `.gitattributes`.
2. **Warning vs block threshold:** 10-50MB is a warning zone (agent advises LFS). Above 50MB is a hard block (agent refuses to commit without LFS). This is agent behavior, not enforced by git.

## Related

Full directory listing conventions: `~/.agents/agents-md-detail/locations/personal-code-projects.md`.

## Triggers

`new project`, `scaffold`, `init`, `AGENTS.md`, `git init`, `git-secrets`, `pre-commit`, `technical-architecture`, `domain-architecture`, `C4`, `bounded context`, `tech stack`, `ubiquitous language`.
