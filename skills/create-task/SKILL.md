---
name: create-task
description: >-
  Draft or create GitHub Task issues using the repo's task template. Use when
  the user asks for a task issue, chore, or concrete work item that is not a
  bug or full feature, when type is task in issue frontmatter, or when
  drafting an accepted root INTENT.md that is one chunk of work, including a
  small idea that needs no breakdown. A task is a child only when the human
  names a parent feature. A stand-alone task may reference a related feature or
  another task, and that reference is not a parent.
---

# Create Task Issue

## Workflow

1. Before any draft or `gh` create, confirm `.github/ISSUE_TEMPLATE/bug.md`, `feature.md`, and `task.md` all exist in the current repository. If any are missing, name the missing files and wait until the human has created them. Do not invent a template.
2. Choose the source. Use the prompt when the user states the work. Use root `INTENT.md` when the user asks to draft from that accepted file. When they do neither and the file exists, ask whether it is the accepted source and wait. When they do neither and the file is absent, ask for the work and wait. Do not ignore a prompt because the file exists.
3. Classify from that source. A task is always one unit of work, including a small idea that needs no breakdown, and is never split. A feature is work that needs to be broken into tasks. A bug is a defect. Draft only when the work is one unit of work. Work that must be broken into child tasks names `create-feature` and waits. A defect names `create-bug` and waits. Do not draft.
4. A task is a child only when the human specifically names a parent feature. A stand-alone task may reference a feature or another task when the human names that issue as related work. That reference is not a parent. Do not infer a parent or a related issue from the branch, the open issues, or earlier conversation. When they name an issue, load it with `gh issue view <number> --json number,title,issueType`. The kind is `issueType.name`.
   - Named as the parent:
     - `Feature`: this task is a child of that feature. Continue.
     - `Task`: refuse. A task cannot have children. The tree is only a feature and its child tasks. Do not draft. Do not pass `--parent`.
     - `Bug`: refuse. A bug cannot have children. Do not draft. Do not pass `--parent`.
     - `issueType` null, or any other name: name what was found and wait. Do not draft.
   - Named as related work, not as the parent:
     - `Feature` or `Task`: this task stays stand-alone and references that issue. Continue. Do not pass `--parent`.
     - `Bug`: this skill does not attach a bug reference. Name what was found and wait. Do not draft. Do not pass `--parent`.
     - `issueType` null, or any other name: name what was found and wait. Do not draft.
   When they name neither a parent nor a related issue, continue with a stand-alone task. The body has no `Related to #N`.
5. Read `.github/ISSUE_TEMPLATE/task.md` and match its structure and YAML frontmatter (`type: task`, and the rest of that frontmatter). The draft uses that file's headings.
6. Fill every heading from the whole source. Ask only when a heading would otherwise be empty. Do not add headings the template does not have. When the parent is a `Feature`, put `Related to #N` in the body, using that feature's issue number. When the task is stand-alone and the related issue is a `Feature` or a `Task`, put `Related to #N` in the body, using that issue's number. When they named neither, the body has no `Related to #N`.
7. Show the full draft in chat for approval before `gh` creates it, unless the human asked to create it immediately. Keep the template's `type` and other frontmatter; set `title` from the source. The created issue is that approved draft: the approved title and body are what get created, with `gh issue create --type Task`. When the parent is a `Feature`, pass `--parent` set to that feature number. When the task is stand-alone, do not pass `--parent`, including when it references a feature or another task.
