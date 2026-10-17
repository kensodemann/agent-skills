---
name: review-pull-request
description: >-
  Review GitHub pull requests and write structured feedback to PRXXX.md (not
  on the PR). Use when reviewing a PR, writing PR feedback, or doing code review
  on a diff or pull request.
---

# Review Pull Request

Review the **resulting diff**, not intermediate commit history.

## Delivery

- **Do not** post review comments on the pull request (no GitHub review threads, inline comments, or `gh pr review` that publishes to the PR).
- Write the full review to a file named **`PRXXX.md`**, where `XXX` is the pull request number (e.g. `PR42.md` for PR #42). Place the file in the workspace root unless the user specifies another path.
- In the agent chat, **only** echo the review summary (see below) and tell the user to open the generated file for details. Do not paste individual findings into the chat.

## Report structure

1. **Summary** — Short paragraph: scope reviewed, overall recommendation (approve / request changes / comment-only), and notable themes.
2. **Counts table** — Count of findings **per category** (use category abbreviations as column keys). Include a **Total** row. Categories with zero findings still appear in the table with `0`.

Example:

```markdown
## Summary

<paragraph>

## Finding counts

| Category                                   | Count |
| ------------------------------------------ | ----: |
| 🔒 Security concerns (immediate attention) |     1 |
| ⚡ Performance issues/optimizations        |     0 |
| 🧹 Code cleanup and maintainability        |     2 |
| 📚 Documentation gaps                      |     0 |
| ✅ Positive feedback                       |     1 |
| 🚨 Critical blockers                       |     0 |
| 💭 Questions for discussion                |     1 |
| **Total**                                  |     5 |
```

3. **Findings** — One section per item, ordered by category (SEC, PERF, CLN, DOC, POS, BLK, QST), then by sequence number within the category.

Each finding **must** start with a tag on its own line: **`[LBL-NNN]`**, where `LBL` is the category abbreviation and `NNN` is a three-digit sequence **within that category** starting at `001` (e.g. `[SEC-001]`, `[CLN-002]`).

Under the tag, use the comment format below (Issue / Suggestion / Why / Documentation as applicable). Positive feedback uses the same format where Suggestion can describe what worked well.

## Category tags

Use these abbreviations consistently in tags and in the counts table:

| Abbr | Label |
| ---- | ----- |
| `SEC` | 🔒 Security concerns (immediate attention) |
| `PERF` | ⚡ Performance issues/optimizations |
| `CLN` | 🧹 Code cleanup and maintainability |
| `DOC` | 📚 Documentation gaps |
| `POS` | ✅ Positive feedback |
| `BLK` | 🚨 Critical blockers |
| `QST` | 💭 Questions for discussion |

## Merge practice

Many teams **squash-merge** pull requests. Intermediate commits (including titles like "WIP") are normal working history and do not land on the base branch. Do not flag commit count, WIP prefixes, or commit-message polish as review blockers unless the user or repo explicitly cares.

## Focus areas

Prioritize feedback in these areas when relevant to the change:

- **Security**: No hardcoded secrets; validate inputs; enforce auth checks in server actions, APIs, and data loaders.
- **Performance**: Avoid N+1 queries; minimize client bundles; keep server-only work off the client when applicable.
- **Testing**: Add or maintain coverage for data operations, routing, and critical behavior.
- **Comments**: Minimal and purposeful; explain "why", not "what".

## Review style

- Give specific, actionable feedback with code examples when helpful.
- Acknowledge good patterns and solid solutions.
- Cite documentation from [references/best-practices.md](references/best-practices.md) when it strengthens a finding.
- Focus on improvement, not criticism.

## Documentation links

When a finding needs external justification, add **Documentation:** with one or more links from [references/best-practices.md](references/best-practices.md). Use the sections that match the repository (TypeScript is common; add Vue, React, Angular, and/or Tailwind when those stacks apply). Do not link elsewhere unless the user asked or the topic is not covered in that file. Omit **Documentation** when the finding is clear from the diff alone.

## Comment format

Use this structure for each substantive finding in `PRXXX.md`:

**Issue:** Describe what needs attention  
**Suggestion:** Specific improvement with code example  
**Why:** Reasoning and benefits  
**Documentation:** Link(s) from [references/best-practices.md](references/best-practices.md) when applicable
