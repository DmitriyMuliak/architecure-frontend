# Tooling and workflow — Guidelines

> **Handbook** level: explains the concepts and shows how to apply the decisions made in
> [Standards](../Standards/index.md). Rules at this level hold in code review, but the strategic
> contract lives there. Implementations live in [Reference-Recipes](../Reference-Recipes/).

## Lint and dead code

Detail of `FE-QC-01`.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-QC-01 | CI linting **MUST** use the type-checked `typescript-eslint` config plus the `react`, `react-hooks`, and `jsx-a11y` plugins. | `eslint.config.js` † |
| FE-REACT-06 | All projects **MUST** enable the `eslint-plugin-react-hooks` `recommended-latest` preset — it already carries the React Compiler diagnostics. | `eslint.config.js` |
| FE-QC-02 | Prettier **MUST** be the sole formatter; ESLint stylistic rules stay off. | CI job `format` |
| FE-QC-03 | Every `eslint-disable` comment **MUST** state its specific reason inline. | `eslint-plugin-eslint-comments` † |
| FE-QC-04 | The lint job **MUST** run with `--max-warnings=0`. | CI † |
| FE-QC-05 | Every publishable package **MUST** run a dead-code (knip) and circular-dependency check in CI. | CI (today: `ui` only) |
| FE-QC-06 | Build artifacts (`dist`, `.next`, `storybook-static`, `coverage`) **MUST** be excluded from lint, format, and knip scope. | ignore files |

## Dependencies

Detail of `FE-DEP-01`.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-DEP-02 | CI **MUST** install dependencies from a committed, frozen lockfile. | `--frozen-lockfile` |
| FE-DEP-04 | Dependency install / build scripts **MUST** be explicitly allow-listed, never run unreviewed. | `onlyBuiltDependencies` |
| FE-DEP-05 | Direct dependency versions **MUST** be pinned or narrowly ranged — no wildcards or floating ranges. | review |

## Git

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-WF-01 | Branches **MUST** fork from `main`, be short-lived, and follow `<type>/<ticket>-<slug>`. | review |
| FE-WF-04 | Pre-commit **MUST** finish in ≤ 10 s and pre-push in ≤ 60 s; anything slower lives in CI only. | convention |
| FE-WF-05 | `--no-verify` **MUST NOT** be used outside a documented emergency acknowledged by a reviewer. | process |
| FE-STACK-02 | Major upgrades of core libraries **MUST** go in their own PR, never inside a feature. | review |

## Configuration

Detail of `FE-WF-08` and `FE-NEXT-11`.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-NEXT-11 | Server configuration **MUST** be read from `process.env` at request time, not baked into the build. | review |
| FE-WF-07 | Real `.env*` files **MUST NOT** be committed; only `.env.example` is tracked. | `.gitignore` |
| FE-WF-08 | App startup **MUST** validate required environment variables against a schema and fail fast. | review † |

## CI and deployment

Detail of `FE-WF-03` — which checks exactly have to be green, and what happens after merge.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-WF-06 | PR CI **MUST** pass frozen install, lint, format, typecheck, unit, interaction, and visual tests, and build before merge. | `quality.yml` |
| FE-WF-09 | Production deploys **MUST** run one immutable artifact, gated on green CI, with a documented rollback. | pipeline † |
