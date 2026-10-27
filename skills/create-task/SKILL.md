---
name: create-task
description: >-
  Draft or create GitHub Task issues using the repo's task template. Use when
  the user asks for a task issue, chore, or concrete work item that is not a
  bug or full feature, when type is task in issue frontmatter, or when
  drafting an accepted root INTENT.md that is one chunk of work, including a
  small idea that needs no breakdown.
---

# Create Task Issue

## Workflow

1. Before any draft or `gh` create, confirm `.github/ISSUE_TEMPLATE/bug.md`, `feature.md`, and `task.md` all exist in the current repository. If any are missing, name the missing files and wait until the human has created them. Do not invent a template.
2. Choose the source. Use the prompt when the user states the work. Use root `INTENT.md` when the user asks to draft from that accepted file. When they do neither and the file exists, ask whether it is the accepted source and wait. When they do neither and the file is absent, ask for the work and wait. Do not ignore a prompt because the file exists.
3. Classify from that source. A task is one chunk of work, including a small idea that needs no breakdown. A feature is work that needs to be broken into tasks. A bug is a defect. Draft only when the work is one chunk. Work that needs to be broken into tasks names `create-feature` and waits. A defect names `create-bug` and waits. Do not draft.
4. Read `.github/ISSUE_TEMPLATE/task.md` and match its structure and YAML frontmatter (`type: task`, and the rest of that frontmatter). The draft uses that file's headings.
5. Fill every heading from the whole source. Ask only when a heading would otherwise be empty. Do not add headings the template does not have.
6. Show the full draft in chat for approval before `gh` creates it, unless the human asked to create it immediately. Keep the template's `type` and other frontmatter; set `title` from the source. The created issue is that approved draft: the approved title and body are what get created.

For breaking work into linked child issues, use the **create-subtasks** skill.
