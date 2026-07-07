---
slug: "review-intent-article"
relates-to: "review-intent-vs-review-code"
relevance: "Argues that code review should be replaced with spec-first, test-first, constraint-enforced verification when AI writes most code"
selected-because: "Directly challenges the assumption that code is the right artifact to review — central to the quality framework analysis"
aspects-covered:
  - "Separation of design review from implementation review"
  - "Spec-first BDD with AI as implementation layer"
  - "Executable constraint layer (CI-enforced agent rules)"
  - "Adversarial planning loop replacing design review"
  - "Preconditions: test quality, SAST, canary deployment"
  - "Ship/Show/Ask routing for spec/test/constraint changes"
  - "Proposed experiment: remove code review for one quarter"
gaps:
  - "Does not address non-deterministic testing or pass^k metrics"
  - "Does not address agent behavioral controls (catch-yourself, fail-loud)"
  - "Does not address test tier systems or MTP/LTP coverage matrices"
  - "Does not address worktree discipline or deployment pipeline SPC"
---
