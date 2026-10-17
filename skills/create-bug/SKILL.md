---
name: create-bug
description: >-
  Draft or create GitHub Bug issues using the repo's bug template. Use when
  the user reports a defect, asks for a bug issue, or when type is bug in
  issue frontmatter.
---

# Create Bug Issue

Before drafting or creating a Bug issue, read `.github/ISSUE_TEMPLATE/bug.md` in the **current repository** and match its structure and YAML frontmatter (`type: bug`, etc.).

## Template body (typical)

Use these sections unless the repo template differs:

```markdown
# Synopsis

# Steps to Reproduce

1.

# Expected behavior

# Additional context

# Acceptance Criteria

- [ ]
```

## Workflow

1. Fill reproduction steps and expected behavior clearly; add context (browser, env, screenshots) when known.
2. Propose the full issue markdown in chat for review before running `gh` unless the user asked to create immediately.
3. Create with the GitHub CLI when instructed, e.g. `gh issue create --template bug` (or equivalent for the repo).

For breaking work into linked child issues, use the **create-subtasks** skill.
