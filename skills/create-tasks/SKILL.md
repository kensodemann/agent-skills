---
name: create-tasks
description: >-
  Divide a Feature into leaf Task issues. Use when the user runs
  /create-tasks #N, or asks to break a feature into child tasks. The parent
  is a Feature. Each child is a Task sub-issue (--parent) with "Related to #N".
  A task or a bug is not a parent.
---

# Create Tasks

## Workflow

1. Before any draft or `gh` create, confirm `.github/ISSUE_TEMPLATE/bug.md`, `feature.md`, and `task.md` all exist in the current repository. If any are missing, name the missing files and wait until the human has created them. Do not invent a template.
2. The usual call is `/create-tasks #N`, where `#N` is the parent feature. When no issue number is given, ask for the parent feature and wait. Do not draft.
3. Load the parent with `gh issue view <number> --json number,title,issueType`. The parent kind is `issueType.name`.
   - `Feature`: continue.
   - `Task`: refuse. A task is one unit of work and cannot have children. Do not draft.
   - `Bug`: refuse. A bug is stand-alone and cannot have children. Do not draft.
   - `issueType` null, or any other name: name what was found and wait. Do not draft.
4. Read `.github/ISSUE_TEMPLATE/task.md`. Divide the feature into logical units of work. Every child is a leaf Task. Match that file's structure and YAML frontmatter (`type: task`, and the rest of that frontmatter). The draft uses that file's headings. Do not draft a feature, a bug, or a task under another task.
5. Fill every heading from the feature. Ask only when a heading would otherwise be empty. Do not add headings the template does not have. Put `Related to #N` in the body, using the parent issue number.
6. Show the full draft of every child in chat. Wait until the human approves. Do not run `gh issue create` before that approval, including when the same request asked to create the issues.
7. After approval, create each child with `gh issue create --title` set to the approved title, `--body` set to the approved body, `--type Task`, and `--parent` set to the parent feature number. The body includes `Related to #N`. Do not create files.
