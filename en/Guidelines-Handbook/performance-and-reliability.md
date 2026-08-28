# Performance and reliability — Guidelines

> **Handbook** level: explains the concepts and shows how to apply the decisions made in
> [Standards](../Standards/index.md). Rules at this level hold in code review, but the strategic
> contract lives there. Implementations live in [Reference-Recipes](../Reference-Recipes/).

## Page weight

Detail of `FE-PERF-02`. The gate and the diagnosis are different tools: `size-limit` answers "did we
fit the budget" and fails CI; the analyzer answers "why didn't we" and is run by hand. The analyzer
is never a gate. `size-limit` **MUST** measure built chunks (`@size-limit/file` over
`.next/static/chunks`) rather than re-bundling the code with its own webpack.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-PERF-12 | Every app **MUST** have an `analyze` script: `[N16]` — `next experimental-analyze`, `[N15]` — `@next/bundle-analyzer`. | `package.json` |
| FE-PERF-13 | `[N16]` `@next/bundle-analyzer` **MUST NOT** be used — under Turbopack it silently produces no report. | review |
| FE-PERF-03 | Images **MUST** use `next/image`, fonts `next/font`, and third-party scripts `next/script` with a non-blocking strategy. | `@next/eslint-plugin-next » no-img-element` |
| FE-PERF-04 | Heavy client-only modules **MUST** be isolated behind `next/dynamic` at the smallest leaf. | review |
| FE-PERF-05 | Lists rendering more than 100 items **MUST** be virtualized. | review |

## Error handling

Detail of `FE-PERF-06`.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-PERF-06 | A `catch` block **MUST NOT** discard an error without logging, rethrowing, or rendering fallback UI. | custom lint rule † |
| FE-PERF-07 | User-facing error messages **MUST NOT** expose stack traces, internal identifiers, or raw backend payloads. | review |
| FE-PERF-08 | Automatic retries **MUST** be bounded, backed off, and limited to idempotent operations. | review |

## Observability

Detail of `FE-PERF-09`.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-PERF-10 | Every request to a backend API **MUST** carry a correlation / request ID header. | review |
| FE-PERF-11 | Production code **MUST NOT** use `console.log`; logs and telemetry **MUST NOT** contain tokens, PII, or full request bodies. | `eslint » no-console` |
