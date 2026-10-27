---
name: capture-intent
description: >-
  Interview the user and write one root INTENT.md for a home project. Use when
  the user states a desire, asks to capture intent, or wants INTENT.md before
  a GitHub issue. Does not create a GitHub issue.
---

# Capture intent

**Scope:** Interview the user and write a single `INTENT.md` at the target repository root. The file is a working draft and must not be committed. This skill does not create a GitHub issue. After the human accepts the draft, `create-feature`, `create-task`, or `create-bug` files that issue later.

## Overwrite

At the start, check for a root `INTENT.md`. If it exists, ask before overwriting it and wait. If the user declines, stop and leave the file unchanged. If it is absent, or the user agrees to replace it, continue.

## Interview

Ask questions until each section below can be filled from the user's answers. Ground the questions in the target repository:

- Read files that bear on the desire.
- Use git facts: repository root, status, and recent commits.
- Use `gh` facts — the repo and open issues — when the desire may overlap work already filed.

Do not use an editor-only tool. Do not run `gh issue create` or any other command that files, edits, or comments on an issue.

## Write INTENT.md

Write root `INTENT.md` with only these headings, in this order. Every heading is present. When the user has nothing for Constraints or Open questions, say so under that heading.

```markdown
# Problem

# Proposed outcome

# Affected users and systems

# Constraints

# Open questions
```

## Recommend a type

After the file is written, recommend one type from this table and then stop. Name the later skill: Feature → `create-feature`, Task → `create-task`, Bug → `create-bug`.

| Type | When to use it | What happens next |
| ---- | -------------- | ----------------- |
| Feature | The work needs to be broken into tasks. A change that does not need a breakdown is a task. | Child tasks, after approval. Never `PLAN.md`. |
| Task | One chunk of work, with or without a parent. A child task uses the Task template and `Related to #N`. | A leaf task is planned on request. A task that already has subtasks is a container and is not planned or worked. |
| Bug | A defect. A short report uses `create-bug` with no `INTENT.md`. A larger defect may start as `INTENT.md` and then become one bug. | Always one issue, then `PLAN.md`. |

A short defect still gets `INTENT.md` when this skill was asked to write it. The recommendation can note that a future short report may use `create-bug` with no `INTENT.md`.

Do not draft the issue and do not start the next skill.
