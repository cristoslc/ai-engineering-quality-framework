---
slug: warnings-and-deprecations
title: Warnings and Deprecations
type: local
path: ~/.agents/agents-md-detail/warnings-and-deprecations.md
fetched: 2026-07-07T14:00:00Z
---

# Warnings and Deprecations

Never solve errors by making them less noisy. Suppressing warnings, deprecation notices, or log output is not a fix — it is information hiding.

Every warning falls into one of three categories:

## Actionable — fix it now

Fix the underlying cause. This means: migrate from the deprecated API, update the pinned version, correct the misconfigured setting, or refactor the offending code path. The default answer to every warning is repair, not silence. This is the vast majority of cases.

## Deferred Actionable — plan it, suppress temporarily

The fix is correct and desirable, but the blast radius or effort level makes it impractical in the current change. **Blast radius and effort level are not valid reasons for permanent suppression.** The right response is:

1. **Write a `docs/plans/` file** describing the sustainable fix — scope, steps, affected files, risk assessment.
2. **Suppress with a targeted mechanism** (skip_list, filterwarnings, noqa — whatever the tool provides).
3. **Reference the plan in the suppression comment** so anyone encountering it knows it's tracked.
4. **Include a re-evaluate trigger** — when should this suppression be re-checked (e.g., "after migrating off Python 3.11", "before next major release").

Example:

```yaml
# ansible-lint
skip_list:
  # Deferred: role-name (23 violations). Fix tracked in docs/plans/rename-role-hyphens.md.
  # Re-evaluate after plan is implemented (est. Q3 2026).
  - role-name
```

```python
# Deferred: var-naming refactor (80+ violations). Fix tracked in
# docs/plans/prefix-variables-by-role.md. Re-evaluate after plan is implemented.
warnings.filterwarnings("ignore", category=DeprecationWarning, module="third_party_lib")
```

Suppression without a plan is information hiding. Suppression with a plan is scheduling.

## Not Actionable — suppress permanently with documentation

Explicitly suppress with a targeted mechanism. Only use this when the rule itself is invalid for this codebase — not "too hard," but genuinely wrong or inapplicable. Examples:

Acceptable suppression mechanisms:

| Tool | Mechanism | Example |
|------|-----------|---------|
| Python | `warnings.filterwarnings` | `warnings.filterwarnings("ignore", category=DeprecationWarning, module="third_party_lib")` |
| Python | `pytest.ini` filter | `filterwarnings = ignore::DeprecationWarning:third_party_lib` |
| Ansible | `deprecation_warnings` in cfg | `deprecation_warnings = False` |
| Ansible | `ANSIBLE_DEPRECATION_WARNINGS` env | `export ANSIBLE_DEPRECATION_WARNINGS=False` |
| Node | `--no-deprecation` flag | `node --no-deprecation script.js` |
| Compilers | pragma/attribute | `#pragma GCC diagnostic ignored "-Wdeprecated-declarations"` |

Always accompany the suppression with a code comment explaining why it is not actionable:

```python
# Not actionable: third_party_lib uses a CPython API deprecated in 3.13.
# Upstream fix tracked at https://github.com/example/lib/issues/1234.
# Re-evaluate after upgrading past lib v2.0.
warnings.filterwarnings("ignore", category=DeprecationWarning, module="third_party_lib")
```

## Universal Scope

This applies everywhere: Python, Node, Ansible, macOS, Linux, compilers, linters, package managers — every tool in the stack.

## Noisy-Test Discipline

Make sure you write tests (even if just temporarily) and iterate against the tests. Manual verification can supplement tests, but automated testing MUST be drafted before attempting any fixes. Whenever consistent with the project design, automated testing should be integrated into the project's test process (though it can be a standalone suite, flagged for later integration into normal suites).

## Triggers

`warning`, `deprecation`, `suppress`, `silence`, `noisy`, `ignore_errors`.
