# React, state and forms — Guidelines

> **Handbook** level: explains the concepts and shows how to apply the decisions made in
> [Standards](../Standards/index.md). Rules at this level hold in code review, but the strategic
> contract lives there. Implementations live in [Reference-Recipes](../Reference-Recipes/).

## Components and hooks

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-REACT-01 | `useEffect` **MUST NOT** compute derived state, handle user events, or fetch data available at render time. | `eslint-plugin-react-hooks` (partial) + review |
| FE-REACT-02 | `useMemo`, `useCallback`, and `memo` **MUST NOT** be added without a profiled, confirmed regression. | review |
| FE-REACT-03 | Stateful logic shared by two or more components **MUST** be extracted into a `use`-prefixed hook. | review |
| FE-REACT-04 | Components **SHOULD** expose composition slots instead of accumulating boolean variant flags. | review |
| FE-REACT-05 | `[R19]` Components **MUST** accept `ref` as a plain prop instead of `forwardRef`. | `eslint » no-restricted-imports` † |
| FE-REACT-07 | New projects **MUST** enable React Compiler. | `next.config.ts` |
| FE-REACT-08 | A separate `eslint-plugin-react-compiler` **MUST NOT** be used — the diagnostics are built into `eslint-plugin-react-hooks`. | review |

## Data transport

Detail of `FE-DATA-07` and `FE-DATA-08` — the third leg of the triad, plus the one case people
regularly try to push through a Server Action.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-DATA-09 | Server Components **MUST** read from the data source directly, never through this app's own Route Handlers. | review |
| FE-DATA-14 | Heavy file uploads **MUST** go straight to the backend or through a streaming Route Handler — never through a Server Action, which buffers the whole body into memory. | review |

## State taxonomy

Detail of `FE-DATA-11`.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-DATA-11 | Cross-route client state **MUST** live in a Zustand-class store; Context is only for low-frequency injection (theme, locale, session). | review |
| FE-DATA-01 | Data available at request time **MUST** be fetched in Server Components, not in a client effect or query. | review |
| FE-DATA-02 | Component state **MUST NOT** duplicate a value derivable from props, the URL, or a cache. | review |
| FE-DATA-03 | Shareable UI state (filters, tabs, pagination) **SHOULD** live in URL search params. | review |
| FE-DATA-04 | React Context **MUST NOT** be used for high-frequency updates or as a server-state cache. | review |

## Forms

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-DATA-12 | Forms **MUST** be built on React Hook Form + Zod; simple server-action forms **MAY** use `useActionState`. | review |
| FE-DATA-13 | New projects **MUST** use `zod/mini`; existing ones migrate to it gradually. | review |
| FE-DATA-05 | A form **MUST** be validated by one schema shared between the client and the server boundary. | review |
| FE-DATA-06 | Submit handlers **MUST** expose a pending state, and field errors **MUST** render at their field. | review |
