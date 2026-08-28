# TypeScript — Guidelines

> **Handbook** level: explains the concepts and shows how to apply the decisions made in
> [Standards](../Standards/index.md). Rules at this level hold in code review, but the strategic
> contract lives there. Implementations live in [Reference-Recipes](../Reference-Recipes/).

## Escape hatches

Detail of `FE-TS-02`.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-TS-02 | Code **MUST NOT** use `any`, except for a caught error value; use `unknown` and narrow. | `typescript-eslint » no-explicit-any` |
| FE-TS-03 | A suppressed type error **MUST** use `@ts-expect-error` with a justification; `@ts-ignore` is banned. | `typescript-eslint » ban-ts-comment` |
| FE-TS-08 | Type assertions and non-null `!` **MUST** carry a comment explaining why inference is impossible. | review |

## Shape of types

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-TS-05 | DTO and API types **MUST** be inferred from that one schema, never hand-duplicated. | review |
| FE-TS-06 | New code **MUST NOT** declare a TypeScript `enum`; use a union literal or an `as const` object. | `eslint » no-restricted-syntax` † |
| FE-TS-07 | Object shapes **MUST** use `type`; `interface` is only for declaration merging or class contracts. | `typescript-eslint » consistent-type-definitions` |

## Compiler flags

Detail of `FE-TS-01`.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-TS-09 | New packages **SHOULD** enable `noUncheckedIndexedAccess` and `exactOptionalPropertyTypes` from day one. | `tsconfig` |
