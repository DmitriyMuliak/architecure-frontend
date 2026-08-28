# Frontend Standards

> **What this is.** The authoritative list of engineering rules for Future Media frontend work.
> Every entry is a rule, not advice. Read it in 5 minutes, cite it in code review by ID.
>
> **What this is not.** Not a handbook and not a tutorial. Reasoning, patterns, examples and
> anti-patterns live in the **Handbook**; step-by-step instructions live in **Recipes**.
> If a statement needs an example to be understood, it does not belong on this page.

`v0.1 (draft)` · Owner: _TBD_ · Last updated: 2026-08-23

---

## How to read this

| Keyword | Meaning at code-review time |
| --- | --- |
| **MUST** / **MUST NOT** | Blocker. The PR does not merge until it is resolved or an exception is recorded. |
| **SHOULD** / **SHOULD NOT** | Default position. Deviation is allowed but must be stated and accepted by the reviewer. |
| **MAY** | Explicitly permitted. Not a recommendation. |

**Markers**

- `[N16]` / `[N15]` — applies only to that Next.js major. `[R19]` — React 19 only.
- `†` — the named tool is **not installed yet**; the rule is review-only until it is adopted.
- Anything automatable **MUST NOT** be argued manually in review. Fix the tool instead.

**Scope.** Applies to all new frontend code and to any file a PR touches. Existing code is not
retro-fixed on sight. Exceptions: see [Governance](#governance).

---

## 1. Technology Stack

Core libraries are non-negotiable and identical across all products. New projects start on the
latest stable major; existing projects pin a major and upgrade deliberately.

| Concern | Library | Notes |
| --- | --- | --- |
| UI framework | React | 18 → 19; new projects 19 |
| Meta-framework | Next.js | **App Router only** |
| Language | TypeScript | `strict`; `[N16]` requires ≥ 5.1 |
| Styling | Tailwind CSS v4 | CSS-first `@theme`, no `tailwind.config.js` |
| Component behaviour | react-aria-components | the only sanctioned headless layer |
| i18n | next-intl | |
| Runtime validation | Zod | `zod/mini` in client-bundled packages |
| Unit / component tests | Vitest + React Testing Library | |
| UI docs & interaction tests | Storybook (+ `addon-vitest`) | |
| Visual regression & E2E | Playwright | |
| Package manager / build | pnpm + Turborepo | `packageManager` and Node version pinned per repo |

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-STACK-01 | A concern already covered by a core library **MUST NOT** be solved with a second library. | review |
| FE-STACK-02 | Core-library majors **MUST** be upgraded deliberately as their own PR, never inside a feature PR. | review |
| FE-STACK-03 | `[N16]` Projects **MUST** run Node ≥ 20.9; Node 18 is unsupported. | CI setup |

---

## 2. Architecture

→ Detail: `Standards/module-boundaries.md`, `Standards/monorepo-structure.md` _(planned)_

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-ARCH-01 | A module **MUST** expose its public surface through `index.ts` and keep internals in `_private/`. | `eslint-plugin-module-boundary` |
| FE-ARCH-02 | Code outside a module **MUST NOT** import from that module's `_private/`. | `eslint-plugin-module-boundary` |
| FE-ARCH-03 | Barrel files **MUST NOT** use `export *`; every export is explicit. | review |
| FE-ARCH-04 | Code **MUST NOT** introduce circular dependencies. | `dependency-cruiser` |
| FE-ARCH-05 | Internal package dependencies **MUST** use `workspace:*`, and every package **MUST** declare its `exports`. | review |
| FE-ARCH-06 | Code **SHOULD** move into `packages/` only once a second app needs it and no feature-specific logic remains. | review |
| FE-ARCH-07 | Imports within `_private/` **MUST** be relative; imports across modules **MUST** use the `@/` alias. | `eslint-plugin-module-boundary` |

---

## 3. TypeScript

→ Detail: `Standards/typescript-compiler-flags.md` _(planned)_

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-TS-01 | Every package **MUST** enable `strict`, `verbatimModuleSyntax`, `isolatedModules` and `noImplicitOverride`. | `tsconfig.base.json` |
| FE-TS-02 | Code **MUST NOT** use `any`, except for a caught error value; use `unknown` and narrow. | `no-explicit-any` |
| FE-TS-03 | A suppressed type error **MUST** use `@ts-expect-error` with a justification; `@ts-ignore` is banned. | `ban-ts-comment` |
| FE-TS-04 | Data crossing a trust boundary (API, form, URL, env) **MUST** be validated at runtime with a Zod schema. | review |
| FE-TS-05 | DTO and API types **MUST** be inferred from that one schema, never hand-duplicated. | review |
| FE-TS-06 | New code **MUST NOT** declare a TypeScript `enum`; use a union literal or `as const` object. | `no-restricted-syntax` † |
| FE-TS-07 | Object shapes **MUST** use `type`; `interface` is only for declaration merging or class contracts. | `consistent-type-definitions` |
| FE-TS-08 | Type assertions and non-null `!` **MUST** carry a comment explaining why inference is impossible. | review |
| FE-TS-09 | New packages **SHOULD** enable `noUncheckedIndexedAccess` and `exactOptionalPropertyTypes` from day one. | `tsconfig` |

---

## 4. Next.js

→ Detail: `Standards/caching-and-revalidation.md`, `Standards/environment-variables.md`,
`Standards/middleware-proxy.md`, `Standards/i18n-next-intl.md` _(planned)_

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-NEXT-01 | Routes **MUST** use the App Router; the Pages Router is banned. | CI check † |
| FE-NEXT-02 | Components **MUST** default to Server Components; `"use client"` is added only at interactive leaves. | review |
| FE-NEXT-03 | A module holding secrets or server SDKs **MUST** import `server-only`. | `server-only` (build error) |
| FE-NEXT-04 | Code **MUST** await `cookies()`, `headers()`, `draftMode()`, `params` and `searchParams`. | `tsc` (`[N16]` removes the sync escape hatch) |
| FE-NEXT-05 | A route relying on cached data **MUST** declare that intent explicitly via `fetch` options or segment config. | review |
| FE-NEXT-06 | Cache invalidation **MUST** use `revalidateTag` / `revalidatePath`, never a manual cache-busting workaround. | `tsc` on `[N16]` |
| FE-NEXT-07 | Middleware / proxy **MUST** be scoped with `matcher` and contain no blocking I/O or heavy computation. | review |
| FE-NEXT-08 | `[N16]` The interception file **MUST** be `proxy.ts` exporting `proxy`, on the Node runtime. | codemod + review |
| FE-NEXT-09 | Every top-level route segment **MUST** provide `loading.tsx`, `error.tsx` and `not-found.tsx`; the root also `global-error.tsx`. | CI script † |
| FE-NEXT-10 | SEO metadata **MUST** come from `generateMetadata` / the `metadata` export, never hand-written `<head>` tags. | `@next/next/no-head-element` |
| FE-NEXT-11 | Server configuration **MUST** be read from `process.env` at request time, never baked into the build. | review |
| FE-NEXT-12 | Localized routes **MUST** use next-intl's `[locale]` segment with negotiation in middleware / proxy. | review |
| FE-NEXT-13 | `'use server'` **MUST** appear only in dedicated `*.actions.ts` files; every other server module starts with `import 'server-only'`. | `no-restricted-syntax` † |
| FE-NEXT-14 | A BFF proxy Route Handler **MUST** forward an explicit header allow-list, use `redirect: 'manual'`, and send no CORS headers. | review |

---

## 5. React

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-REACT-01 | `useEffect` **MUST NOT** compute derived state, handle user events, or fetch data available at render time. | `react-hooks` (partial) + review |
| FE-REACT-02 | `useMemo`, `useCallback` and `memo` **MUST NOT** be added without profiler evidence of a real regression. | review |
| FE-REACT-03 | Stateful logic shared by two or more components **MUST** be extracted into a `use`-prefixed hook. | review |
| FE-REACT-04 | Components **SHOULD** expose composition slots instead of accumulating boolean variant flags. | review |
| FE-REACT-05 | `[R19]` Components **MUST** accept `ref` as a plain prop instead of `forwardRef`. | `no-restricted-imports` † |

---

## 6. State & Data

→ Detail: `Standards/state-management.md`, `Standards/forms.md` _(planned)_

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-DATA-01 | Data available at request time **MUST** be fetched in Server Components, not in a client effect or query. | review |
| FE-DATA-02 | Component state **MUST NOT** duplicate a value derivable from props, the URL, or a cache. | review |
| FE-DATA-03 | Shareable UI state (filters, tabs, pagination) **SHOULD** live in URL search params. | review |
| FE-DATA-04 | React Context **MUST NOT** be used for high-frequency updates or as a server-state cache. | review |
| FE-DATA-05 | A form **MUST** be validated by one schema shared between the client and the server boundary. | review |
| FE-DATA-06 | Submit handlers **MUST** expose a pending state, and field errors **MUST** render at their field. | review |
| FE-DATA-07 | Reads from a Client Component **MUST** go through the same-origin Route Handler proxy with TanStack Query, never through a Server Action. | review |
| FE-DATA-08 | A Server Action **MUST** be used only when the mutation touches the session, Next's cache, or needs a server-side redirect; other mutations use the browser client. | review |
| FE-DATA-09 | Server Components **MUST** read from the data source directly, never through this app's own Route Handlers. | review |
| FE-DATA-10 | Server-state on the client **MUST** be owned by TanStack Query — no hand-rolled refetch-on-focus, polling, or dedup logic. | review |

---

## 7. UI, Styling & Design System

→ Detail: `Standards/design-tokens.md`, `Standards/component-api-contract.md`,
`Standards/storybook-conventions.md` _(planned)_

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-UI-01 | Tailwind utilities **MUST** style all UI; plain CSS is reserved for `@theme`, `@utility` and vendor overrides. | review |
| FE-UI-02 | Colours and numeric values **MUST** come from semantic `@theme` tokens; raw hex/px needs a documented exception. | `eslint-plugin-better-tailwindcss` † |
| FE-UI-03 | `@apply` **MUST NOT** be used; extract a `@utility` or a component instead. | CI grep † |
| FE-UI-04 | Conditional and state-driven classes **MUST** be composed with `cx()` / `sortCx()` and `data-*`, never string concatenation. | review |
| FE-UI-05 | A Figma stroke excluded from auto-layout **MUST** be implemented as `inset-shadow` / `ring`, never a CSS `border`. | review |
| FE-UI-06 | Interactive primitives **MUST** be built on `react-aria-components`, not bespoke keyboard/focus handling. | review |
| FE-UI-07 | Variants **MUST** be discrete typed props or one string union — never a config object or stacked booleans. | review |
| FE-UI-08 | Shared components **MUST** expose `className` passthrough and `data-*` state; consumers never target internals. | `eslint-plugin-module-boundary` |
| FE-UI-09 | Every exported `packages/ui` component **MUST** ship co-located stories covering its default and interactive states. | review |
| FE-UI-10 | Inline `style` **MUST NOT** be used except for values computed at runtime. | review |

---

## 8. Accessibility

→ Detail: `Standards/accessibility-checklist.md` _(planned)_ · Automation catches a minority of
criteria; the rest is manual review.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-A11Y-01 | All shipped UI **MUST** conform to WCAG 2.2 Level AA. | `addon-a11y` + manual |
| FE-A11Y-02 | Semantic HTML **MUST** come before ARIA; `aria-*` only where semantics are insufficient. | `jsx-a11y` † |
| FE-A11Y-03 | Every interactive element **MUST** be operable by keyboard alone with a visible focus indicator. | manual |
| FE-A11Y-04 | Every interactive element **MUST** expose a non-empty accessible name. | `addon-a11y` |
| FE-A11Y-05 | Form inputs and their errors **MUST** be programmatically associated with a label. | `jsx-a11y` † |
| FE-A11Y-06 | Dialogs **MUST** trap focus while open and restore it on close. | manual |

---

## 9. Testing

| Test type | Covers | **MUST NOT** cover | Owner |
| --- | --- | --- | --- |
| Unit (Vitest, jsdom) | logic, hooks, utils, transforms | rendered output, integration | author |
| Storybook interaction (Vitest browser) | component rendering, interaction flows | navigation, real network, logic already unit-tested | author |
| Visual regression (Playwright) | pixel regression of stories | behaviour, a11y semantics | author; design-system owner approves baselines |
| E2E (Playwright) | critical cross-page journeys | component variants, pixel diffs, edge cases | QA team |

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-TEST-01 | New or changed behaviour **MUST** ship with a passing test before merge. | CI |
| FE-TEST-02 | Coverage thresholds **MUST** apply to changed code, never to a global repository percentage. | vitest thresholds † |
| FE-TEST-03 | Tests **MUST** assert user-visible behaviour, never internal state, props or class names. | review |
| FE-TEST-04 | Queries **MUST** prefer role, label or text; `data-testid` is a last resort. | `eslint-plugin-testing-library` † |
| FE-TEST-05 | Interactions **MUST** be simulated with `userEvent`, never `fireEvent`. | `eslint-plugin-testing-library` † |
| FE-TEST-06 | Async Server Components and Server Actions **MUST** be verified with Playwright, not RTL rendering. | review |
| FE-TEST-07 | Code owned by this codebase **MUST NOT** be mocked inside its own tests. | review |
| FE-TEST-08 | Tests **MUST NOT** depend on real network, real timers, or state shared across tests. | review |
| FE-TEST-09 | A flaky test **MUST** be quarantined and ticketed, never masked with retries. | review |

---

## 10. Security

→ Detail: `Standards/csp-contract.md`, `Standards/supply-chain-response.md` _(planned)_

Ordered by consequence. FE-SEC-01…04 are the ones that cause incidents.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-SEC-01 | Auth tokens and session state **MUST** live only in `httpOnly` + `Secure` + `SameSite` cookies, never in Web Storage. | review |
| FE-SEC-02 | Every Server Action and Route Handler **MUST** re-verify authorization itself; middleware / proxy is never a sufficient gate. | review (see CVE-2025-29927) |
| FE-SEC-03 | Client Component props and Server Action returns **MUST** be explicit DTOs — the RSC payload is publicly readable. | review |
| FE-SEC-04 | All client-supplied input **MUST** be validated server-side before use. | review |
| FE-SEC-05 | HTML passed to `dangerouslySetInnerHTML` **MUST** be sanitized first. | `react/no-danger` |
| FE-SEC-06 | User-supplied URLs used for `href`, navigation or redirects **MUST** be allowlisted, rejecting the `javascript:` scheme. | review |
| FE-SEC-07 | Secrets and PII **MUST NOT** appear in `NEXT_PUBLIC_` variables, client bundles, or logs. | secret scanning † |
| FE-SEC-08 | State-changing Route Handlers **MUST** verify `Origin` against `Host` themselves — Server Actions get this automatically. | review |
| FE-SEC-09 | `target="_blank"` links **MUST** carry `rel="noopener noreferrer"`, and `postMessage` handlers **MUST** check origin. | `jsx-no-target-blank` |
| FE-SEC-10 | Production CSP **SHOULD** use per-request nonces rather than `unsafe-inline`. | header smoke test † |

---

## 11. Performance & Reliability

→ Detail: `Standards/performance-budgets.md`, `Standards/observability-contract.md` _(planned)_

| Metric | Target (p75, field) | Gates merge |
| --- | --- | --- |
| LCP / INP / CLS | ≤ 2.5 s / ≤ 200 ms / ≤ 0.1 | no — RUM, post-deploy |
| TTFB | ≤ 800 ms | synthetic only |
| Initial client JS per route | _to be baselined_ | proposed |

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-PERF-01 | Production routes **MUST** meet the Core Web Vitals targets above at p75 in RUM. | Sentry RUM |
| FE-PERF-02 | Per-route JS, bundle and image budgets **MUST** be measured and gated in CI. | `size-limit` † |
| FE-PERF-03 | Images **MUST** use `next/image`, fonts `next/font`, and third-party scripts `next/script` with a non-blocking strategy. | `@next/next/no-img-element` |
| FE-PERF-04 | Heavy client-only modules **MUST** be isolated behind `next/dynamic` at the smallest leaf. | review |
| FE-PERF-05 | Lists rendering more than 100 items **MUST** be virtualized. | review |
| FE-PERF-06 | A `catch` block **MUST NOT** discard an error without logging, rethrowing, or rendering fallback UI. | custom lint rule † |
| FE-PERF-07 | User-facing error messages **MUST NOT** expose stack traces, internal identifiers, or raw backend payloads. | review |
| FE-PERF-08 | Automatic retries **MUST** be bounded, backed off, and limited to idempotent operations. | review |
| FE-PERF-09 | Unhandled exceptions **MUST** reach Sentry with a release version and uploaded source maps. | CI deploy step |
| FE-PERF-10 | Every request to a backend API **MUST** carry a correlation / request ID header. | review |
| FE-PERF-11 | Production code paths **MUST NOT** use `console.log`; logs and telemetry **MUST NOT** contain tokens, PII, or full request bodies. | `no-console` |

---

## 12. Code Quality & Tooling

→ Detail: `Standards/eslint-rule-contract.md` _(planned)_

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-QC-01 | CI linting **MUST** use `typescript-eslint`'s type-checked config plus the `react`, `react-hooks` and `jsx-a11y` plugins. | `eslint.config.js` † |
| FE-QC-02 | Prettier **MUST** be the sole formatter; ESLint stylistic rules stay off. | `format` CI job |
| FE-QC-03 | Every `eslint-disable` comment **MUST** state its specific reason inline. | `eslint-comments/require-description` † |
| FE-QC-04 | The lint job **MUST** run with `--max-warnings=0`. | CI † |
| FE-QC-05 | Every publishable package **MUST** run a dead-code (knip) and dependency-cycle check in CI. | CI (today: `ui` only) |
| FE-QC-06 | Build artifacts (`dist`, `.next`, `storybook-static`, `coverage`) **MUST** be excluded from lint, format and knip scope. | ignore files |

---

## 13. Dependencies

→ Detail: `Standards/dependency-approval-checklist.md` _(planned)_

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-DEP-01 | A new dependency **MUST** pass the approval checklist and get owner sign-off before merge. | PR template |
| FE-DEP-02 | CI **MUST** install from a committed, frozen lockfile. | `--frozen-lockfile` |
| FE-DEP-03 | A critical or high CVE **MUST** be patched or the dependency replaced within the defined SLA. | `pnpm audit` + Renovate † |
| FE-DEP-04 | Dependency install / build scripts **MUST** be explicitly allow-listed, never run unreviewed. | `onlyBuiltDependencies` |
| FE-DEP-05 | Direct dependency versions **MUST** be pinned or narrowly ranged — no wildcards or floating ranges. | review |

---

## 14. Workflow: Git, CI/CD & Environment

→ Detail: `Standards/ci-cd-pipeline.md`, `Standards/environment-config.md` _(planned)_

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-WF-01 | Branches **MUST** fork from `main`, be short-lived, and follow `<type>/<ticket>-<slug>`. | review |
| FE-WF-02 | Every PR **MUST** link its tracker issue and carry at least one CODEOWNERS approval. | branch protection † |
| FE-WF-03 | `main` **MUST** require squash merge, passing checks, and block direct and force pushes. | branch protection † |
| FE-WF-04 | Pre-commit **MUST** finish in ≤ 10 s and pre-push in ≤ 60 s; anything slower belongs in CI only. | convention |
| FE-WF-05 | `--no-verify` **MUST NOT** be used outside a documented, reviewer-acknowledged emergency. | process |
| FE-WF-06 | PR CI **MUST** pass frozen install, lint, format, typecheck, unit, interaction and visual tests, and build before merge. | `quality.yml` |
| FE-WF-07 | Real `.env*` files **MUST NOT** be committed; only `.env.example` is tracked. | `.gitignore` |
| FE-WF-08 | App startup **MUST** validate required environment variables against a schema and fail fast. | review † |
| FE-WF-09 | Production deploys **MUST** run one immutable artifact, gated on green CI, with a documented rollback. | pipeline † |

---

## Not yet decided

These have **no rule yet**. Do not assume a default; raise them before relying on one.

| Question | Working recommendation |
| --- | --- |
| Does the reads-via-proxy / actions-for-session split apply to every product, including those with no separate backend API? | Yes for products fronting a Core API; a Next-owns-the-data product needs its own answer |
| Which client store is sanctioned? | Zustand-class for cross-route state; Context for low-frequency injection only |
| Which form library is the standard? | React Hook Form + Zod; `useActionState` for simple server-action forms |
| Adopt React Compiler? | Not yet — enable its ESLint diagnostics now, compile later |
| Adopt `cacheComponents` / `use cache`? | New `[N16]` projects only; not a drop-in migration |
| Changed-code coverage threshold, and does it block merge? | 80% lines/branches, blocking |
| Initial per-route JS budget, and does a breach block merge? | Baseline first, warn for one quarter, then block |
| CVE response SLA | Critical 48 h · High 7 d · Medium 30 d |
| Strict CSP with nonces now? | Auth-gated routes first — nonces disable static generation and ISR |

---

## Governance

**Exceptions.** A rule may be broken deliberately. Record it in `Standards/exceptions.md` with:
rule ID → reason → trade-off accepted → owner → **expiry date**. No exception is permanent;
expired entries are closed, and the code is either fixed or the exception re-approved.

**Changing a rule.** Open a PR against this file stating which rule changes and why, plus the
Handbook update it needs. One week for feedback; owner plus one senior engineer approve.

**Rule IDs** are permanent. A retired rule is marked `[DEPRECATED]` and its ID is never reused,
so review comments citing it stay meaningful.

**Enforcement.** Rules marked `†` are aspirational until their tooling lands — tracked in the
adoption backlog, not silently ignored. A rule that stays unenforced and unenforceable for two
quarters is either demoted to the Handbook or deleted.
