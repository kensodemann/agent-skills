---
name: create-feature
description: >-
  Draft or create GitHub Feature issues using the repo's feature template. Use
  when the user asks for a feature issue, epic, or user-story-driven work item,
  when type is feature in issue frontmatter, or when drafting an accepted root
  INTENT.md that will be split into tasks. A one-chunk idea is not a feature.
---

# Create Feature Issue

## Workflow

1. Before any draft or `gh` create, confirm `.github/ISSUE_TEMPLATE/bug.md`, `feature.md`, and `task.md` all exist in the current repository. If any are missing, name the missing files and wait until the human has created them. Do not invent a template.
2. Choose the source. Use the prompt when the user states the work. Use root `INTENT.md` when the user asks to draft from that accepted file. When they do neither and the file exists, ask whether it is the accepted source and wait. When they do neither and the file is absent, ask for the work and wait. Do not ignore a prompt because the file exists.
3. Classify from that source. A feature is work that needs to be broken into tasks. It must have child tasks and is not itself a leaf. A change that does not need a breakdown is a task. A task is one chunk of work, including a small idea that needs no breakdown. A bug is a defect. Draft only when the work needs to be broken into tasks. A one-chunk idea names `create-task` and waits. A defect names `create-bug` and waits. Do not draft.
4. Read `.github/ISSUE_TEMPLATE/feature.md` and match its structure and YAML frontmatter (`type: feature`, and the rest of that frontmatter). The draft uses that file's headings.
5. Fill every heading from the whole source. Ask only when a heading would otherwise be empty. Do not add headings the template does not have.
6. Show the full draft in chat for approval before `gh` creates it, unless the human asked to create it immediately. Keep the template's `type` and other frontmatter; set `title` from the source. The created issue is that approved draft: the approved title and body are what get created, with `gh issue create --type Feature`.

For dividing the feature into child tasks, use the **create-tasks** skill. This skill does not create those children.
