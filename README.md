# Agent Skills

Skills for the path from a written intent to a reviewed pull request. This repo ships both a [Claude Code](https://docs.anthropic.com/en/docs/claude-code) plugin (`.claude-plugin/`) and a [Cursor](https://cursor.com/docs/plugins) plugin (`.cursor-plugin/`). The loop in this README is the one both plugins ship.

`create-tests` and `update-deps` stay outside that loop. `create-tests` covers behavior already in the codebase. `update-deps` takes patch and minor npm and pnpm updates.

Skills live under [`skills/`](skills/) as standard Agent Skills (`SKILL.md` per folder).

---

## Usage

After [installing](#installation), open the **repository you are working in**. Issue skills read that repo’s `.github/ISSUE_TEMPLATE/` files; run `gh auth login` if you want the agent to create issues with the GitHub CLI.

Invoke a skill explicitly with **`/skill-name`** (Cursor IDE, **`agent`**, or Claude Code), or paste a prompt like the samples below—the agent should match the skill from context.

### Loop

1. [capture-intent](skills/capture-intent/) writes one root `INTENT.md`. The file is a working draft and is not committed. This skill does not create the GitHub issue.
2. [create-feature](skills/create-feature/), [create-task](skills/create-task/), or [create-bug](skills/create-bug/) files the issue from that accepted intent, or from a prompt. A target repository needs `.github/ISSUE_TEMPLATE/bug.md`, `feature.md`, and `task.md`. This repository does not add those templates to target repos. If any of the three is missing, the issue skill names the missing files and waits.
3. A feature must be broken into leaf child tasks with [create-tasks](skills/create-tasks/) (`/create-tasks #N`). A feature is never planned directly. A task is one unit of work: [create-task](skills/create-task/) creates it stand-alone unless the human names a parent feature, and a task is never a parent. A bug is a stand-alone defect and is never split. The tree is only a feature and its child tasks.
4. [plan-implementation](skills/plan-implementation/) writes one root `PLAN.md` for a task or a bug. Each step lists the test files for that change. A feature is refused until [create-tasks](skills/create-tasks/) has divided it.
5. [implement-next-step](skills/implement-next-step/) carries out the next incomplete step and asks the human to validate it. It marks the step complete only after the human says it is complete. The human commits. The skill does not commit and does not open the pull request. When the human says `tests only` or `tests-only`, the skill writes that step’s tests and leaves production code unchanged.
6. The human opens the pull request.
7. [review-pull-request](skills/review-pull-request/) writes `REVIEW.md` and does not post on the pull request.
8. The human addresses that feedback with ordinary prompts. No skill owns that turn.

[create-tests](skills/create-tests/) and [update-deps](skills/update-deps/) stay outside the loop. [create-tests](skills/create-tests/) covers behavior already in the codebase. [update-deps](skills/update-deps/) takes patch and minor npm and pnpm updates.

| Skill | Purpose |
| ----- | ------- |
| [capture-intent](skills/capture-intent/) | Write an uncommitted root `INTENT.md` and recommend feature, task, or bug |
| [create-feature](skills/create-feature/) | File a feature that must be split into child tasks |
| [create-tasks](skills/create-tasks/) | Divide a feature into leaf child tasks (`/create-tasks #N`) |
| [create-task](skills/create-task/) | File one task, stand-alone unless the human names a parent feature |
| [create-bug](skills/create-bug/) | File a stand-alone bug |
| [plan-implementation](skills/plan-implementation/) | Write one root `PLAN.md` for a task or a bug |
| [implement-next-step](skills/implement-next-step/) | Implement the next incomplete step, including `tests only`, and mark it complete after the human validates it |
| [review-pull-request](skills/review-pull-request/) | Write `REVIEW.md` only. Do not post on the pull request |
| [create-tests](skills/create-tests/) | Vitest tests for behavior already in the codebase. Outside the loop |
| [update-deps](skills/update-deps/) | Patch and minor npm/pnpm updates. Outside the loop |

### [capture-intent](skills/capture-intent/)

```text
/capture-intent
I want a shared inbox that files each request as a bug, a feature, or a task.
```

### [create-feature](skills/create-feature/)

```text
/create-feature
Add GitHub OAuth as a sign-in option alongside email/password.
```

### [create-tasks](skills/create-tasks/)

```text
/create-tasks #123
```

### [create-task](skills/create-task/)

```text
/create-task
Bump TypeScript to 5.7 across the monorepo and fix any new type errors in the Vue app package.
```

### [create-bug](skills/create-bug/)

```text
/create-bug
On mobile Safari, the checkout button stays disabled after autofill until the page is refreshed.
```

### [plan-implementation](skills/plan-implementation/)

```text
/plan-implementation 441
```

### [implement-next-step](skills/implement-next-step/)

```text
/implement-next-step
```

```text
/implement-next-step
tests only
```

### [review-pull-request](skills/review-pull-request/)

```text
/review-pull-request #87
```

### [create-tests](skills/create-tests/)

```text
/create-tests Cover the logout flow in AuthService.
```

### [update-deps](skills/update-deps/)

```text
/update-deps
```

---

## Installation

Pick the path that matches your editor. **Plugin install** is the easiest way to get every skill and stay updatable. **Manual install** copies skill folders into discovery directories—you can mix, match, and install only the skills you want.

### Cursor

Cursor loads skills from **plugins** or from fixed **skill directories** on disk (see [Manual Installation](#manual)). This repository’s root `skills/` folder is **not** scanned automatically when you only clone and open the repo; install the plugin or copy skills into a discovery path below.

The same plugin and skill discovery applies in the **desktop app** and in the **terminal Agent CLI** (`agent`, also invokable as `cursor agent`). User-scoped plugin installs sync across both; project-scoped installs follow the workspace you have open.

#### Cursor Agent CLI (terminal)

**Note:** There is currently a bug in the Cursor CLI that ends up pinning the plugin to the initially installed version, so do not use this method at this time. Instead, clone the repo as a local plugin (`cd ~/.cursor/plugins/local/; git clone git@github.com:kensodemann/agent-skills.git` )

Use this path if you work in the terminal-based agent rather than the IDE **Customize** panel.

1. Register this repository as a plugin marketplace (scriptable; safe to run in dotfiles or setup scripts):
   ```bash
   agent plugin marketplace add https://github.com/kensodemann/agent-skills
   ```
   Optional: pin a branch, tag, or commit with `--git-ref` (for example `--git-ref v1.0.0`). List or refresh marketplaces with `agent plugin marketplace list` and `agent plugin marketplace update https://github.com/kensodemann/agent-skills`.
2. Start an interactive session from the repo you are working in:
   ```bash
   cd /path/to/your-project
   agent
   ```
3. Install the plugin inside the session:
   - Type **`/plugin`**, open the **Marketplace** tab, select **agent-skills**, and confirm install.
   - Choose **User** (all workspaces on this machine) or **Project / workspace** (current workspace only)—same meaning as in the IDE flow below.
4. Use skills in the terminal session:
   - **`/skills`** — list discovered skills and validation hints
   - **`/review-pull-request`** (or any skill name) — run that skill for the next turn
   - Or describe the task; the agent can apply a skill when its description matches

There is no documented **`agent plugin install`** command yet; registering the marketplace is CLI-automatable, but choosing the plugin and scope is done through **`/plugin`** in an `agent` session (or once via the IDE **Customize** flow, which also satisfies the CLI for **user** scope).

**Load a local checkout without marketplace install** (development or air-gapped):

```bash
agent --plugin-dir /path/to/agent-skills
```

You can pass **`--plugin-dir`** multiple times. Alternatively, clone into `~/.cursor/plugins/local/agent-skills/` (see [Local plugin development](#local-plugin-development)) and start a new `agent` session.

#### Cursor IDE (desktop app)

1. Open **Customize** in the sidebar.
2. Choose **From GitHub Repository** and enter:
   `https://github.com/kensodemann/agent-skills`
3. Install the **agent-skills** plugin.
4. Choose scope when prompted:
   - **User** — Available in **every workspace** on your machine (good default if you want these skills everywhere). Also available in **`agent`** sessions on this machine.
   - **Project / workspace** — Tied to the **current workspace** only (good if only some repos should see them).

After install, skills appear under **Customize → Skills**. Invoke them from chat with `/skill-name` (for example `/review-pull-request`) or let the agent apply them when the task matches the skill description.

#### Local plugin development

To test changes without publishing:

1. Clone this repository into `~/.cursor/plugins/local/agent-skills/` (include `.cursor-plugin/` and `skills/`).
2. **IDE:** **Developer: Reload Window** (or restart Cursor) and confirm under **Customize**.
3. **CLI:** Start `agent` again, or run `agent --plugin-dir ~/.cursor/plugins/local/agent-skills` from any directory.

---

### Claude Code

Claude Code reads the `.claude-plugin/` manifest and loads skills from this repo when the plugin is installed.

#### Install from GitHub (recommended)

**One step in Claude Code 2.1.275+** (in the Claude Code session):

```text
/plugin install agent-skills --marketplace kensodemann/agent-skills
```

If the marketplace is not registered yet, Claude adds it from the GitHub repo automatically.

**CLI** (same result; useful for scripts):

```bash
claude plugin install agent-skills --marketplace kensodemann/agent-skills
```

Or add the marketplace explicitly, then install:

```bash
claude plugin marketplace add kensodemann/agent-skills
claude plugin install agent-skills
```

**Install scope** (`-s` / `--scope`):

| Scope | Meaning |
| ----- | ------- |
| `user` (default) | Plugin available for **you** across projects (stored in user settings). |
| `project` | Plugin tied to the **current project** (checked in or referenced from project config). |
| `local` | Machine-local install for experimentation; not shared as project config. |

List installed plugins: `claude plugin list`.

#### Release zip (optional)

Tagged releases attach `agent-skills-vX.Y.Z.zip` containing only the `skills/` tree. You can unzip individual skill folders into a manual path (see below) without installing the full plugin.

---

### Manual

Manual setup means copying **one or more** folders from `skills/<skill-name>/` (each folder contains a `SKILL.md`) into a directory your agent already scans. You do **not** need to copy `.claude-plugin/` or `.cursor-plugin/` unless you are packaging your own plugin.

Use manual install when you want **only some skills**, a **fork or pin** of one skill, or to **vendor** skills into a repo’s git history.

#### Where skills can live

Paths are grouped by **who sees them** and **which tools** pick them up.

| Location | Scope | Cursor | Claude Code | Typical use |
| -------- | ----- | :----: | :---------: | ----------- |
| `~/.cursor/skills/<skill-name>/` | User (this machine) | Yes | — | Personal default in Cursor for all projects |
| `.cursor/skills/<skill-name>/` | Project (this repo) | Yes | — | Team-shared Cursor skills committed with the project |
| `~/.agents/skills/<skill-name>/` | User | Yes | — | Same as `~/.cursor/skills/` (alternate Agent Skills path) |
| `.agents/skills/<skill-name>/` | Project | Yes | — | Same as `.cursor/skills/` (alternate path) |
| `~/.claude/skills/<skill-name>/` | User | Yes* | Yes | Personal skills; Claude also auto-loads `@skills-dir` plugins here |
| `.claude/skills/<skill-name>/` | Project | Yes* | Yes | Project-vendored skills; shared via git |
| `~/.codex/skills/<skill-name>/` | User | Yes* | — | Codex-compatible alias (Cursor discovers it) |
| `.codex/skills/<skill-name>/` | Project | Yes* | — | Project Codex-compatible alias |

\*Cursor treats `.claude/skills/` and `.codex/skills/` as **compatibility** paths—handy if you already use those layouts for Claude Code and want one copy on disk for both tools.

**What “user” vs “project” means**

- **User** paths live under your home directory. They apply across repositories on this machine, are **not** committed to git, and are appropriate for personal preferences or trying a skill before adding it to a repo.
- **Project** paths live inside a repository (or monorepo subtree). They are **version-controlled** with the project, so everyone who clones the repo gets the same skills. In monorepos, nested folders such as `apps/web/.cursor/skills/` are supported; skills there are scoped to that part of the tree.

**Cloud / remote agents (Cursor)** — User skills under `~/.cursor/skills/` can be synced for cloud agents via **Settings → Agents → Sync Skills for Cloud Agents**. Project skills ride along when the remote environment has the repo checked out.

#### Example: copy one skill into a project

From this repository’s root:

```bash
mkdir -p /path/to/your-repo/.cursor/skills
cp -R skills/review-pull-request /path/to/your-repo/.cursor/skills/
```

Commit `.cursor/skills/review-pull-request/` if you want the team to share it.

#### Example: symlink while developing this repo

```bash
mkdir -p ~/.cursor/skills
ln -s /path/to/agent-skills/skills/review-pull-request ~/.cursor/skills/review-pull-request
```

Reload the window so Cursor rescans skill directories.

#### Plugin install vs manual copy

| Approach | Pros | Tradeoffs |
| -------- | ---- | --------- |
| **Plugin install** (Cursor or Claude) | All skills, metadata, and updates through the marketplace; matches this repo’s release workflow | Installs the whole plugin bundle |
| **Manual copy** | Cherry-pick skills; full control in git | You manage updates yourself; must use a discovery path (not root `skills/` here) |

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for skill layout, validation, and releases.
