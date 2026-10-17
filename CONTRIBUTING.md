# Contributing

Thank you for contributing to **agent-skills**. This document covers how to prepare changes and how maintainers publish a release.

## Skills layout

Each skill lives in its own folder under `skills/`:

```
skills/
  my-skill/
    SKILL.md
```

The `name` field in `SKILL.md` (first `name:` line in the front matter or header) **must match the folder name** exactly. The release workflow enforces this.

Before opening a pull request, you can validate the plugin locally:

```bash
npx -y @anthropic-ai/claude-code@2.1.300 plugin validate --strict .
```

## Releasing a version

Releases are **automated** when a semver Git tag is pushed. The [Release workflow](.github/workflows/release.yml) validates the repo, builds a zip of `skills/`, and creates a GitHub Release with notes and the zip attached.

### Prerequisites

- Merge release-related changes to the default branch (version bump, changelog, skills).
- You need permission to push tags and to let GitHub Actions create releases (`contents: write` is configured on the workflow).

### 1. Bump the plugin version

Set `version` in [`.claude-plugin/plugin.json`](.claude-plugin/plugin.json) to the new semver **without** a `v` prefix (for example `1.2.0`).

The Git tag you push later must be **`v` + that exact string** (for example tag `v1.2.0` for version `1.2.0`). If they differ, the release job fails.

### 2. Update the changelog

Add a section to [`CHANGELOG.md`](CHANGELOG.md) for the same version (no `v` prefix):

```markdown
## [1.2.0]

- Short description of what changed.
```

The workflow copies everything under that heading until the next `## [` heading into the GitHub Release notes. If the section is missing or empty, the release fails.

### 3. Confirm skills and plugin validation

Ensure every `skills/*/SKILL.md` has a `name` that matches its parent folder name.

Run strict validation locally (same command as CI):

```bash
npx -y @anthropic-ai/claude-code@2.1.300 plugin validate --strict .
```

Fix any errors before tagging.

### 4. Commit, tag, and push

Commit the version and changelog updates, then create an annotated or lightweight tag matching **`v*.*.*`** (three numeric components):

```bash
git tag v1.2.0
git push origin main
git push origin v1.2.0
```

Only **pushing the tag** triggers the Release workflow. Pushing branch commits alone does not publish a release.

### 5. What CI does

On tag push `v*.*.*`, the workflow:

1. Checks that `v$(jq -r .version .claude-plugin/plugin.json)` equals the tag name.
2. Runs `plugin validate --strict` on the repository root.
3. Verifies skill folder names match each skill’s `name` field.
4. Extracts release notes from `CHANGELOG.md` for that version.
5. Creates `agent-skills-<tag>.zip` containing the `skills/` directory.
6. Publishes a GitHub Release titled with the tag, with the changelog body and the zip as a release asset.

Monitor the **Release** workflow run on the Actions tab. If it fails, fix the reported issue, amend or add commits as needed, **move or recreate the tag** on the corrected commit, and push the tag again.

### Release checklist

- [ ] `plugin.json` `version` updated (no `v` prefix).
- [ ] `CHANGELOG.md` has a non-empty `## [x.y.z]` section for that version.
- [ ] Skill names match folder names; `plugin validate --strict .` passes locally.
- [ ] Tag `vx.y.z` matches `plugin.json` version.
- [ ] Tag pushed to `origin`; Release workflow succeeded.
