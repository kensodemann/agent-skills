---
name: create-tests
description: >-
  Add Vitest tests for behavior that already exists in the codebase. Use when
  the user asks to test existing functionality, add coverage, or write tests for
  a component, page, or service. Tests for behavior that is not in the tree yet
  belong on the root implementation PLAN.md. plan-implementation writes that
  plan when it does not exist. implement-next-step carries out the step once
  the plan exists.
---

# Create Tests for Existing Functionality

Vitest harnesses, mocks, and fixtures are defined in the **current repository** (for example `.cursor/rules/testing.mdc`). Follow those for setup, module mocks, and shared utilities.

**Scope:** Implement tests only. Do not add or change product features unless the user explicitly asks.

Tests for existing behavior **should pass** when written correctly. If they fail, investigate: wrong assumptions, flaky setup, or a bug in production code — explain what you find.

## Test file structure

Spec files usually **nest** `describe` blocks. Extend the existing hierarchy; do not assume every function gets its own root-level `describe`.

Example for `calculate.ts` with `sum`, `difference`, `product`, `quotient`:

```typescript
describe('calculate', () => {
  describe('sum', () => { /* ... */ });
  describe('product', () => { /* new tests go here */ });
});
```

- **Module / file** — Outer `describe` names the file or module under test (for example `'calculate'`).
- **Function or unit** — One inner `describe` per **exported function** in a service/module, or per **single interactive unit** on a component. New tests for `product()` belong in `describe('product', ...)`, still inside `describe('calculate', ...)`.
- **Scenarios** — Deeper nested `describe` blocks group related cases (for example `when logged in`, validation errors, happy path).

The **logical scope** for new tests is the function or unit `describe`, not necessarily the outermost block in the file. Match naming, nesting, and assertion style of neighboring tests in the same spec file.

## Workflow

1. **Identify the unit** — Source file(s), export or component, and behaviors to cover (user scope or inferred from code).
2. **Pick or create the spec file** — Co-located `__tests__` or `*.spec.ts(x)` per project convention; extend an existing file when the unit already has one.
3. **Study references** — Similar tests in the repo (mocks, mount/render, async patterns).
4. **Read implementation** — Handlers, props, branches, data flow; cover meaningful paths, not exhaustive noise.
5. **Implement tests** — Follow project rules; use shared test data helpers when present.
6. **Run tests** — Use the project’s Vitest command. Fix test code if setup is wrong; report likely product bugs if behavior contradicts the implementation the user intended to test.

When the user names a GitHub issue for **context only**, use `gh issue view` to understand acceptance criteria, and treat all tests as **existing functionality**. When the user wants tests for behavior that is not in the tree yet, stop. If the root implementation `PLAN.md` does not exist, point them at **plan-implementation**. If it exists, point them at **implement-next-step**. Do not write those tests in this skill.
