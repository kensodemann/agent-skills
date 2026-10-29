---
name: plan-implementation
description: >-
  Write PLAN.md at the repository root for a task or a bug. The plan breaks
  that issue into implementation steps. Use when the user gives an issue
  number and asks for that plan. A feature is refused until it is broken
  into tasks. Does not implement steps or mark them complete.
  implement-next-step does that.
---

# Plan implementation

**Scope:** Write **`PLAN.md` at the repository root** only. Do not implement a step, edit application or test code, commit, or set Status to complete.

This skill writes the only `PLAN.md` for a change, at the repository root. Each step lists the test files for the change it makes. There is no `PLAN.md` beside a spec.

Not **create-tasks** (GitHub child tasks under a feature).

The user passes a GitHub issue number. The current repository owns that issue.

## Prerequisite

The work must be on a feature branch, not `main`. Read the current branch with `git branch --show-current`.

Accepted branch names, compared to the issue number and title. Slug the title in lowercase with spaces turned into hyphens. Issue #73, "Reset the warp core", accepts:

- `feature/reset-the-warp-core`
- `feature/` plus other wording close to that title
- `73-reset-the-warp-core`

If the branch is `main`, stop. Do not write `PLAN.md` and do not ask to continue on `main`.

If the branch matches none of those, ask whether this branch is the correct feature branch and wait. Do not write `PLAN.md` until the user confirms.

## Chunks

- The size of the chunks for the task is determined by a logical breakdown of the bug or task
- The chunk should be large enough to be a meaningful and self-contained change, but small enough for a human to quickly review, both within the PLAN.md and in the code
- The chunks will be labeled as [STEP-###]
- The working flow after the plan is generated is that a user will ask for a step to be implemented. After the step is implemented (AI), the human will review the change, potentially request changes or make the changes themselves, and eventually commit the change
- As such, the step should build, test, and lint without warning or error at the end of each step
- Do not generate any steps that do not make modifications (that is, no step that simply runs the regression tests, as that is done as part of the completion criteria of every step)
- The human will ask for steps to be marked complete as they are implemented, so the "Status" in the step table is important

Leave every Status value empty. **implement-next-step** sets Status to `complete` in the table and under that step after the user validates it. This skill does not.

Number steps `[STEP-001]`, `[STEP-002]`, in dependency order. A later step may assume earlier steps are already committed. Each step, on its own, must leave build, test, and lint clean.

## Stop and ask

Do not write `PLAN.md` until these are settled. Ask in chat, list every open choice together, and wait. Do not record an unanswered choice as a Decision.

- The current branch matches none of the accepted names in Prerequisite, and the user has not confirmed it.
- A step depends on a choice the issue does not settle (behavior, API, or which part of the code owns the change).
- `PLAN.md` already exists.
- The repository's build, test, or lint command cannot be identified. Do not invent a package manager or a linter.

After the user answers, those choices are settled. Then write the plan.

## Workflow

1. Read the current branch. If it is `main`, stop.
2. Load the issue: `gh issue view <number> --json title,body,state,url,comments,issueType`.
3. If the branch is not `feature/` plus the title slug or wording close to the title, and not `<issue-number>-<title-slug>`, ask whether it is the correct feature branch and wait.
4. Classify `issueType.name`.
   - `Feature`: stop. Tell the human to break it into tasks first. Do not write `PLAN.md`.
   - `Task` or `Bug`: continue. A task is one unit of work, stand-alone or a child of a feature, and is never a parent. Write root `PLAN.md` in this request. Do not stop to propose splitting the issue into child issues. Do not refuse it for being large. Do not read sub-issues.
   - `issueType` null, or any other name: name what was found and wait. Do not write `PLAN.md`.
5. Read the code the issue touches. Name real files only. State current behavior from that code.
6. The step lists the test files for the change it makes and states what they assert. Do not require a failing test before production code. Do not add a new test suite when that area has none and the issue does not ask for tests. Do not write a second `PLAN.md`.
7. Take build, test, and lint from the repository (package scripts, Makefile, task runner, or project rules). Those commands are the commit gate. They must succeed with no warning or error.
8. Write root `PLAN.md` with the sections below.
9. Tell the user to review and edit the plan. **implement-next-step** implements the next incomplete step and marks it complete only after the user validates it.

## PLAN.md sections

Title: `# PLAN-<number> — <short title>`

First lines: `Issue: [#<number>](<url>) (<state>).` Then dependency, related issue, or parent feature links only when the issue states them.

One short paragraph of current behavior, with the files and symbols the steps change.

**Decisions** — one settled choice per bullet. Omit the section when the issue already settled every choice the steps need.

**Out of scope** — exclusions stated by the issue, and neighboring work a step must not absorb. Omit the section when there is nothing to exclude.

If acceptance depends on specific observable values or wording, add a short contract section that lists them. Otherwise omit it.

**File order** — this table. Leave Status empty. Each step changes at least one file. List files in edit order.

| Order | Step       | Status | Files                   |
| ----- | ---------- | ------ | ----------------------- |
| 1     | [STEP-001] |        | `path/a`, then `path/b` |

**Commit gate** — the human commits. A spot-check does not replace the gate. Record the repository's commands:

```bash
<build>
<test>
<lint>
```

State that all three must succeed with no warning or error before that commit.

**Each step** uses this shape. The same `[STEP-###]` label appears in the table and the heading.

```markdown
## [STEP-001] <imperative title>

**Status:**

**Acceptance criteria:** <observable outcome of this step>

**Files**

1. `path` — <what changes in this file>

**Change**

<Concrete change. Enough to implement without re-deriving the issue.>

**Spot-check**

<narrowest existing test command for this step's files>

- <what that run should show>

**Done when:** <acceptance criteria hold, and build, test, and lint succeed with no warning or error.>

**Commit:** the commit-gate commands succeed with no warning or error.
```

Omit **Spot-check** when the repository has no narrower test command for that step.
