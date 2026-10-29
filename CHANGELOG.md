# Changelog

## [2.0.0]

- Add `capture-intent` to interview the user and write an uncommitted root `INTENT.md` before a GitHub issue.
- Add `create-tasks` to divide a feature into leaf child Task issues. Remove `create-subtasks`.
- File features, tasks, and bugs from an accepted `INTENT.md` or a prompt, using the repository issue templates and the matching GitHub issue type.
- A feature is split into child tasks and is never planned directly. A task or a bug is stand-alone and may reference a related issue without becoming its child.
- `plan-implementation` writes root `PLAN.md` only for a task or a bug.
- `implement-next-step` can run `tests only` to write that step’s tests and leave production code unchanged, and requires a clean tree only when starting a step.
- Remove `create-tdd-test-plan` and `create-tdd-tests`. `create-tests` remains for behavior already in the codebase.
- Add bug, feature, and task issue templates, and document the intent-to-review loop in the README.

## [1.2.0]

- Add `plan-implementation` to write a root `PLAN.md` that splits one GitHub issue into reviewable `[STEP-###]` steps.
- Add `implement-next-step` to implement the next incomplete `PLAN.md` step and mark it complete after the user validates it.
- Write `review-pull-request` reports to `REVIEW.md`.
- Document the Cursor CLI bug that pins a plugin to the version first installed, and cloning the repo as a local plugin.
- Clarify the release process in `CONTRIBUTING.md`.

## [1.1.0]

- Add `update-deps` to take patch and minor npm/pnpm upgrades, leave major upgrades unapplied, and write `update-report.md`.

## [1.0.0]

- Initial GitHub workflow skills: `create-feature`, `create-task`, `create-bug`, and `create-subtasks`.
- Add `review-pull-request` skill with local `PRXXX.md` reports, category tags, and per-framework best-practices links.
- Add Vitest skills: `create-tests` for existing behavior, `create-tdd-test-plan` for a `PLAN.md` before implementation, and `create-tdd-tests` for failing tests from that plan or a GitHub issue.
- Add instructions and plugin metadata for Claude and Cursor.
