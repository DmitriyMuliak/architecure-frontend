# Architecture — Guidelines

> **Handbook** level: explains the concepts and shows how to apply the decisions made in
> [Standards](../Standards/index.md). Rules at this level hold in code review, but the strategic
> contract lives there. Implementations live in [Reference-Recipes](../Reference-Recipes/).

## Module boundaries

Detail of `FE-ARCH-01`. The boundary is defined by **path depth**, not by directory name: anything
not re-exported from the module's `index.ts` is internal by definition.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-ARCH-01 | A module **MUST** expose its public surface through `index.ts`; importing deeper than the module root is banned. | `eslint-plugin-boundaries » boundaries/dependencies` † |
| FE-ARCH-02 | Code outside a module **MUST NOT** import a file inside it — only through its `index.ts`. | `eslint-plugin-boundaries » boundaries/dependencies` † |
| FE-ARCH-04 | Code **MUST NOT** introduce circular dependencies. | `dependency-cruiser` |
| FE-ARCH-03 | Barrel files **MUST NOT** use `export *`; every export is explicit. | review |
| FE-ARCH-07 | Imports within a module **MUST** be relative; imports across modules **MUST** use the `@/` alias. | `eslint-plugin-import » import/no-relative-packages` † |
| FE-ARCH-09 | A barrel **MUST** sit only at a module or FSD-slice boundary, never in every folder. | review |

> The `_private/` directory is no longer a standard. In `packages/ui` it remains as a historical
> local convention together with the in-house `@futuremedia/eslint-plugin-module-boundary` — existing
> code does not need rewriting, but new modules and new projects do not introduce it.

## Why two tools, not one

`eslint-plugin-boundaries` and `dependency-cruiser` **do not duplicate each other** — this is neither
legacy nor an oversight, so there is nothing "redundant" to remove.

| | Answers the question | Why this one |
| --- | --- | --- |
| `eslint-plugin-boundaries » boundaries/dependencies` | who may import whom (`FE-ARCH-01`, `-02`, `-08`) | ESLint → an error in the editor before the commit; needs no separate CI job, rides along with lint |
| `dependency-cruiser` | whether there are cycles (`FE-ARCH-04`) | `eslint-plugin-boundaries` **has no cycle rules at all** |

The ESLint alternative for cycles (`import/no-cycle`) is deliberately not used: ESLint works
per-file, so it walks the import graph again for every file, whereas dependency-cruiser builds it
once. On a large repo that is the difference between an acceptable lint and a lint people turn off.

The plugin's canonical rule is `boundaries/dependencies`. `boundaries/element-types`, `entry-point`,
`external`, and `no-private` are deprecated and replaced by it.

## Barrels and tree-shaking

A barrel does not break tree-shaking given ESM and a correct `sideEffects`, but it forces the
compiler to parse the whole chain to check for side effects. `FE-ARCH-10` (index) closes the most
expensive case — mixing server and client modules in one export.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-ARCH-10 | A barrel **MUST NOT** mix server and client modules in one export. | review |
| FE-ARCH-11 | Every package **MUST** declare `sideEffects` in `package.json` — `false` or an exact list. | review |

## Monorepo

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-ARCH-05 | Internal dependencies between packages **MUST** use `workspace:*`, and every package **MUST** declare its `exports`. | review |
| FE-ARCH-06 | Code **SHOULD** move into `packages/` only once a second app needs it and it holds no feature-specific logic. | review |
