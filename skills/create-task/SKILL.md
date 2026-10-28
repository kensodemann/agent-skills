---
name: create-task
description: >-
  Draft or create GitHub Task issues using the repo's task template. Use when
  the user asks for a task issue, chore, or concrete work item that is not a
  bug or full feature, when type is task in issue frontmatter, or when
  drafting an accepted root INTENT.md that is one chunk of work, including a
  small idea that needs no breakdown. A task is stand-alone unless the human
  names a parent feature.
---

# Create Task Issue

## Workflow

1. Before any draft or `gh` create, confirm `.github/ISSUE_TEMPLATE/bug.md`, `feature.md`, and `task.md` all exist in the current repository. If any are missing, name the missing files and wait until the human has created them. Do not invent a template.
2. Choose the source. Use the prompt when the user states the work. Use root `INTENT.md` when the user asks to draft from that accepted file. When they do neither and the file exists, ask whether it is the accepted source and wait. When they do neither and the file is absent, ask for the work and wait. Do not ignore a prompt because the file exists.
3. Classify from that source. A task is always one unit of work, including a small idea that needs no breakdown, and is never split. A feature is work that needs to be broken into tasks. A bug is a defect. Draft only when the work is one unit of work. Work that must be broken into child tasks names `create-feature` and waits. A defect names `create-bug` and waits. Do not draft.
4. A task is stand-alone unless the human specifically names a parent feature. Do not infer a parent from the branch, the open issues, or earlier conversation. When they name a parent, load it with `gh issue view <number> --json number,title,issueType`. The parent kind is `issueType.name`.
   - `Feature`: this task is a child of that feature. Continue.
   - `Task` or `Bug`: refuse. A task cannot have children. A bug cannot have children. The tree is only a feature and its child tasks. Do not draft.
   - `issueType` null, or any other name: name what was found and wait. Do not draft.
   When they do not name a parent, continue with a stand-alone task.
5. Read `.github/ISSUE_TEMPLATE/task.md` and match its structure and YAML frontmatter (`type: task`, and the rest of that frontmatter). The draft uses that file's headings.
6. Fill every heading from the whole source. Ask only when a heading would otherwise be empty. Do not add headings the template does not have. When the parent is a `Feature`, put `Related to #N` in the body, using that feature's issue number. When the task is stand-alone, the body has no `Related to #N`.
7. Show the full draft in chat for approval before `gh` creates it, unless the human asked to create it immediately. Keep the template's `type` and other frontmatter; set `title` from the source. The created issue is that approved draft: the approved title and body are what get created, with `gh issue create --type Task`. When the task is stand-alone, do not pass `--parent`. When the parent is a `Feature`, pass `--parent` set to that feature number.
