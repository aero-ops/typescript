# Bug: npm is not synced after merging (`docs`/`ci`/`chore` merges never publish)

> Status: **open**. This document is the bug report of record while GitHub
> Issues are disabled for this repository (`.github/settings.yml` has
> `has_issues: false`). Labels that would apply: `bug`, `ci`, `release`.

## Summary

Merging to `main` does not reliably sync the package to npm. The automated
`Publish Package` workflow has **never run**, and the version currently on npm
(`1.0.0`) was pushed by a **manual local `npm publish`**. On top of that, the
artifact on npm is already out of sync with `main`.

## Current state

| Check | Result |
| --- | --- |
| `gh run list --workflow=publish.yml` | **no runs, ever** |
| `gh release list` | only `v1.0.0`, published `2026-09-24T19:40:36Z` |
| `publish.yml` merged (PR #6) | `2026-09-25T18:20Z` (**after** the only release) |
| `npm view @aero-ops/typescript dist-tags` | `latest: 1.0.0`, created `2026-09-25T18:25:14Z` |

The npm `1.0.0` was created 5 minutes after `publish.yml` landed on `main`, with
no corresponding workflow run, so it came from a local machine.

### Drift already shipped

`main` has commits that npm does not have. `npm pack @aero-ops/typescript@1.0.0`
returns a tarball with the **old Portuguese README**:

```
package/LICENSE
package/package.json
package/README.md      <- starts with "Configuração base de TypeScript compartilhada..."
package/tsconfig.base.json
```

while `main` is already in English (PR #4, PR #7). Consumers installing from npm
get stale documentation.

## Root cause

`publish.yml` triggers only on `release: types: [published]`, and release-please
only creates a release when it finds **user-facing** commits. In
`release-please-config.json`, every type except `feat`, `fix`, `perf`, `revert`
and `refactor` is marked `"hidden": true`.

So for the `Release Please` run on the `main` push of PR #7 (run `36173982034`):

```
Splitting 3 commits by path
Building candidate release pull request for path: .
commits: 3
Considering: 3 commits
No user facing commits found since 0519f89d0b5ffab940c59b1d9987b2952d224105 - skipping
```

The 3 commits since `v1.0.0` are all hidden:

```
db38e8a docs: translate README to English (#7)
fea976b ci(release): publish package to npm on release (#6)
d3ac636 docs: add repository and contribution documentation (#4)
```

No release PR, no tag, no GitHub release, therefore `publish.yml` never fires.
Only `feat`/`fix`/`perf`/`revert`/`refactor` merges publish automatically.

## Steps to reproduce

1. Merge a PR into `main` that changes the published artifact
   (`tsconfig.base.json`, `package.json`, `README.md`) but only contains
   `docs`/`ci`/`chore` commits.
2. `Release Please` finishes green and logs
   `No user facing commits found ... - skipping`.
3. Observe there is no release PR, no tag, and no `Publish Package` run.
4. `npm view @aero-ops/typescript version` still returns the old version.
5. Workaround today: run `npm publish` locally from a machine with registry
   access.

## Expected

Every merge to `main` that changes the published artifact produces a new version
on npm, with no local step and no human handling of `NPM_TOKEN`.

## Actual

- Only `feat`/`fix`/`perf`/`revert`/`refactor` merges reach npm.
- `docs`/`ci`/`chore`/`style`/`test`/`build` merges are silently dropped, and
  the published artifact drifts from `main`.
- The one release that did exist (`v1.0.0`) predates `publish.yml`, so the
  automated path has never been proven end to end.

## Open risk to confirm

The workflow relies on npm trusted publishing (OIDC), which must be registered
once per package. Since the workflow has never executed, the registration has
never been validated; the next real release could fail with `ENEEDAUTH`.
`npm owner ls @aero-ops/typescript` currently lists only the personal account
`alsgy2001`, not the `aero-ops` organization. Please confirm that the trusted
publisher (org/user `aero-ops`, repo `typescript`, workflow filename
`publish.yml`) is registered, and that the org account can administer the
package.

## Possible fixes

1. **Un-hide the types that change the published artifact.** Make `docs` a
   visible section in `release-please-config.json` so doc-only merges bump the
   version and trigger a release (and show up in `CHANGELOG.md`).
2. **Publish on every push to `main` instead of on release.** Trigger on `push`
   to `main`, derive the version from the release-please manifest, and keep the
   existing "already on npm" guard to make the job idempotent.
3. **Document the gap** if neither of the above is wanted, and state in
   `CONTRIBUTING.md`/`AGENTS.md` that non-user-facing merges do not publish, so
   nobody has to discover it through a failed local publish.

## Acceptance criteria

- [ ] Merging a `docs`/`ci`/`chore` PR that touches the published artifact
      results in a new version on npm with no manual step.
- [ ] `npm pack @aero-ops/typescript@latest` contains the same `README.md` and
      `tsconfig.base.json` as `main`.
- [ ] The `Publish Package` workflow has at least one successful run, proving
      trusted publishing is configured.
- [ ] Re-publishing an existing version is a no-op, not a failure.
