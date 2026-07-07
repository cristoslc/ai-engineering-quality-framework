---
slug: worktree-discipline
title: Worktree Discipline
type: local
path: ~/.agents/agents-md-detail/worktree-discipline.md
fetched: 2026-07-07T14:00:00Z
---

# Worktree Discipline

The root directory of a project MUST always be on `main` (or `trunk`). All other branches MUST only exist inside `.worktrees/`. This prevents accidental work on the wrong branch, keeps the root clean, and ensures worktree isolation is the default.

## Pre-checkout hook

Install this `pre-checkout` hook to enforce the rule automatically. It blocks any checkout of a non-main branch in the root directory.

### `pre-checkout` hook script

Save as `.git/hooks/pre-checkout` in any repo that follows this discipline:

```bash
#!/usr/bin/env bash
# Pre-checkout hook: block non-main branch checkouts in root directory.
# Only applies to the main worktree (not nested worktrees).
set -euo pipefail

# Skip if we're inside a worktree (nested worktrees are fine)
if git rev-parse --git-common-dir 2>/dev/null | grep -q '/.worktrees/'; then
    exit 0
fi

# The ref being checked out is the third argument (pre-checkout hook format)
# git pre-checkout hook: <old-ref> <new-ref> <is-branch-checkout>
# For branch checkouts, <new-ref> is the branch name
if [ "${3:-}" != "1" ]; then
    # Not a branch checkout (e.g., file checkout) — allow
    exit 0
fi

NEW_BRANCH="${2:-}"

# Strip "refs/heads/" prefix if present
NEW_BRANCH="${NEW_BRANCH#refs/heads/}"

# Allowed branches (add your trunk branch name here)
ALLOWED_BRANCHES="main trunk master"

for allowed in $ALLOWED_BRANCHES; do
    if [ "$NEW_BRANCH" = "$allowed" ]; then
        exit 0
    fi
done

echo "ERROR: Cannot checkout branch '$NEW_BRANCH' in the root directory."
echo ""
echo "The root directory must stay on main/trunk. To work on this branch:"
echo ""
echo "  1. Return to main:  git checkout main"
echo "  2. Create a worktree:  git worktree add .worktrees/$NEW_BRANCH $NEW_BRANCH"
echo "  3. Work inside:  cd .worktrees/$NEW_BRANCH"
echo ""
echo "If you need to override (e.g., initial setup), use:"
echo "  SKIP_WORKTREE_CHECK=1 git checkout $NEW_BRANCH"
echo ""

exit 1
```

### Installation

```bash
# Install the hook
cp pre-checkout .git/hooks/pre-checkout
chmod +x .git/hooks/pre-checkout
```

### Override

To bypass the hook (e.g., initial repo setup, emergency):

```bash
SKIP_WORKTREE_CHECK=1 git checkout <branch>
```

## Post-checkout recovery

If you accidentally check out a non-main branch in the root (e.g., before the hook was installed):

```bash
# Create a worktree for the branch you're on
BRANCH=$(git rev-parse --abbrev-ref HEAD)
git worktree add ".worktrees/$BRANCH" "$BRANCH"

# Return root to main
git checkout main

# Work inside the worktree
cd ".worktrees/$BRANCH"
```

## Triggers

`worktree discipline`, `root branch`, `main only in root`, `pre-checkout hook`, `worktree isolation`, `SKIP_WORKTREE_CHECK`.
