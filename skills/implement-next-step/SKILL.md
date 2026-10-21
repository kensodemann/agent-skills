---
name: implement-next-step
description: >-
  Implement the next incomplete step in the repository root PLAN.md, then ask
  the user to validate it. Use when the user asks to implement the next step,
  continue a PLAN.md step, or mark a validated step complete. Further changes
  stay on that step until the user says it is complete. Does not commit or
  start the following step.
---

# Implement next step

**Scope:** The next incomplete step in root **`PLAN.md`**. Do not start a later step, do not commit, and do not edit `PLAN.md` except when marking that step complete.

**plan-implementation** writes the plan. This skill carries out one step.

## Prerequisite

The current git branch must not have any uncommitted changes, must contain a `PLAN.md` file, and must not be `main`.

If the branch is `main` or if uncommitted changes currently exist, stop. Do not implement and do not mark a step complete.

## Which step

Read `PLAN.md` in full. User edits win over the GitHub issue. Do not re-plan.

The next step is the first **File order** row whose Status is not `complete`. Empty Status is not complete.

- No root `PLAN.md`: stop and say so.
- Every row is `complete`: stop and say so.
- That step's acceptance criteria already hold in the tree: do not redo the work. Ask the user to validate.

## Implement

1. Check the branch (see Prerequisite).
2. Implement only that step: its files, in the listed order, its **Change**, its acceptance criteria, and any Decisions or contract text the step depends on. Leave **Out of scope** work undone.
3. Run the step's **Spot-check** when it has one, then every commit-gate command in `PLAN.md`. Each must succeed with no warning or error. Fix failures that are inside this step and run them again.
4. Ask the user to validate the step. They may ask for more work, or they may edit the code themselves. Do not mark the step complete in this turn.

Do not revert edits the user made. A request for more work is not validation: make those changes, stay inside this step, re-run the spot-check and the commit gate, then ask again.

## Mark complete

Mark the step only after the user explicitly says it is complete.

1. Run the commit-gate commands if they have not been run since the last change. If any command fails or warns, report that and do not mark the step. Wait for a fix or for more work.
2. Set Status to `complete` in the **File order** row and in that step's `**Status:**` line. Change nothing else in `PLAN.md`.
3. Stop. Do not implement the next step. The human commits.
