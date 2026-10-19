---
name: update-deps
description: >-
  Update patch and minor dependencies in a pnpm or npm project, leave major
  upgrades unapplied, and write update-report.md. Use when the user asks to
  update, bump, or upgrade npm or pnpm dependencies, or to refresh outdated
  packages.
---

# Update Dependencies

Update **direct** dependencies in a `pnpm` or `npm` project. Take patch and minor upgrades. Do not take major upgrades. Leave the tree green, and write `update-report.md` at the repository root.

Do not commit, push, or stage. Do not edit application source to make an upgrade pass. Do not run `npm audit fix` or take a major in order to clear a warning.

## Version policy

Compare the **currently specified** version to the newest published version.

| Delta | Action |
| ----- | ------ |
| Patch | Take it |
| Minor | Take it, and record new features in the report |
| Major | Do not take it. Research breaking changes and record them in the report |

- Only consider the same release channel. If the installed version is stable, the target is the latest **stable** version with the same major. Do not move onto a pre-release, `next`, or `beta` tag.
- "Take it" means the installed version becomes that target, and the `package.json` range operator stays as it is (`^`, `~`, or an exact pin). `^1.2.3` → `^1.8.1` when `1.8.1` is the latest `1.x`. `~1.2.3` → `~1.8.1`. An exact pin stays exact.
- A `peerDependencies` range that would move its major is a major upgrade. Do not take it.
- Do not bump `engines`, `packageManager`, Node, or pnpm itself.

## Workflow

### 1. Detect the project

Work from the repository the user has open, or the package they named.

If the project is not pnpm or npm, stop. Do not improvise Yarn, Bun, or another installer.

### 2. Baseline

The three scripts are `build`, `test`, and `lint`. If any of them is missing from the root and from every workspace package, stop. Do not update.

Run them with the detected package manager.

Before that, run a normal install and save the full install output. This is the warning baseline.

If install, build, test, or lint fails, stop. Change nothing. Do not write `update-report.md`. Tell the user which command failed.

### 3. Classify

Record, for every direct dependency, the package name, manifest path, original specifier, and installed version. Keep this record until the run is finished. Copy the lockfile and every manifest you might edit to a temp directory outside the repo, and delete that temp directory when the run is finished.

List outdated packages with `pnpm outdated --format json` or `npm outdated --json`. Confirm the latest stable version with `npm view <pkg> version` (or `npm view <pkg> versions --json` when you need the set). Classify against the installed version, not against the minimum of the range. `wanted` is only the top of the current range; do not use it as the target when a newer same-major version exists.

### 4. Apply patch and minor updates

Skip every major. Update the remaining packages by editing the specifier, then run one install (`pnpm install` or `npm install`) so the lockfile matches.

### 5. Verify

Run `build`, `test`, and `lint` with the new set of dependencies installed.

Treat the update as broken when any script fails, or when the install prints a **new** warning or error that was not in the baseline. Ignore funding notices and the package manager's own "new version available" line. Deprecation warnings, peer-dependency warnings, and install errors count.

### 6. Roll back failures

When verification fails, restore a clean tree. Do not patch source to keep the new version.

1. Find the smallest set of upgraded dependencies responsible. Bisect when several were upgraded together: restore half, reinstall, and re-run the failing check.
2. Roll each responsible dependency back to the **installed version recorded in step 3**, including its original specifier and lockfile resolution. Restore the temp copies, then re-apply only the upgrades that still pass.
3. Re-run install, `build`, `test`, and `lint`. The final tree must match the baseline: same three scripts clean, and no new install warnings.
4. If the failure remains after every candidate is rolled back, restore the temp copies entirely, reinstall, and report that the update was aborted.

Each rolled-back dependency becomes an issue in the report. A minor that was rolled back does not also appear under New Features.

### 7. Research

Use release notes, changelogs, or migration guides from the package. Prefer the repository linked from `npm view <pkg> repository`. Do not invent features or breaking changes. If notes for a package cannot be found, say so in the chat summary and omit that package from the section.

- **New features** — For each **applied** minor (and any applied patch whose notes actually add a feature), list distinct user-facing features introduced after the old installed version, up through the version now installed. Skip bugfix-only releases.
- **Breaking changes** — For each dependency whose latest version is a new major, research the breaking changes on the way from the installed major to that latest major. Do not install it. For each change, decide whether it applies by searching this project for the affected API, import, config, or behavior.

### 8. Write the report

Write `update-report.md` at the repository root. Overwrite an older report from this skill. Always include all three headings. Under a heading with no entries, write `None.`

In the chat, summarize counts only: packages upgraded, packages rolled back, majors left in place, and the report path. Do not paste the report.

## Report format

Tags are three digits, start at `001`, and number independently inside each section. Put the tag on its own line directly under the heading it belongs to.

```markdown
# Issues

## <package> <installed> → <attempted>

[ISS-001]

**Rolled back to:** <installed version>

**Synopsis:** What failed (script name, or the new install warning or error) and the relevant output.

**Proposed fix:** A concrete fix when one applies. Omit this line when none does.

# New Features

## <package> (<installed> → <applied>)

### <feature name>

[FEAT-001]

What the feature is, and a link to the release note or changelog.

# Breaking change

## <package> (<installed> → <latest>, not upgraded)

### <breaking change>

[BRK-001]

**Applies to this project:** Yes

What changed, where this project uses it (file and symbol), and a suggested update with a code example taken from that usage. Do not apply the example.

### <another breaking change>

[BRK-002]

**Applies to this project:** No

What changed, and why this project does not hit it.
```

- **Issues** — One `##` per rolled-back dependency. When two dependencies only fail together, say that in each synopsis.
- **New Features** — One `##` per library, alphabetical. One `###` per distinct feature. Number `[FEAT-NNN]` across the whole section.
- **Breaking change** — One `##` per library, alphabetical. One `###` per specific change. Number `[BRK-NNN]` across the whole section. Always state whether it applies. Include a suggested-update example only when it applies.

## Example shape

The following shows structure only. Replace every fact with the packages, notes, and project usage from the current run.

````markdown
# Issues

## left-pad 1.3.0 → 1.4.0

[ISS-001]

**Rolled back to:** 1.3.0

**Synopsis:** `pnpm run test` failed in `src/pad.spec.ts`. `1.4.0` returns `""` for an empty string; the test expects `"  "`.

**Proposed fix:** Assert the new empty-string result in `src/pad.spec.ts`, then retry `1.4.0`.

# New Features

## vite (5.4.2 → 5.5.0)

### Environment API

[FEAT-001]

Vite 5.5 can run several environments in one process. See the 5.5 changelog.

# Breaking change

## react (18.3.1 → 19.0.0, not upgraded)

### propTypes removed from the type definitions

[BRK-001]

**Applies to this project:** Yes

`src/components/Button.tsx` assigns `Button.propTypes`.

**Suggested update:**

```tsx
type ButtonProps = { label: string }

export function Button({ label }: ButtonProps) {
  return <button>{label}</button>
}
```

Remove the `Button.propTypes` assignment and type the props instead.
````
