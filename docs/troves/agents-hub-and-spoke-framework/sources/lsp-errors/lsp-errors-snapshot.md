---
slug: lsp-errors
title: LSP Errors
type: local
path: ~/.agents/agents-md-detail/lsp-errors.md
fetched: 2026-07-07T14:00:00Z
---

# LSP Errors

Handling LSP output — errors, warnings, missing config, and misconfigured servers.

## Pre-existing LSP errors

When working on a codebase that has pre-existing LSP errors (type errors, lint violations, etc.):

- **Minor/fast fixes** (single-file, low-risk, clearly correct): fix them in their own commit before the feature/fix commit. The commit message should start with `chore: fix pre-existing LSP errors in <area>`.
- **Significant/complex errors** (multi-file, risky, unclear root cause): add an entry to `docs/tech-debt/` instead. Do not fix them inline — that risks scope creep and introducing regressions.

## Missing or misconfigured LSP config files

Many LSPs require a project-local config file to work correctly. If the LSP is producing errors or not functioning, check whether the required config file exists and is correct. **Fix the config — do not ignore the errors or file them as tech debt.** Missing config is a setup problem, not pre-existing tech debt.

### Default LSPs by language

OpenCode ships with these default LSPs. Each has config requirements:

| Language | Default LSP | Required config |
|----------|------------|-----------------|
| Python | `pyright` | `pyproject.toml` with `[tool.pyright]` or `pyrightconfig.json` |
| TypeScript / JavaScript | `typescript-language-server` | `tsconfig.json` |
| Go | `gopls` | `go.mod` |
| Rust | `rust-analyzer` | `Cargo.toml` |
| C / C++ | `clangd` | `compile_commands.json` |
| Java | `jdtls` | `pom.xml` or `build.gradle` |
| C# | `csharp` | `.csproj` |
| Dart | `dart` | `pubspec.yaml` |
| Elixir | `elixir-ls` | `mix.exs` |
| Haskell | `hls` | `hie.yaml` or `cabal.project` / `stack.yaml` |
| Swift / ObjC | `sourcekit-lsp` | `Package.swift` |
| Zig | `zls` | `build.zig` |
| Gleam | `gleam` | `gleam.toml` |
| Nix | `nixd` | `flake.nix` or `default.nix` |
| Deno | `deno` | `deno.json` or `deno.jsonc` (auto-detected) |
| Prisma | `prisma` | `schema.prisma` |
| Terraform | `terraform` | `*.tf` files |
| Svelte | `svelte` | `svelte.config.js` |
| Vue | `vue` | `vue.config.js` or `vite.config.ts` |
| Astro | `astro` | `astro.config.mjs` |
| PHP | `intelephense` | `composer.json` |
| Lua | `lua-ls` | (auto-installs, minimal config) |
| Kotlin | `kotlin-ls` | (auto-installs, minimal config) |
| Bash | `bash-language-server` | (no config file needed) |
| YAML | `yaml-ls` | (no config file needed) |
| Typst | `tinymist` | (no config file needed) |

### Python: pyright + uv venv

Python projects using `uv` for virtual environment management are a common source of LSP misconfiguration. Pyright needs to know where the venv is.

**If the project uses `uv`**, create or update `pyrightconfig.json` at the project root:

```json
{
  "venvPath": ".",
  "venv": ".venv"
}
```

Or add to `pyproject.toml`:

```toml
[tool.pyright]
venvPath = "."
venv = ".venv"
```

**If the project uses a different venv path** (e.g., `venv/`, `env/`), adjust accordingly.

**If pyright still can't find imports**, check:
1. The venv exists and has dependencies installed (`uv sync` or `uv pip install`)
2. The Python interpreter in the venv matches the project's Python version requirement
3. `pyrightconfig.json` is at the project root (not in a subdirectory)

### TypeScript: tsconfig.json

The `typescript-language-server` requires a `tsconfig.json` at the project root. If missing, create one:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "outDir": "./dist"
  },
  "include": ["src"]
}
```

Adjust `compilerOptions` to match the project's actual requirements.

### Go: go.mod

`gopls` requires a `go.mod` file. If missing, run `go mod init <module-path>`.

### Rust: Cargo.toml

`rust-analyzer` requires a `Cargo.toml`. If missing, run `cargo init`.

## Troubleshooting workflow

When you encounter LSP errors:

1. **Identify the LSP** — check which language server is producing the error (the error message usually includes the LSP name).
2. **Check config** — does the required config file exist? If not, create it. If it exists, check it's correct.
3. **Check the venv/toolchain** — for Python, verify the venv is set up. For Node, verify `node_modules` exists. For Go/Rust, verify the module file.
4. **Fix config problems immediately** — do not file them as tech debt. Missing config is a setup gap, not pre-existing debt.
5. **If the error is a genuine code issue** (type error, lint violation), apply the pre-existing LSP errors rules above.

## Location for tech debt entries

`docs/tech-debt/` in each project.

## Entry format

Each LSP error item is a single markdown file in `docs/tech-debt/`. Name it with a short kebab-case slug describing the issue, e.g. `unused-imports-in-api-layer.md`.

```markdown
# <Title>

- **Area:** <module, package, or component>
- **Severity:** minor | moderate | significant
- **Discovered:** <date> during <context> (e.g. "2026-06-21 during work on auth refactor")
- **Root cause:** <brief explanation of why the error exists>
- **Impact:** <what it blocks or makes harder>
- **Fix approach:** <sketch of what a fix would look like>
- **Risk:** <why it wasn't fixed inline — scope creep, regression risk, unclear ownership>
```

## Lifecycle

1. **Discovery** — agent encounters a pre-existing LSP error that is too complex/risky to fix inline. Creates an entry in `docs/tech-debt/`.
2. **Conversion** — when a sashay or other planned work targets the area, the tech debt entry is converted into a plan under `docs/plans/`. The plan should reference the original tech debt entry.
3. **Deletion** — once the plan is created, delete the tech debt entry from `docs/tech-debt/`. Do NOT wait for the fix to be implemented — the plan is the actionable artifact.
4. **Indexing** — before deleting, add an entry to the running index (see below).

## Running index of deleted entries

`docs/tech-debt/INDEX.md` is a hub file that tracks all entries that have been deleted from `docs/tech-debt/`. It follows a hub-spoke pattern where the hub is the INDEX file and the spoke is git history.

### INDEX.md format

```markdown
# Tech Debt Index

Entries that have been deleted from `docs/tech-debt/` after being converted to plans.

| Entry | Deleted | Converted to plan | Git ref |
|-------|---------|-------------------|---------|
| `unused-imports-in-api-layer.md` | 2026-06-21 | `docs/plans/clean-up-api-imports.md` | `abc1234` |
```

The **Git ref** column points to the commit that deleted the entry. This is the spoke — use `git show <ref>` to retrieve the original file content from history.

### Adding to the index

When deleting a tech debt entry:

1. `git add docs/tech-debt/<entry>.md`
2. `git commit -m "chore: convert tech debt <entry> to plan"`
3. Note the commit hash
4. Add a row to `INDEX.md` with the entry name, date, plan path, and commit hash
5. `git add docs/tech-debt/INDEX.md && git commit --amend` (or a second commit)

### Retrieving a deleted entry

```bash
git show <ref>:docs/tech-debt/<entry>.md
```

## Triggers

`LSP`, `type error`, `lint error`, `pre-existing`, `tech debt`, `tech-debt`, `docs/tech-debt/`, `pyright`, `tsconfig`, `gopls`, `rust-analyzer`, `venv`, `uv`.
