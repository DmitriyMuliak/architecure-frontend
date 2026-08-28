# Frontend Standards

> **What this is.** The strategic contract for Future Media frontend: technologies, architectural
> boundaries, and the rules whose violation costs an incident. Readable in 5 minutes, cited in code
> review by ID.
>
> **What is not here.** How to write good code. That is the **[Guidelines-Handbook](../Guidelines-Handbook/)**
> level: it explains the concepts and shows how to apply the decisions made here. Concrete
> implementations live in **[Reference-Recipes](../Reference-Recipes/)**.
>
> A rule reaches this page only if it fixes a **technology**, sets an **architectural boundary**, or
> prevents an **incident** that nothing else catches. A consequence of another rule is not a rule.

`v1.2 (draft)` · Owner: _TBD_ · Updated: 2026-08-24

---

## How to read this document

| Keyword | What it means at code-review time |
| --- | --- |
| **MUST** / **MUST NOT** | Blocker. The PR does not merge until it is resolved or an exception is recorded. |
| **SHOULD** / **SHOULD NOT** | Default position. Deviation is allowed but must be stated and accepted by the reviewer. |
| **MAY** | Explicitly permitted. Not a recommendation. |

- `[N16]` / `[N15]` — applies only to that Next.js major. `[R19]` — React 19 only.
- `†` — the tool is **not installed yet**; until it lands, the rule rests on review alone.
- In the "Enforced by" column the format is `package » rule`: what you install on the left, what you turn on the right.
- Anything automatable **MUST NOT** be argued manually in review. Fix the tool, not the comment.
- Rule IDs are permanent — including when a rule moves between levels.

**Scope.** Applies to all new frontend code and to any file a PR touches. Existing code is not
retro-fixed on sight. Exceptions: see [Governance](#governance).

---

## 1. Technology stack

The table below is **normative**. Every row is a **MUST**-level rule with its own ID: the chosen
library is mandatory, and the "Constraints" column is mandatory just the same. A row is cited as
`FE-LIB-NEXT`. New projects start on the latest stable majors; existing projects pin a major and
upgrade deliberately.

| ID | Concern | Library | Constraints |
| --- | --- | --- | --- |
| FE-LIB-REACT | UI framework | React | 18 → 19; new projects on 19 |
| FE-LIB-NEXT | Meta-framework | Next.js | **App Router only**; Pages Router is banned |
| FE-LIB-TS | Language | TypeScript | `strict`; `[N16]` requires ≥ 5.1 |
| FE-LIB-CSS | Styling | Tailwind CSS v4 | CSS-first `@theme`, no `tailwind.config.js` |
| FE-LIB-HEADLESS | Component behaviour | react-aria-components | the only sanctioned headless layer |
| FE-LIB-SERVER-STATE | Server state on the client | TanStack Query | |
| FE-LIB-CLIENT-STATE | Client state | Zustand-class store | |
| FE-LIB-FORMS | Forms | React Hook Form | |
| FE-LIB-VALIDATION | Runtime validation | Zod | new projects use `zod/mini` |
| FE-LIB-I18N | Internationalization | next-intl | |
| FE-LIB-UNIT | Unit / component tests | Vitest + React Testing Library | |
| FE-LIB-STORYBOOK | UI documentation and interaction tests | Storybook | + `addon-vitest` |
| FE-LIB-E2E | Visual regression and E2E | Playwright | |
| FE-LIB-LINT | Lint and format | ESLint (flat config) + Prettier | `react-hooks` preset — `recommended-latest` |
| FE-LIB-OBSERVABILITY | Errors and RUM | Sentry | |
| FE-LIB-BOUNDARIES | Module and layer boundaries | eslint-plugin-boundaries | rule `boundaries/dependencies` |
| FE-LIB-CYCLES | Circular dependencies | dependency-cruiser | |
| FE-LIB-BUILD | Package manager / build | pnpm + Turborepo | `packageManager` and Node version pinned per repo |

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-STACK-01 | A concern already covered by a row in the table above **MUST NOT** be solved with a second library. | review |

---

## 2. Architecture

→ [Handbook: architecture](../Guidelines-Handbook/architecture.md) ·
[project-structure-fsd.md](./project-structure-fsd.md) _(planned)_

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-ARCH-08 | New projects **MUST** use an FSD-like directory structure with unidirectional dependencies between layers. | `eslint-plugin-boundaries » boundaries/dependencies` † |

---

## 3. Data

The criterion for choosing a transport is **not** "read or mutation" — it is **whether there is a
side effect on the session or on Next's cache**.

→ [Handbook: state and data](../Guidelines-Handbook/react-and-state.md) ·
[Handbook: Next.js](../Guidelines-Handbook/nextjs.md)

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-DATA-07 | Reads from a Client Component **MUST** go through the same-origin Route Handler proxy with TanStack Query, never through a Server Action. | review |
| FE-DATA-08 | A Server Action **MUST** be used only when the mutation touches the session, Next's cache, or requires a server-side redirect. | review |
| FE-DATA-10 | Server state on the client **MUST** be owned by TanStack Query — no hand-rolled refetch-on-focus, polling, or dedup. | review |

---

## 4. Types and validation

→ [Handbook: TypeScript](../Guidelines-Handbook/typescript.md)

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-TS-01 | Every package **MUST** enable `strict`, `verbatimModuleSyntax`, `isolatedModules`, and `noImplicitOverride`. | `tsconfig.base.json` |
| FE-TS-04 | Data crossing a trust boundary (API, form, URL, env) **MUST** be validated at runtime on the server with a Zod schema. | review |

---

## 5. UI and accessibility

→ [Handbook: UI and a11y](../Guidelines-Handbook/ui-and-a11y.md) ·
[design-tokens.md](./design-tokens.md) _(planned)_

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-UI-01 | Tailwind utilities **MUST** style all UI; plain CSS is reserved for `@theme`, `@utility`, and vendor overrides. | review |
| FE-A11Y-01 | All UI shipped to production **MUST** conform to WCAG 2.2 Level AA. | `addon-a11y` + manual |

---

## 6. Testing

→ [Handbook: testing](../Guidelines-Handbook/testing.md)

| Test type | Covers | **MUST NOT** cover | Owner |
| --- | --- | --- | --- |
| Unit (Vitest, jsdom) | logic, hooks, utils, transforms | rendering, integration | author |
| Storybook interaction (Vitest browser) | component rendering, interaction flows | navigation, real network, logic already unit-tested | author |
| Visual regression (Playwright) | pixel regressions of stories | behaviour, a11y semantics | author; design-system owner approves baselines |
| E2E (Playwright) | critical cross-page journeys | component variants, pixel diffs, edge cases | QA team |

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-TEST-01 | New or changed behaviour **MUST** ship with a passing test before merge. | CI |
| FE-TEST-02 | Coverage of **changed** code **MUST** be ≥ 75% (target 80%) and blocks merge; a global repository percentage is not used. | vitest thresholds † |
| FE-TEST-10 | Access to components in tests **MUST** go through reusable entities that encapsulate lookup and control, not through direct DOM queries in every test. | review |

---

## 7. Security

→ [Handbook: security](../Guidelines-Handbook/security.md) ·
[csp-contract.md](./csp-contract.md) _(planned)_

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-SEC-01 | Auth tokens and session state **MUST** live only in `httpOnly` + `Secure` + `SameSite` cookies, never in Web Storage. | review |
| FE-SEC-02 | Every Server Action and Route Handler **MUST** verify authorization itself; middleware / proxy is never a sufficient gate. | review (see CVE-2025-29927) |
| FE-SEC-05 | HTML passed to `dangerouslySetInnerHTML` **MUST** be sanitized first. | `eslint-plugin-react » react/no-danger` |
| FE-SEC-07 | Secrets and PII **MUST NOT** appear in `NEXT_PUBLIC_` variables, client bundles, or logs. | secret scanning † |

---

## 8. Performance and observability

→ [Handbook: performance and reliability](../Guidelines-Handbook/performance-and-reliability.md)

| Metric | Target (p75, field) | Blocks merge |
| --- | --- | --- |
| LCP / INP / CLS | ≤ 2.5 s / ≤ 200 ms / ≤ 0.1 | no — RUM, post-deploy |
| TTFB | ≤ 800 ms | synthetic only |
| Initial client JS per route | set per project | yes |

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-PERF-01 | Production routes **MUST** meet the Core Web Vitals targets above at p75 in RUM. | Sentry RUM |
| FE-PERF-02 | Every project **MUST** define its own per-route JS budgets; a breach blocks merge, and raising a limit is a separate deliberate commit. | `size-limit` † |
| FE-PERF-09 | Unhandled exceptions **MUST** reach Sentry with a release version and uploaded source maps. | CI deploy step |

---

## 9. Dependencies

→ [Handbook: tooling and workflow](../Guidelines-Handbook/tooling-and-workflow.md) ·
[dependency-approval-checklist.md](./dependency-approval-checklist.md) _(planned)_

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-DEP-01 | A new dependency **MUST** pass the approval checklist and get owner sign-off before merge. | PR template |
| FE-DEP-03 | A CVE **MUST** be closed within SLA: critical — 48 hours, high — 7 days, medium — 30 days. | `pnpm audit` + Renovate † |

---

## 10. Process

→ [Handbook: tooling and workflow](../Guidelines-Handbook/tooling-and-workflow.md)

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-WF-02 | Every PR **MUST** link its tracker issue and carry at least one CODEOWNERS approval. | branch protection † |
| FE-WF-03 | `main` **MUST** require squash merge and green checks, and block direct and force pushes. | branch protection † |

---

## Not yet decided

There are no open questions right now. When a question arises that the standard does not answer, it
lands here **before** anyone starts relying on their own default.

---

## Governance

**Three levels.** `Standards/` — what has been decided (this page). `Guidelines-Handbook/` — what it
means and how to apply it. `Reference-Recipes/` — what it looks like in code. A statement that needs
an example to be understood does not belong here by definition.

**Membership test.** A rule stays here only if the answer to "what breaks if we remove it?" is a
different technology, a broken architectural boundary, or an incident. If the answer is "the code
gets worse", it is Handbook. If the rule is a consequence of another rule, it is Handbook.

**Exceptions.** A rule may be broken deliberately. Record it in `Standards/exceptions.md`:
rule ID → reason → trade-off accepted → owner → **expiry date**. No exception is permanent; expired
entries are closed, and the code is either fixed or the exception re-approved.

**Changing a rule.** Open a PR against this file stating which rule changes and why, plus the
Handbook update it needs. One week for feedback; the owner plus one senior engineer approve.

**Two ID namespaces.** `FE-LIB-*` — rows of the normative stack table: **which** tool is chosen.
`FE-<DOMAIN>-NN` — everything else: **how** to apply it. A technology choice changes only through
`FE-LIB-*`; a rule that merely restates a table row is not added to Standards.

**Rule IDs** are permanent — including when a rule moves between levels. A retired rule is marked
`[DEPRECATED]` and its ID is never reused, so review comments citing it stay meaningful.

**Enforcement.** Rules marked `†` are declarative until their tooling lands — they are in the
adoption backlog, not silently ignored. A rule that stays unenforced and unenforceable for two
quarters is either demoted to the Handbook or deleted.
