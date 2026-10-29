---
name: implement-next-step
description: >-
  Implement the next incomplete step in the repository root PLAN.md, then ask
  the user to validate it. Use when the user asks to implement the next step,
  continue a PLAN.md step, or mark a validated step complete. Also use when the
  user says "tests only" or "tests-only": write that step's tests, leave
  production code unchanged, do not commit, and do not mark the step complete.
  Further changes stay on that step until the user says it is complete. Does
  not commit or start the following step.
---

# Implement next step

**Scope:** The next incomplete step in root **`PLAN.md`**. Do not start a later step, do not commit, and do not edit `PLAN.md` except when marking that step complete.

**plan-implementation** writes the plan. This skill carries out one step.

## Prerequisite

The current branch must contain a root `PLAN.md` and must not be `main`. If the branch is `main`, or there is no root `PLAN.md`, stop. Do not implement and do not mark a step complete.

Require a clean working tree only when starting a step. Starting means the first request to implement that step, including `/implement-next-step` or a request that names the step, when that step is not already in progress. If uncommitted changes exist at that start, stop. Do not implement.

A step is already in progress when this conversation has started it and it is not yet marked complete, or when the user is asking for more work on it, asking for `tests only` on it, or saying it is complete. Uncommitted changes from that work are part of the step. Once the step is in progress, further updates stay on that step and do not require a commit in between. Do not stop for a dirty tree. Marking the step complete does not require a clean tree. The human commits after it is marked complete. The next step starts only after that, and that start requires a clean tree again.

## Which step

Read `PLAN.md` in full. User edits win over the GitHub issue. Do not re-plan.

The next step is the first **File order** row whose Status is not `complete`. Empty Status is not complete.

- No root `PLAN.md`: stop and say so.
- Every row is `complete`: stop and say so.
- That step's acceptance criteria already hold in the tree: do not redo the work. Ask the user to validate.

## Implement

The normal path runs when the user did not say `tests only` or `tests-only`.

1. Check the branch (see Prerequisite). Apply the clean-tree stop only when this step is not already in progress.
2. Implement only that step: its files, in the listed order, its **Change**, its acceptance criteria, and any Decisions or contract text the step depends on. Leave **Out of scope** work undone.
3. Run the step's **Spot-check** when it has one, then every commit-gate command in `PLAN.md`. Each must succeed with no warning or error. Fix failures that are inside this step and run them again.
4. Ask the user to validate the step. They may ask for more work, or they may edit the code themselves. Do not mark the step complete in this turn.

Do not revert edits the user made. A request for more work is not validation: make those changes, stay inside this step, re-run the spot-check and the commit gate, then ask again.

The tests-only path runs instead of the normal path when the user says `tests only` or `tests-only`.

1. Use the same **Prerequisite** and **Which step** rules. One incomplete step only. Apply the clean-tree stop only when this step is not already in progress.
2. Edit only the test files that step names. A listed file is a test file when that step's **Files** entry or **Change** text describes it as a test. Leave every production file unchanged, including production files the step lists. If the step names no test file, stop and say so. Do not add a test path the step does not list.
3. Write those tests from the step's **Change**, acceptance criteria, and any Decisions or contract text the step depends on. Leave **Out of scope** work undone.
4. When the behavior those tests describe is not in the tree, leave the tests failing. Do not add or edit production code to make them pass. When that behavior is already in the tree, the tests are expected to pass. Fix a failure of that kind only in the test files.
5. Run the step's **Spot-check** when it has one, then the commit-gate commands in `PLAN.md`. A test failure caused by missing behavior is the expected result: report it, and do not treat it as a prompt to edit production code. A gate command other than that expected test failure must succeed with no warning or error. Fix a failure inside the test files and run the commands again.
6. Ask the user to validate. Do not mark the step complete. Do not commit. Do not start the next step.

On the tests-only path, a request for more work stays inside this step. More work still does not edit production code and still does not mark the step complete. Do not revert edits the user made.

## Mark complete

Mark the step only after the user explicitly says it is complete. Uncommitted changes from this step are expected. Do not apply the clean-tree stop.

1. Run the commit-gate commands if they have not been run since the last change. If any command fails or warns, report that and do not mark the step. Wait for a fix or for more work.
2. Set Status to `complete` in the **File order** row and in that step's `**Status:**` line. Change nothing else in `PLAN.md`.
3. Stop. Do not implement the next step. The human commits.
