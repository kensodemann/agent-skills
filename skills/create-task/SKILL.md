---
name: create-task
description: >-
  Draft or create GitHub Task issues using the repo's task template. Use when
  the user asks for a task issue, chore, or concrete work item that is not a
  bug or full feature, or when type is task in issue frontmatter.
---

# Create Task Issue

Before drafting or creating a Task issue, read `.github/ISSUE_TEMPLATE/task.md` in the **current repository** and match its structure and YAML frontmatter (`type: task`, etc.).

## Template body (typical)

Use these sections unless the repo template differs:

```markdown
# User Story

As a user, I would like to...

# Details

# Acceptance Criteria

- [ ]
```

## Workflow

1. Fill every section with concrete content; keep acceptance criteria as checkboxes.
2. Propose the full issue markdown in chat for review before running `gh` unless the user asked to create immediately.
3. Create with the GitHub CLI when instructed, e.g. `gh issue create --template task` (or equivalent for the repo).

For breaking work into linked child issues, use the **create-subtasks** skill.
