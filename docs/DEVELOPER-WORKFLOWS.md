# Developer Workflows

## Build

No build step — this is a pure documentation project.

## Test

```
echo "No automated tests — this is a documentation/analysis project"
```

Validation is handled by:
- `mmdc` for Mermaid diagram verification
- markdownlint for prose quality

## Deploy

No deployment — this is a documentation artifact. See `scripts/staging/README.md` for the staging exemption.

## Local Development

1. Clone the repository
2. Edit markdown files
3. Run `mmdc` to verify any Mermaid diagrams before committing
4. Commit and push
