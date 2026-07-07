---
slug: cove
relates-to: agents-hub-and-spoke-framework
relevance: Documents the local developer platform (Forgejo, Vault) that the hub-and-spoke framework may use for git operations, credential management, and CI — providing the infrastructure context for subagent execution.
selected-because: The hub-and-spoke framework operates within the Cove ecosystem; this spoke defines the forge CLI (fj), vault credential protocol, and git remote conventions that subagents must follow.
aspects-covered:
  - Forgejo web and CLI access
  - Vault credential management (vault-get, vault-put, batch-pull)
  - Cove CLI commands (up, down, install, uninstall)
  - Service architecture (nginx, dnsmasq, Tailscale)
  - TLS setup (mkcert, Tailscale cert)
  - Git remote conventions (origin + forge-specific remotes)
  - New project setup workflow
  - Offline-first constraints
gaps:
  - Does not cover CI pipeline configuration
  - Does not address Cove Pages or Registry services
---
