---
slug: review-intent-article
title: "Stop Reviewing Code. Start Reviewing Intent. | Thoughts and code"
type: web
url: "https://claydon.co/posts/stop-reviewing-code-start-reviewing-intent/"
fetched: "2026-07-07T17:29:22Z"
---

[~](/)/[thoughts](/posts/)/
[Stop Reviewing Code. Start Reviewing Intent.](/posts/stop-reviewing-code-start-reviewing-intent/)

# Stop Reviewing Code. Start Reviewing Intent.

July 6, 2026
  ·  9 min read

[#Ai-Tooling](https://claydon.co/tags/ai-tooling/)
[#Code-Review](https://claydon.co/tags/code-review/)
[#Sdlc](https://claydon.co/tags/sdlc/)
[#Draft](https://claydon.co/tags/draft/)

Most of the code I ship is written by AI, and every line of it is still reviewed by a human. I’ve stopped believing those two facts are compatible.

The review queue keeps growing, the wait times keep growing with it, and developer sentiment about the whole process keeps sliding in the same direction. When I raise this, the proposed fixes are always the same: more reviewers, better tooling, an AI pre-review pass to lighten the load. They all share an assumption I think is wrong. They treat code as the thing to be reviewed, and that assumption is the thing that keeps breaking.

So here’s the experiment I want to run, and this post is me thinking it through in public: pick one well-bounded service, owned by one willing team, and remove mandatory code review from it for a quarter. Replace it with verification that actually holds: human-authored specifications, tests written before any code exists, and constraints enforced in CI rather than in prose nobody reads. The rest of this post is the part where I explain why that’s less reckless than it sounds.

## Code review is two jobs we’ve never separated [#](#code-review-is-two-jobs-weve-never-separated)

PR-level review does two things that have always been tangled together. One is pressure-testing design decisions: is this the right approach? The other is catching implementation errors: does this approach do what it’s supposed to? We have ADRs and RFCs for the big design decisions, but plenty of smaller design work still gets relitigated in review comments after the code exists, which is too late to be useful and just a little too late to be anything other than friction.

When AI writes most of the code, applying human attention to implementation errors becomes a losing position. Dave Farley made this point at AI DevCon this year: no reviewer can meaningfully audit 12,000 lines of code a day. I believe him. The volume has simply outgrown what humans can engage with seriously. What that pressure produces is rubber-stamping, and rubber-stamping is worse than no review because it manufactures assurance nobody actually earned. I wrote about the [motion-versus-progress problem](/posts/measuring-ai-impact) before: we accelerated code generation without building the verification infrastructure to match it, and the gap shows up as queue time and declining trust in the process.

## The model [#](#the-model)

Humans write specifications and tests, with AI assistance if they like; human judgment is the requirement, not human typing. AI writes the code. The code is not reviewed.

| Artifact | Author | Review required | What it encodes |
| --- | --- | --- | --- |
| Specification (BDD / acceptance criteria) | Human | Yes | What the system must do |
| Tests | Human | Yes | What “done” means |
| Constraint layer (agent rules, CI gates) | Human | Yes, like production code | What agents are not allowed to do |
| Code | AI | No | An implementation satisfying the above |

Canary deployment is the runtime safety net. When something wrong gets through, you roll back in minutes rather than finding out in the next sprint retrospective.

## People who already stopped reviewing code [#](#people-who-already-stopped-reviewing-code)

Paul Stack runs a five-person company that banned handwritten code in January 2026, not as an experiment but as policy. In the following 30 days they shipped 217 issues with a median triage-to-ship time of 1.6 hours, for about $3,000 a month in tooling. [His explanation is three words](https://www.youtube.com/watch?v=wuGJNWhUOoE&list=PLISstAySqk7KtlYPFps1ZnK9xNIMSVkDD&index=35): “Vibes don’t scale.” (A sentiment [I’ve endorsed before](/posts/no-vibes).) His agent instructions file is what he calls an executable contract: a specification of what the agent is allowed to do, what patterns it must follow, what it should refuse. Design decisions get pressure-tested in an adversarial planning loop before any code exists. A planning agent proposes, a security agent attacks the plan, and if they can’t agree after five rounds a human steps in to arbitrate. The bottleneck at his company is deciding what to build, and I’d argue that’s the bottleneck every software organisation should aspire to have.

[Dave Farley closed AI DevCon](https://www.youtube.com/watch?v=libNzUdL9eM&list=PLISstAySqk7KtlYPFps1ZnK9xNIMSVkDD&index=38) with the argument I found most useful. A test generated from existing code can only verify that the code stays the same. It cannot verify the code was ever correct, and it inherits whatever bugs were already there, which is a polite way of saying your test suite has cosigned the existing codebase and filed it under coverage.

His example is hard to argue with: if `calculateTax` returns `50 * amount`, any AI generating tests from that function will confirm that tax is fifty times the input. The tests pass, the system is wrong, and somewhere in the CI pipeline a green checkmark is quietly congratulating itself. You’ve paid for confidence you were not owed.

His alternative is to write BDD-style executable specifications before any code exists, then let AI generate code that satisfies them. The engineer’s job shifts from writing precise solutions to writing precise problem descriptions. It’s TDD with AI as the implementation layer, and if you’ve been practicing test-driven development, you’re already most of the way there.

## The preconditions [#](#the-preconditions)

This model has preconditions, and I want to name them before anyone agrees to try it, because they’re not preliminary steps you complete before the real work starts. They’re the difference between a different approach to verification and simply removing the one safety check you currently have and calling it progress.

**The constraint layer must be executable, not documentary.** Most agent instruction files describe projects. What they need to do is constrain agents, with enforcement in CI rather than in prose. If your codebase requires a tenant identifier on every database query, that’s a lint rule that blocks merge, not a paragraph in a markdown file. And when an agent violates a constraint, the CI error message needs to be written for the agent, not for an engineer who already knows the codebase. “sql: constraint violation” does nothing. “Tenant field is required on all database queries; see the database conventions doc” does something, because it lands in the generation loop where the agent can act on it.

**Test quality has to come first.** Under this model, passing tests is the definition of correct, so a weak test suite is a foundation you cannot build on and fix later. The obvious counter is that the same engineers producing low-quality tests today are the ones who’d be writing the specs, and that’s a real sequencing risk. It’s also why test quality is the first gate before the review gate comes down, and why it needs a number rather than a vibe. A coverage floor on the pilot service, hit by writing tests spec-first rather than retrofitting them against existing code, is the setup work for the whole experiment. Tests written against implementations are precisely the antipattern Farley describes, and a test that passes against a thin fixture is not evidence of anything except that the fixture is thin.

**SAST before scope expansion.** Functional tests don’t catch injection vulnerabilities, secret leakage, or auth bypass. Human reviewers catch some of these, inconsistently, but they do catch them. Any service running without code review and without static analysis has a gap, and in regulated industries gaps have a way of becoming case studies. The expansion plan follows SAST coverage, not the other way around.

**The adversarial planning loop replaces design review.** A planning agent proposes, an adversarial agent attacks on security, correctness, and codebase-specific invariants, and a human arbitrates if they can’t agree after a bounded number of rounds. Design decisions get real scrutiny before implementation starts, which is where scrutiny belongs. If you’ve built [multi-agent code review](/code/temporal-multi-agent-pr-review), this is where it earns its keep: in the planning loop rather than post-merge, catching bad decisions before they become bad code. The one case that skips the loop entirely is any decision that would produce an ADR if a human made it (a new dependency, a new service pattern, a change to a shared interface), because the loop exists to pressure-test implementations, not to make architectural decisions that haven’t been made yet.

**The canary phase has to mean something.** Progressive deployment infrastructure is common; promotion gates that actually catch regressions are less so. A canary that promotes on silence is a slow full deployment, not a safety net.

## Where the review pressure goes [#](#where-the-review-pressure-goes)

Removing code review only works if the artifacts that replace it have clear routing rules. Without them, review pressure migrates up the stack and you’ve rebuilt the same bottleneck, just higher up and with more expensive people in the queue. [Martin Fowler’s Ship/Show/Ask framework](https://martinfowler.com/articles/ship-show-ask.html) maps onto the artifacts cleanly.

Constraint changes ship when they encode an already-agreed convention, show when they’re new but unambiguous, and ask before being written when they change what’s currently allowed. Specifications show by default for purely technical work and ask before writing for anything user-facing or crossing a service boundary. Tests ship when they implement a spec that already went through review, show when they surface an edge case the spec missed, and ask when they change acceptance criteria for existing behavior, because that’s a product decision wearing a test’s clothing.

The failure modes are worth naming too. Show becomes post-facto Ask when someone merges a spec, receives five comments, and feels obligated to block on consensus; Show invites comment, not consensus, and the author decides what to do with the feedback. And Ask-before-writing becomes a queue when every spec requires a synchronous conversation before work starts, at which point the bottleneck has moved from code review to spec approval, which is a lateral move rather than progress.

## The experiment [#](#the-experiment)

The shape I’d propose: one quarter of setup, one quarter of running it live.

Setup means choosing a service with single-team ownership, active development to measure against, and no regulatory requirement for human review. It means raising test quality to where the tests can carry the argument, rewriting the service’s agent instructions as enforceable CI gates with agent-readable errors, and verifying the canary gates catch regressions rather than counting seconds. None of this is glamorous, but skipping it is just removing code review.

Then the review gate comes down. New work starts with a BDD specification signed off by the tech lead and product owner before any code exists. From that spec, humans write the tests; from those tests, AI writes the code. PRs remain visible and open to async comment, but no approval is required. The merge gate stays: CI green, constraint layer passes, all tests pass, SAST clean. What goes away is the approval step, not the gate.

Measure defect ratio, review wait time reclaimed, and the pilot team’s own sentiment about whether verification feels stronger or weaker than what it replaced. If defect ratio holds and the team trusts the process more than the old one, expand to a second service. If defect ratio rises, the diagnostics will be specific: weak specs, thin fixtures, incomplete constraints, or a SAST gap. Reinstating code review without that diagnosis is just a concession that the preconditions were never met.

Reading output was always a strange way to verify correctness; we did it because for most of software history there was nothing else standing between an engineer’s intentions and production. Now there can be. Specs, tests, and constraints can hold the line better than a tired reviewer skimming their ninth PR of the day, but only if we build them like we mean it. That’s what the experiment is for. If it works, the data will show it, and if it doesn’t, I’d rather have evidence than an argument.

---

←

[Measuring AI Impact: The Motion vs. Progress Problem](/posts/measuring-ai-impact/)

---

[back to top](#top)
