---
name: create-tdd-test-plan
description: >-
  Draft a TDD test plan (PLAN.md) from a GitHub issue or part of one, without
  writing tests or feature code. Use when the user wants a test plan for an
  issue, PLAN.md before implementation, or to review cases before TDD tests.
---

# Create TDD Test Plan from a GitHub Issue

Stack-specific harness notes live in the **current repository** (for example `.cursor/rules/testing.mdc`). Reference those when describing setup, mocks, and fixtures in the plan.

**Scope:** Create or update **`PLAN.md` only**. Do not write spec files or production code in this step unless the user explicitly expands the request.

Every planned case is **TDD** — behavior not implemented yet; failing tests will be expected when **create-tdd-tests** runs later.

## Test file structure (planned layout)

Document nesting explicitly in the plan (spec files rarely use a flat single-level `describe`):

- **Module / file** — Outer `describe` for the source file or module (for example `'calculate'` for `calculate.ts`).
- **Function or unit** — Inner `describe` per **exported function** or **single interactive unit** on a component (for example `'product'` under `'calculate'`).
- **Scenarios** — Deeper `describe` blocks for grouped cases (for example `when logged in`, invalid input).
- **Target spec file** — Path (existing or new) per project layout.

## Workflow

1. **Load requirements** — `gh issue view <number> --comments`. Honor user scope when they name a **portion** of the issue (subset of ACs, one workflow, one component).
2. **Map to code** — Files/functions/components that will exist or change; note gaps honestly (TDD).
3. **Survey test patterns** — Similar spec files: mocks, helpers, assertion style (cite paths and line ranges when helpful).
4. **Write `PLAN.md`** — Place it in the **same directory as the spec file** that will hold the tests (create the directory path in the plan if the file does not exist yet). Use a clear filename if the user specifies one; default **`PLAN.md`**.
5. **Hand off** — Tell the user to review/edit the plan. After approval, **create-tdd-tests** (**From PLAN.md**) implements from the current file; the plan overrides the issue if they disagree.

## PLAN.md content (per test case)

For each planned test:

- **Describe hierarchy** — Full nesting: module/file → function or unit → scenario `describe` titles (not “top-level” unless the spec file truly has no outer wrapper)
- **Scenario** — User workflow and issue AC reference
- **Setup** — Mocks, render/mount, async notes (per project testing rules)
- **Actions** — Interactions, calls, events
- **Assertions** — Expected outcomes and mock expectations
- **Pattern references** — Similar tests to mirror
- **Implementation notes** — Production work required for this test to pass (TDD)

Use headers and code blocks so **create-tdd-tests** can implement without re-deriving context.

## After the plan

1. User reviews and may edit `PLAN.md`.
2. When the user asks to implement tests, invoke **create-tdd-tests** on the **From PLAN.md** path: read the **current** `PLAN.md` end-to-end, implement case-for-case, do not re-fetch the issue to replace plan content.
