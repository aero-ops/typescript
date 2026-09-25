# @aero-ops/typescript

Base TypeScript configuration shared by all AeroOps monorepo projects.

This package contains no application code. It publishes `tsconfig.base.json` so
TypeScript projects can extend a single, standardized, Bun-compatible config.

> This is an internal AeroOps package. External contributions are not accepted.

## Usage

From a project with access to the internal AeroOps registry, install the
package as a dev dependency:

```bash
bun install -D @aero-ops/typescript
npm i -D @aero-ops/typescript
pnpm add -D @aero-ops/typescript
yarn add -D @aero-ops/typescript
```

Then extend the config in the project's `tsconfig.json`:

```json
{
  "extends": ["@aero-ops/typescript/base.json"],
  "include": ["src/**/*.ts"]
}
```

Project-specific options can be added under `compilerOptions` and take
precedence over the base config.

Because the config sets `"types": ["bun"]`, Bun's types must be available in the
consuming project. Install `@types/bun` as a dev dependency when needed:

```bash
bun install -D @types/bun
npm i -D @types/bun
pnpm add -D @types/bun
yarn add -D @types/bun
```

## Shared configuration

`tsconfig.base.json` applies the following rules:

| Option | Value | Purpose |
| --- | --- | --- |
| `target` | `ESNext` | Uses the latest JavaScript features. |
| `module` | `ESNext` | Keeps the output as ES modules. |
| `moduleResolution` | `bundler` | Resolves modules the way bundlers do. |
| `allowImportingTsExtensions` | `true` | Allows imports with the `.ts` extension. |
| `noEmit` | `true` | Prevents JavaScript output from being generated. |
| `incremental` | `true` | Allows incremental caching of type checks. |
| `lib` | `ESNext` | Enables the latest standard libraries. |
| `verbatimModuleSyntax` | `true` | Keeps imports and exports aligned with the syntax used in the code. |
| `strict` | `true` | Turns on TypeScript's strict checks. |
| `skipLibCheck` | `true` | Skips type checking of dependency declaration files. |
| `resolveJsonModule` | `true` | Allows importing JSON files. |
| `experimentalDecorators` | `true` | Enables experimental decorators. |
| `emitDecoratorMetadata` | `true` | Emits decorator metadata whenever there is output. |
| `allowSyntheticDefaultImports` | `true` | Allows synthetic default imports. |
| `esModuleInterop` | `true` | Improves interoperability between CommonJS and ES modules. |
| `forceConsistentCasingInFileNames` | `true` | Requires file names to be cased consistently across systems. |
| `isolatedModules` | `true` | Ensures every file can be transpiled on its own. |
| `noUncheckedIndexedAccess` | `true` | Accounts for possible `undefined` values when indexing. |
| `noUnusedLocals` | `true` | Reports unused variables and imports. |
| `noUnusedParameters` | `true` | Reports unused parameters. |
| `noFallthroughCasesInSwitch` | `true` | Prevents unintentional `switch` fallthrough. |
| `noImplicitOverride` | `true` | Requires `override` when overriding methods. |
| `types` | `["bun"]` | Includes Bun's global types. |

## Development

Prerequisites:

- Git
- Bun

Install the dependencies and run the type check:

```bash
bun install
bun run check-types
```

To validate Conventional Commits against `main`:

```bash
bun run lint:commit
```

This repository follows [Conventional Commits](https://www.conventionalcommits.org/)
and commitlint. Examples:

```text
fix(base): correct moduleResolution setting
docs: update usage instructions
chore: bump dependencies
```

Commit messages must be written in English, including the subject, body, and
optional scope.

Read [CONTRIBUTING.md](./CONTRIBUTING.md) for the full contribution flow.

## Structure

- `tsconfig.base.json`: the config shipped to consuming projects.
- `tsconfig.json`: the config used to type-check this repository itself.
- `package.json`: package metadata, exports, and scripts.
- `release-please-config.json`: automated release configuration.

## License

This project is proprietary and is governed by the terms described in
[LICENSE](./LICENSE). All rights reserved by AeroOps.
