---
name: create-subtasks
description: >-
  Plan and create GitHub subtasks linked to a parent issue. Use when breaking
  down an issue, generating subtask markdown, or creating child issues with gh
  and "Related to #N" links.
---

# Create Subtasks

Applies when splitting a parent Feature, Task, or Bug into smaller issues.

## Rules

- **Do not create files** for subtasks. Output complete markdown for **each** subtask in chat first for review.
- Pick the correct issue type per subtask (feature, task, or bug) and follow the matching **create-feature**, **create-task**, or **create-bug** skill for structure.
- When the user asks to create issues, use the `gh` CLI.
- Link every subtask to the parent in the body, e.g. `Related to #123` (use the parent issue number).

## Workflow

1. Confirm parent issue number and scope.
2. Draft all subtask bodies in chat; wait for approval if the user is iterating.
3. Create issues with `gh` when instructed, preserving template frontmatter and parent links.
