---
name: create-feature
description: >-
  Draft or create GitHub Feature issues using the repo's feature template. Use
  when the user asks for a feature issue, epic, or user-story-driven work item,
  or when type is feature in issue frontmatter.
---

# Create Feature Issue

Before drafting or creating a Feature issue, read `.github/ISSUE_TEMPLATE/feature.md` in the **current repository** and match its structure and YAML frontmatter (`type: feature`, etc.).

## Template body (typical)

Use these sections unless the repo template differs:

```markdown
# Synopsis

# User stories

1. As a user, I would like to...

# Processes

# Components

# Acceptance Criteria

- [ ]
```

## Workflow

1. Fill every section with concrete content; keep acceptance criteria as checkboxes.
2. Propose the full issue markdown in chat for review before running `gh` unless the user asked to create immediately.
3. Create with the GitHub CLI when instructed, e.g. `gh issue create --template feature` (or equivalent for the repo).

For breaking work into linked child issues, use the **create-subtasks** skill.
