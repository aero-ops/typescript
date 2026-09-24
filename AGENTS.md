# AeroOps TypeScript — Agent Guide

This file is the entry point for any agent working in this repo. Before
making any changes, consult and follow [`CONTRIBUTING.md`](./CONTRIBUTING.md).

## What this repo is

`@aero-ops/typescript` is a small, internal package that standardizes the
`tsconfig` used across AeroOps monorepo projects. It has no application code,
no runtime, no tests — it exists solely to publish `tsconfig.base.json` as a
shared config other AeroOps TypeScript projects extend from.

There is exactly one file that matters functionally: `tsconfig.base.json`.
Everything else in the repo (`package.json`, `bunfig.toml`, `commitlint.config.cjs`,
`release-please-config.json`, `.editorconfig`) exists to package, lint, and
release that one file consistently.

**External contributions are not accepted.** Issues and PRs from outside the
organization are closed without review.

---

## Repo layout

| Path | Purpose |
|---|---|
| `tsconfig.base.json` | The shared config other AeroOps projects extend (`@aero-ops/typescript/base.json`). Any change here is a breaking or behavior change for every consumer — treat edits with care. |
| `tsconfig.json` | Local tsconfig for this repo itself (extends the base, type-checks `commitlint.config.cjs`). |
| `package.json` | Publishes `tsconfig.base.json` via the `./base.json` export. `check-types` is the only script. |
| `bunfig.toml` | Bun install config (`hoisted` linker, `exact` versions). |
| `commitlint.config.cjs` | Enforces Conventional Commits via `@commitlint/config-conventional`. |
| `release-please-config.json` | Drives automated releases/changelog via release-please (`simple` release type, package name `typescript`). |
| `.release-please-manifest.json` | release-please's version bookkeeping — do not hand-edit. |

There are no `apps/`, `packages/`, or `src/` directories, no test suite, and
no application logic. Do not invent any of that scaffolding unless the user
explicitly asks for the repo's scope to grow.

---

## Working on `tsconfig.base.json`

- Every option is deliberate; this is a consumer-facing contract for other
  AeroOps repos. Do not add or remove `compilerOptions` without a clear
  reason, and call out in the PR description what downstream behavior
  changes (e.g. tightening `strict`-family flags is a breaking change for
  consumers with existing lint/type debt).
- Keep it framework-agnostic. It targets `ESNext`/`bundler` resolution and Bun
  types (`types: ["bun"]`) — don't fold in options specific to one downstream
  app (e.g. React JSX settings) unless the whole monorepo is expected to need
  them.
- After changing it, run `bun run check-types` here to confirm the repo's own
  `tsconfig.json` (which extends the base) still type-checks.

---

## Conventions

- **Branching:** single long-lived branch, `main`. No `develop`/`staging`.
  Changes land via PR, or a direct commit at the maintainer's discretion.
- **Commits:** [Conventional Commits](https://www.conventionalcommits.org/),
  enforced by commitlint on `commit-msg`. Format:
  `<type>(<optional scope>): <subject>`. Allowed types: `feat`, `fix`, `docs`,
  `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`.
- **Releases:** automated via release-please based on commit types (`feat` →
  minor, `fix`/`perf` → patch, pre-1.0 semantics per
  `release-please-config.json`). Don't hand-edit `CHANGELOG.md` or the
  manifest.
- **Style:** no semicolons is not enforced here (no formatter config exists
  yet); follow `.editorconfig` and match existing file style. Keep
  `package.json`/config JSON files minimal and hand-readable.
- No emojis in code, commits, or docs.

---

## Quality gate

Before committing:

```bash
bun install
bun run check-types
```

If it fails locally, it fails in CI. There is no lint step, test suite, or
build step beyond type-checking — don't add one speculatively.

---

## Definition of Done

A change is done when:

1. `bun run check-types` passes.
2. The commit message follows Conventional Commits and passes commitlint.
3. If `tsconfig.base.json` changed, the PR description states the practical
   effect on downstream AeroOps projects that extend it.

---

## Guardrails

- Never bypass commitlint or skip git hooks.
- Never hand-edit `.release-please-manifest.json` or `CHANGELOG.md`.
- Don't scaffold `apps/`, `packages/`, tests, or CI workflows into this repo
  on your own initiative — its scope is intentionally limited to shared
  TypeScript config. If a task seems to require more, confirm with the user
  first.
