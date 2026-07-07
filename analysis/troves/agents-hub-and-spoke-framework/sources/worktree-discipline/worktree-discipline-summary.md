---
slug: worktree-discipline
relates-to: agents-hub-and-spoke-framework
relevance: Defines the git worktree discipline that keeps the root on main/trunk, which is the branch management convention used by the hub-and-spoke framework's sashay workflow.
selected-because: Worktree discipline is the git workflow convention that the hub-and-spoke framework relies on for branch isolation, making it essential context for understanding how agents manage parallel work.
aspects-covered:
  - Root directory must stay on main/trunk
  - Pre-checkout hook script and installation
  - Hook override mechanism (SKIP_WORKTREE_CHECK)
  - Post-checkout recovery procedure
gaps:
  - Does not cover worktree lifecycle management (cleanup, pruning)
  - No guidance on multiple simultaneous worktrees
  - Does not address CI/CD integration with worktrees
---
