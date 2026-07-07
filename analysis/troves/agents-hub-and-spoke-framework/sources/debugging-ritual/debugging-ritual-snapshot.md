---
slug: debugging-ritual
title: Debugging Ritual
type: local
path: ~/.agents/agents-md-detail/debugging-ritual.md
fetched: 2026-07-07T14:00:00Z
---

# Debugging Ritual

This document uses RFC 2119 key words (MUST, MUST NOT, SHOULD, SHOULD NOT, MAY) to indicate requirement levels.

## Core Principle

Whenever you encounter a runtime error, bug, or unexpected failure — whether reported by the user, discovered during development, or surfaced by a tool — your **first action** is to write a test that reproduces the failure. Only then do you implement the fix. The test MUST fail before the fix and pass after it.

This is not optional. It applies to every code change, not just explicit test requests.

## The Ritual: 8 Steps

### Step 1: Reproduce the failure

Before any diagnosis, confirm the failure is real and reproducible. Run the failing code path and capture the exact error output, stack trace, or unexpected behavior. If the failure is intermittent, run it multiple times to understand the pattern.

### Step 2: Write the failing test (MANDATORY)

Write a test that reproduces the failure **immediately**, before you understand the root cause. The test MUST:
- Fail before the fix is applied (confirm by running it)
- Pass after the fix is applied
- Be committed as part of the change

This is the most important step. It captures the bug at the symptom level and protects against regression. Do not skip it even if you think you know the fix.

### Step 3: Isolate the root cause

Use binary search, logging, or targeted probes to find the exact location and mechanism of the failure. Do NOT jump to a fix — understand what is wrong first.

### Step 4: Write additional tests (MANDATORY)

Now that you understand the root cause, write tests that cover:
- **Edge cases** at the boundary of the fix (off-by-one, empty, max-length, zero-value)
- **Related paths** that share the same code path or assumption that was wrong
- **Corner cases** where multiple conditions combine to trigger the same root cause
- **Inverse assertions** — tests that expect the correct behavior to fail under invalid conditions

These tests MUST fail before the fix and pass after it, just like the reproduction test. They protect against shallow fixes that only address the reported symptom.

### Step 5: Implement the fix

Now change the code. Make the minimal change needed to pass all tests. Do not refactor unrelated code in the same change.

### Step 6: Verify all tests pass

Run the reproduction test and all additional tests — they MUST all pass. If any don't, your fix is incomplete or incorrect.

### Step 7: Run the full test suite

Run the project's full test suite to verify no regressions. All tests MUST pass before declaring completion.

### Step 8: Commit with discipline

The tests and the fix belong in the same commit. The commit message SHOULD reference the error or issue being fixed. If the tests are large or the fix is complex, consider two commits: one adding all tests, one implementing the fix.

## Catch yourself before you fix

The most common failure mode: the agent diagnoses a bug and jumps straight to implementation, skipping the test that would prove the fix works. This produces correct fixes that are not protected against regression.

Recognize the pattern. If you catch yourself writing any of these phrases — in your thinking, in a user-facing response, or in a commit message — STOP and write the failing test first:

- "The fix is to add X"
- "I'll add X to fix this"
- "I'll patch Y"
- "I'll change Z"
- "I'll update Y to resolve this"
- "I need to change Z"
- "To resolve this, I need to..."
- "The solution is..."
- "Root cause is..."
- "The issue is..."
- "The problem is..."
- "Missing: X"
- "Doesn't exist: X"
- "Should be X"

The pattern is: **diagnosis followed by implementation intent, with no test in between.** The test comes before the implementation, always.

> **A test failure is a test failure; you either fix it or decide it's testing the wrong thing.**

**No rationalizing test failures.** A test failure is a test failure. You MUST NOT treat a failing test as "signal," "evidence," "indication," or any other euphemism for an unresolved problem. You either fix it or consciously decide it's testing the wrong thing — there is no third option. Common rationalization patterns to catch yourself on: "X/Y passing is evidence the fix worked for the original failure mode," "case Z is a separate issue," "below threshold," "accept X/Y as signal," "iterate on the prompt instead of fixing the test."

## Debugging techniques

### Binary search (git bisect)

When a regression is introduced across multiple commits, use `git bisect` to find the exact commit that introduced the bug. Write a test script that reproduces the failure and let bisect find the culprit.

### Add assertions, not print statements

When tracing through code, add assertions that document your assumptions rather than print/log statements. An assertion that fails is a test case waiting to be written. A print statement scrolls past and is forgotten.

### Reproduce in isolation

Before debugging in the full system, try to reproduce the failure in a minimal, isolated environment — a unit test, a standalone script, or a reduced input. This eliminates confounding factors and speeds up the fix-verify cycle.

### Check the obvious first

Before deep investigation, check:
- Is the input what you expect? (type, shape, bounds)
- Is the error handling path itself buggy?
- Is there a race condition or timing dependency?
- Is the environment different from what the code assumes?

## Triggers

User symptoms: `error`, `bug`, `failure`, `broken`, `crash`, `doesn't work`, `not working`, `fails`, `failed`, `runtime error`, `Traceback`, `Exception`, `AssertionError`, `panic`, `fatal`.

Agent diagnostic / fix-intent language: `the fix is`, `I'll add`, `I'll change`, `I'll patch`, `I'll update`, `I'll fix`, `I'll modify`, `I'll replace`, `I need to add`, `I need to change`, `I need to fix`, `to resolve this`, `the solution is`, `root cause`, `the issue is`, `the problem is`, `missing`, `doesn't exist`, `should be`.

Wrong-output / regression language: `regression`, `wrong output`, `incorrect`, `unexpected result`, `not supposed to`, `used to work`.
