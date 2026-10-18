---
name: create-tdd-tests
description: >-
  Write failing Vitest TDD tests from an approved PLAN.md or from a GitHub issue
  (or part of one). Use when the user asks for TDD tests, red tests, implement
  tests from PLAN.md, or issue-scoped TDD without building the feature yet.
---

# Create TDD Tests

Vitest harnesses, mocks, and fixtures are defined in the **current repository** (for example `.cursor/rules/testing.mdc`). Follow those for setup, module mocks, and shared utilities.

**Scope:** Implement **tests only**. Do not implement the feature or fix production code to make tests pass unless the user explicitly asks.

TDD tests **are expected to fail** until the feature exists. After writing them, note what production changes are needed to go green (functions, branches, wiring).

If behavior **already exists** in the codebase, use **create-tests** instead (tests should pass, not stay red).

## Choose a path

| Trigger | Path |
| ------- | ---- |
| User references `@PLAN.md`, “implement the plan”, or a plan path | **From PLAN.md** |
| User gives an issue number or issue-driven TDD with no plan | **From GitHub issue** |

If both apply, **From PLAN.md** wins — the user may have edited the plan after the issue was written.

## Test file structure

Spec files usually **nest** `describe` blocks. Match the target spec; do not assume each function is a root-level `describe`.

- **Module / file** — Outer `describe` names the file or module (for example `describe('calculate', () => { ... })`).
- **Function or unit** — Inner `describe` per **exported function** or per **single interactive unit** on a component. New TDD cases for one function live in that function’s `describe`, inside the module wrapper when one exists.
- **Scenarios** — Further nesting for related cases (auth state, edge cases, error handling).

When implementing from `PLAN.md`, use the **describe hierarchy** in the plan (file → function/unit → scenario). Align with nearby spec files in the project.

## From PLAN.md

1. **Read the current `PLAN.md` in full** — User-edited content is authoritative. Do not rely on chat history or an older version of the plan.
2. **Do not re-plan** — Implement each planned case (describe hierarchy, setup, actions, assertions). Do not edit `PLAN.md` unless the user asks.
3. **Issue is optional context** — Only `gh issue view` when the plan or user references an issue and you need extra background. Do not let the issue override cases or wording in `PLAN.md`.
4. **Spec file** — Use the target path from the plan; create the file if needed.
5. **Reference patterns** — Use pattern references from the plan; fill gaps from comparable specs in the repo.
6. **Write failing tests** — One implementation pass per planned case; failures should reflect missing product behavior, not broken harness.
7. **Run tests** — Confirm expected failures; summarize required production work (from plan notes and what you observed).

If the user scoped implementation to **part of the plan**, implement only those sections and say so in chat.

## From GitHub issue

1. **Load requirements** — `gh issue view <number> --comments` (and body). If the user scoped a **portion** of the issue (single AC, section, or component), test only that slice and say so in chat.
2. **Locate or choose spec file** — Co-located `__tests__` or `*.spec.ts(x)` per project convention.
3. **Reference patterns** — Mocks, mount/render, and data from comparable tests in the repo.
4. **Write failing tests** — Encode acceptance criteria as assertions; each case should fail for the right reason (missing behavior, not broken test harness).
5. **Run tests** — Confirm expected failures; summarize required implementation work for the issue scope.

If a **`PLAN.md` already exists** beside the target spec (for example after **create-tdd-test-plan**), switch to **From PLAN.md** instead of deriving cases from the issue alone.
