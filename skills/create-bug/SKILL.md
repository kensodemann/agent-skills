---
name: create-bug
description: >-
  Draft or create GitHub Bug issues using the repo's bug template. Use when
  the user reports a defect, asks for a bug issue, when type is bug in issue
  frontmatter, or when drafting an accepted root INTENT.md that describes a
  defect, including a larger defect that started as INTENT.md. A short defect
  report with no INTENT.md still uses this skill. A bug may reference a related
  feature or task and is never a child of any issue.
---

# Create Bug Issue

## Workflow

1. Before any draft or `gh` create, including a short defect report with no `INTENT.md`, confirm `.github/ISSUE_TEMPLATE/bug.md`, `feature.md`, and `task.md` all exist in the current repository. If any are missing, name the missing files and wait until the human has created them. Do not invent a template.
2. Choose the source. Use the prompt when the user states the defect. Use root `INTENT.md` when the user asks to draft from that accepted file. When they do neither and the file exists, ask whether it is the accepted source and wait. When they do neither and the file is absent, ask for the defect and wait. A short report in the prompt is enough. Do not require `INTENT.md` on the prompt path, and do not ignore a prompt because the file exists.
3. Classify from that source. A bug is a defect, always stand-alone, and has no child tasks. It may reference a feature or task when the human names one as related work or a root cause. That reference does not make the bug a child. A feature is work that needs to be broken into tasks; a change that does not need a breakdown is a task. A task is one chunk of work, including a small idea that needs no breakdown. Draft only when the work is a defect. Otherwise name `create-feature` or `create-task` and wait. Do not draft.
4. A bug is never a child of any issue. Do not infer a related issue from the branch, the open issues, or earlier conversation. When the human names a related feature or task, load it with `gh issue view <number> --json number,title,issueType`. The kind is `issueType.name`.
   - `Feature` or `Task`: this bug references that issue. Continue. Do not pass `--parent`.
   - `Bug`: a bug is not a child of a bug. Name what was found and wait. Do not draft. Do not pass `--parent`.
   - `issueType` null, or any other name: name what was found and wait. Do not draft. Do not pass `--parent`.
   When they do not name a related issue, continue. The body has no `Related to #N`.
5. Read `.github/ISSUE_TEMPLATE/bug.md` and match its structure and YAML frontmatter (`type: bug`, and the rest of that frontmatter). The draft uses that file's headings.
6. Fill every heading from the whole source. Ask only when a heading would otherwise be empty. Do not add headings the template does not have. When the related issue is a `Feature` or a `Task`, put `Related to #N` in the body, using that issue's number. When they did not name one, the body has no `Related to #N`.
7. Show the full draft in chat for approval before `gh` creates it, unless the human asked to create it immediately. Keep the template's `type` and other frontmatter; set `title` from the source. The created issue is that approved draft: the approved title and body are what get created, with `gh issue create --type Bug`. Do not pass `--parent`.
