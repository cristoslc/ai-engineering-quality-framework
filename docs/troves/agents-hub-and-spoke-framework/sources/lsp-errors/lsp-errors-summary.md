---
slug: lsp-errors
relates-to: agents-hub-and-spoke-framework
relevance: Defines how agents handle LSP errors — distinguishing between fixable config issues and pre-existing code errors that should be filed as tech debt.
selected-because: LSP errors are a common source of pre-existing issues that agents encounter; this spoke provides the protocol for triaging and tracking them without scope creep.
aspects-covered:
  - Pre-existing LSP error classification (minor/fast fixes vs significant/complex)
  - Missing or misconfigured LSP config files by language
  - Python pyright + uv venv configuration
  - Troubleshooting workflow for LSP errors
  - Tech debt entry format and lifecycle
  - Running index of deleted tech debt entries
  - Trigger keywords
gaps:
  - Does not address warning/deprecation handling (covered in warnings-and-deprecations)
  - Does not define the debugging ritual for LSP-related runtime errors
  - No guidance on LSP version compatibility or server-specific issues
