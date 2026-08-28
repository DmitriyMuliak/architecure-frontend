# proceeed ARCHITECTURE = claude --resume 40018f32-1a34-4410-8264-d742d600c5ce

# proceeed TASK = claude --resume 80579659-a3a2-43c0-ad0b-feb4ccafeb61

# Request

мені потрібно розробити стандарти для ведення фронтенду у компанії. Наш стек - nodejs, typescript, nextJS v15-16, tailwind v4, vitest, maybe tanstack query, react-testing-library, playwright, react v18-19, storybook. Далі я приведу початковий приклад колеги але вважаю що він і не. повний і не точний. Тимаєш подумати і сказати які взагалі мають існувати розділи і дуже коротко пару речень що там має бути. Потім поговоримо більш детально. Можливо треба щось доповнити або додати. Також враховуй best practicies. Ось його приклад колеги - Frontend Standardization
[image](https://futuremedia.atlassian.net/wiki/aa-avatar/712020:a0435eb1-98f5-47ab-a886-7be8c579f200)

[thumbs up](https://pf-reactions-service.prod-east.frontend.public.atl-paas.net/assets/standard/ef8b0642-7523-4e13-9fd3-01b65648acf6/128x128/1f44d.png)

1

[App Icon](https://icon.cdn.prod.atlassian-dev.net/76c7a7ed-0fb6-49c4-9733-34d92ae4c42d/7c1b2c3b-70b5-498a-acfe-9b7e28d4495b/93874fe5-5f72-48a5-8027-06dd64d60ca3/custom-ui-resource/assets/imgs/documents_icon_toolbox_cloud.svg)Scroll page details

- [Stack](https://futuremedia.atlassian.net/wiki/spaces/FC/pages/1804959754/Frontend+Standardization#Stack)
  - [Core libraries](https://futuremedia.atlassian.net/wiki/spaces/FC/pages/1804959754/Frontend+Standardization#Core-libraries)
- [Code quality](https://futuremedia.atlassian.net/wiki/spaces/FC/pages/1804959754/Frontend+Standardization#Code-quality)
  - [TypeScript](https://futuremedia.atlassian.net/wiki/spaces/FC/pages/1804959754/Frontend+Standardization#TypeScript)
  - [Linting](https://futuremedia.atlassian.net/wiki/spaces/FC/pages/1804959754/Frontend+Standardization#Linting)
  - [Git hooks](https://futuremedia.atlassian.net/wiki/spaces/FC/pages/1804959754/Frontend+Standardization#Git-hooks)
- [Security](https://futuremedia.atlassian.net/wiki/spaces/FC/pages/1804959754/Frontend+Standardization#Security)
- [Testing](https://futuremedia.atlassian.net/wiki/spaces/FC/pages/1804959754/Frontend+Standardization#Testing)

Standardization is continuous and requires an incremental approach. We must begin with foundational and critical aspects. This document serves as the primary location for discussing and deciding standards. Sections on additional aspects will be added and brought forward for discussion as needed.

---

# Stack

svg

New projects start on the latest stable versions. Existing projects remain pinned to the latest major version and upgrade intentionally. Dependencies are upgraded by priority, with core libraries highest. An old React version can ripple through other dependencies and block their upgrades.

### Core libraries

svg

Core libraries are non-negotiable. All products must use them without exception. They form the foundation that everything else sits on and must remain consistent across all products.

|     |
| --- |

Concern

svg

Library

svg

Comments

svg

|     |
| :-- |

**UI framework**

|     |
| :-- |

React

|     |
| :-- |

|     |
| :-- |

**Meta-framework / routing**

|     |
| :-- |

Next

|     |
| :-- |

App Router only; the Pages Router has a fundamentally different architecture and doesn’t fully support modern React and Next features.

|     |
| :-- |

**Styling**

|     |
| :-- |

Tailwind CSS

|     |
| :-- |

|     |
| :-- |

**Internationalization**

|     |
| :-- |

next-intl

|     |
| :-- |

|     |
| :-- |

**Testing framework**

|     |
| :-- |

Vitest

|     |
| :-- |

Vitest used for both unit tests and Storybook interaction tests

|     |
| :-- |

**UI documentation & testing**

|     |
| :-- |

Storybook + React Testing Library (RTL)

|     |
| :-- |

Storybook includes a testing wrapper that internally uses Vite and RTL. These libraries should not be used directly in stories.

---

# Code quality

svg

## TypeScript

svg

TypeScript is configured with strict mode. Rules are disabled in comments only for strong reasons and include an explanatory comment. Types are strict; avoid loose constructs such as `Object` when a clear shape exists. Use `any` and `unknown` only for truly unknown errors or to avoid excessive type gymnastics, and document these rare cases with a justification comment.

## Linting

svg

Linting helps pick up issues in a deterministic way and prevents developers from focusing on trivial aspects during code review. The following Eslint plugins are used to catch common issues before PRs are merged:

- `typescript-eslint` — checks for bug patterns type-checker doesn't catch
- `eslint-plugin-react` — useful React rules
- `eslint-plugin-react-hooks` — enforces React’s hook rules and surfaces React compiler diagnostics
- `eslint-plugin-jsx-a11y` — checks JSX for accessibility issues

## Git hooks

svg

Pre-commit and pre-push hooks give quick feedback for issues before a PR is opened. Pre-commit lints and formats staged files. Pre-push runs type-checker on full project and builds the Next app. Pre-push is a good place to include other optional quality checks agreed internally by the team (run tests, check for dependency cycles and dead code). Scripts that take significant time to run should not be included in Git hooks. If they have a long feedback loop, team members tend to commit/push with `--no-verify` flag and catch issues only in PR checks.

---

# Security

svg

- Authorization tokens and any sensitive session state is stored in http-only cookies and is never accessible to client code. Sensitive data is kept on server-side through Server Components, Server Functions and Route Handlers.
- Any markup rendered with `dangerouslySetInnerHTML` must be sanitized

---

# Testing

svg

Testing is covered with 4 types of tests:

- **Unit tests** for logic and hooks
- **Storybook interaction tests** for UI components. They allow writing familiar Vitest + React Testing Library tests but run inside a full browser with visual feedback inside Storybook or inside a headless browser in CI.
- **Visual regression tests** for UI components. They are configured with Playwright to automatically run against Storybook stories. Check Frontend SDK for setup reference: [playwright.visual.config.ts](https://github.com/QR-CODE-App/futuremedia-fe-sdk/blob/main/packages/ui/playwright.visual.config.ts)[**svg**](https://github.com/QR-CODE-App/futuremedia-fe-sdk/blob/main/packages/ui/playwright.visual.config.ts)

  [**Explain**](https://github.com/QR-CODE-App/futuremedia-fe-sdk/blob/main/packages/ui/playwright.visual.config.ts)

  . These tests are expensive (and slow) and must be used carefully and decided whether they can be skipped as a tradeoff between performance and priority.

- **End-to-End tests** test critical flows and are owned by the QA team.

Regardless of type, all tests follow these general rules:

- Tests use accessible queries (`getByRole`, `getByLabelText`, etc.) and only resort to `data-testid` as an exception when using accessible queries don’t make sense or require workarounds.
- Prefer `userEvent` over `fireEvent` to stay closer to real user interactions
- Test cases cover real usage, otherwise they just add noise and more code to maintain
- Tests don’t make assumptions about implementation details and only care about behavior
- Tests files are kept simple and minimalistic without heavy setup logic

# Answer 1

Так. Я б сказав, що приклад колеги — це **хороший стартовий skeleton**, але він змішує різні рівні абстракції: stack, coding conventions, security і testing. При цьому відсутні досить важливі речі: architecture, data fetching, Next.js-specific правила, component design, accessibility, performance, Git/PR workflow, observability тощо.

Я б будував стандарт не як «список бібліотек», а як **Engineering Standards / Frontend Handbook**, де для кожного правила є:

> **Rule → Why → When → Example → Exceptions**

І дуже важливо розділити **mandatory standards** (`MUST`) від **recommendations** (`SHOULD`) — інакше документ перетвориться на релігію з ESLint як верховним жерцем.

## Які розділи я б заклав

### 1. Principles & Scope

Що саме стандартизуємо, на які проєкти поширюється документ, і головні engineering principles.

Наприклад: simplicity, maintainability, accessibility, performance, security, type safety, progressive enhancement, explicit trade-offs.

---

### 2. Technology Stack & Dependency Management

Фіксуємо canonical stack:

- Node.js
- TypeScript
- React
- Next.js
- Tailwind
- Vitest
- RTL
- Playwright
- Storybook
- TanStack Query — якщо приймаємо його як standard

Також правила версій, package manager, dependency updates, коли можна додавати нову бібліотеку, хто/як її approve.

**Особливо:** не просто «latest stable», а policy щодо major upgrades і compatibility matrix.

---

### 3. Project Structure & Architecture

Оце я б додав **обов'язково** — у колеги цього фактично немає.

Правила щодо:

- folder structure;
- feature/domain organization;
- dependency direction;
- component boundaries;
- `app/` у Next.js;
- shared vs feature-specific code;
- server/client boundaries;
- circular dependencies;
- architecture layers.

Тут треба чітко визначити, **де який код має жити і хто від кого може залежати**.

---

### 4. Next.js Architecture

Окремий розділ, бо Next.js у вашому випадку — це не просто router.

Наприклад:

- App Router only;
- Server Components vs Client Components;
- коли використовувати `"use client"`;
- Server Actions;
- Route Handlers;
- Middleware;
- caching/revalidation;
- `fetch`;
- loading/error/not-found boundaries;
- metadata;
- streaming;
- dynamic/static rendering;
- cookies/headers;
- redirects;
- server-only/client-only modules.

Це один із найважливіших розділів.

---

### 5. TypeScript Standards

Не тільки `strict: true`.

Я б описав:

- `strict`;
- `any` policy;
- `unknown` policy;
- type vs interface;
- discriminated unions;
- generics;
- type inference;
- enums;
- utility types;
- type assertions;
- runtime validation;
- API types;
- `null` / `undefined`;
- error typing.

І головне правило:

> **TypeScript types do not validate runtime data.**

Для API/user input треба окремо визначити runtime validation strategy.

---

### 6. React Standards

Правила React-specific development:

- component design;
- hooks;
- composition;
- controlled/uncontrolled components;
- state placement;
- Context;
- memoization;
- `useMemo` / `useCallback`;
- refs;
- effects;
- derived state;
- React 18/19 conventions;
- React Compiler, якщо вирішите його використовувати.

Особливо варто сформулювати правило:

> Don't optimize renders by default; measure first.

---

### 7. State Management & Data Fetching

Я б **не змішував це просто з React**.

Окремо визначити:

- local state → `useState`;
- shared UI state → Context / Zustand;
- server state → TanStack Query;
- URL state → search params;
- form state → React Hook Form або інший standard;
- derived state → derive, don't duplicate;
- persistence → окремий policy.

І дуже важливо визначити **що є server state, а що client state**.

---

### 8. API & Data Layer

Окремий architectural layer.

Правила:

- API client;
- DTO;
- runtime validation;
- error handling;
- request cancellation;
- retries;
- authentication;
- authorization;
- pagination;
- optimistic updates;
- API contracts;
- serialization.

Якщо Node.js backend теж у межах стандартів — тут можна описати frontend ↔ backend contract.

---

### 9. UI / Component Architecture

Тут уже design system/component philosophy:

- primitives;
- reusable components;
- composition;
- presentational vs feature components;
- headless components;
- component API;
- controlled components;
- accessibility;
- variants;
- slots/composition;
- avoiding "god components".

Також правила, коли компонент повинен потрапляти в shared UI library, а коли залишатися feature-local.

---

### 10. Styling & Tailwind

Не просто «use Tailwind».

Наприклад:

- Tailwind v4;
- design tokens;
- CSS variables;
- responsive strategy;
- spacing;
- typography;
- colors;
- arbitrary values;
- `@apply`;
- class composition;
- `cn` / `tailwind-merge`;
- state variants;
- dark mode;
- animations;
- when plain CSS is acceptable.

І окремо можна зафіксувати рішення щодо **border vs box-shadow**, про яке ми недавно говорили.

---

### 11. Accessibility (a11y)

Я б зробив окремий first-class розділ.

- semantic HTML;
- keyboard navigation;
- focus management;
- ARIA;
- accessible names;
- forms;
- dialogs;
- errors;
- contrast;
- screen readers;
- automated accessibility testing.

`eslint-plugin-jsx-a11y` — це лише частина story.

---

### 12. Forms & Validation

Якщо у вас багато product UI — дуже корисний окремий стандарт.

- form library;
- validation;
- schema validation;
- client/server validation;
- error presentation;
- async validation;
- controlled/uncontrolled;
- submission states;
- accessibility.

---

### 13. Testing Strategy

Тут колега почав правильно, але я б зробив значно більш системно.

Описати:

**Unit / integration**
→ Vitest + RTL

**Component / interaction**
→ Storybook

**Visual regression**
→ Playwright

**E2E**
→ Playwright

І головне — **testing pyramid / test boundaries**:

> Що тестуємо на кожному рівні і що НЕ тестуємо.

Наприклад, не треба 15 unit tests на кнопку, якщо один interaction test прекрасно перевіряє її поведінку.

---

### 14. Storybook Standards

Окремо від testing.

Storybook має бути не просто «місцем для скріншотів компонентів», а:

- component documentation;
- states;
- interaction tests;
- accessibility;
- visual regression;
- design review;
- edge cases.

Також правила щодо `args`, decorators, loaders, MSW, naming, story organization.

---

### 15. E2E & Browser Testing

Окремий розділ для Playwright:

- critical user journeys;
- test isolation;
- fixtures;
- auth;
- test data;
- parallelism;
- retries;
- flaky tests;
- screenshots/traces;
- CI execution.

І чітко розмежувати **component tests vs E2E**.

---

### 16. Security

Колега тут занадто поверхнево.

Я б включив:

- authentication;
- authorization;
- cookies;
- CSRF;
- XSS;
- CSP;
- secrets;
- environment variables;
- sensitive data;
- `dangerouslySetInnerHTML`;
- user-generated content;
- URL validation;
- open redirects;
- dependency vulnerabilities;
- server/client data boundaries.

---

### 17. Performance

Цього дуже бракує.

Наприклад:

- Core Web Vitals;
- bundle size;
- code splitting;
- dynamic imports;
- image optimization;
- fonts;
- caching;
- server rendering;
- client JS minimization;
- hydration;
- unnecessary re-renders;
- performance budgets.

І правила типу:

> Don't add `useMemo`/`useCallback` purely as a preventive optimization.

---

### 18. Error Handling & Resilience

Окремий стандарт.

- expected vs unexpected errors;
- Error Boundaries;
- Next.js `error.tsx`;
- API errors;
- user-facing errors;
- retry strategy;
- fallback UI;
- logging;
- graceful degradation.

---

### 19. Observability

Часто про це забувають, а для production це критично.

- Sentry;
- logging;
- metrics;
- Web Vitals/RUM;
- error tracking;
- correlation/request IDs;
- monitoring;
- alerting.

І що **не можна логувати**.

---

### 20. Code Quality & Tooling

Тут вже можна перенести частину колеги:

- ESLint;
- Prettier;
- TypeScript;
- import sorting;
- dead code;
- dependency cycles;
- formatting;
- naming conventions;
- complexity;
- generated code.

---

### 21. Git & Pull Requests

Окремо від git hooks.

- branch strategy;
- commit conventions;
- PR size;
- PR description;
- reviewers;
- CODEOWNERS;
- merge strategy;
- squash/rebase;
- required checks;
- dependency updates.

---

### 22. CI/CD

Теж окремий розділ:

```text
lint
typecheck
unit/integration tests
storybook tests
build
E2E
visual regression
security/dependency checks
```

Плюс правила щодо deployment, preview environments, rollback.

---

### 23. Environment & Configuration

Важливо для Next.js.

- `.env`;
- public vs server environment variables;
- secrets;
- build-time vs runtime config;
- development/staging/production;
- feature flags;
- configuration validation.

---

### 24. Documentation & ADRs

Як приймаються architectural decisions.

Наприклад:

- README;
- architecture docs;
- ADR;
- component docs;
- API docs;
- comments;
- TODO policy.

Дуже корисно мати правило:

> Comments explain **why**, code explains **what**.

---

### 25. Code Review Standards

Що reviewer повинен перевіряти.

Наприклад:

- correctness;
- architecture;
- security;
- accessibility;
- tests;
- performance;
- maintainability.

І що **не повинно обговорюватися в review вручну**, якщо це може перевірити automation.

---

### 26. Dependency / Library Governance

Я б навіть виніс це окремо, якщо компанія достатньо велика.

Перед додаванням бібліотеки:

```text
Do we actually need it?
Can existing stack solve it?
Bundle size?
Maintenance?
Security?
License?
TypeScript support?
SSR/RSC compatibility?
React 19 compatibility?
Next.js compatibility?
```

Це дуже добре стримує ситуацію `npm install another-state-library-because-I-saw-it-on-Reddit`.

---

### 27. Migration & Legacy Policy

Оскільки у вас будуть Next 15/16, React 18/19 і старі проєкти, це реально потрібно.

Правила:

- legacy code;
- migration strategy;
- deprecation;
- major upgrades;
- codemods;
- strangler approach;
- technical debt;
- exceptions.

---

### 28. Standards Enforcement & Exceptions

І останнє — **як ці стандарти реально працюють**.

Наприклад:

```text
MUST      — mandatory
SHOULD    — recommended
MAY       — optional
```

І process для exception:

```text
Rule
↓
Why exception is needed
↓
Trade-off
↓
Owner
↓
Expiration/review date
```

Це дуже важливо. Інакше через пів року буде 47 правил, з яких 31 порушуються.

---

# Я б організував усе у 7 великих блоків

Щоб сам документ не перетворився на енциклопедію:

```text
Frontend Engineering Standards
│
├── 1. Foundations
│   ├── Principles & Scope
│   ├── Stack & Dependencies
│   ├── TypeScript
│   └── Code Quality
│
├── 2. Architecture
│   ├── Project Structure
│   ├── Next.js Architecture
│   ├── React Architecture
│   ├── State Management
│   └── API / Data Layer
│
├── 3. UI Engineering
│   ├── Component Architecture
│   ├── Styling / Tailwind
│   ├── Design System
│   ├── Forms
│   └── Accessibility
│
├── 4. Quality
│   ├── Testing Strategy
│   ├── Unit / Integration
│   ├── Storybook
│   ├── E2E / Playwright
│   └── Visual Regression
│
├── 5. Production Engineering
│   ├── Performance
│   ├── Security
│   ├── Error Handling
│   └── Observability
│
├── 6. Developer Workflow
│   ├── Git
│   ├── Code Review
│   ├── CI/CD
│   ├── Environment / Configuration
│   └── Documentation / ADR
│
└── 7. Governance
    ├── Dependency Governance
    ├── Legacy / Migration
    ├── Exceptions
    └── Standards Ownership
```

### І ще одна важлива думка

Я б **не намагався одразу прописати всі best practices як обов'язкові правила**.

Спочатку треба визначити приблизно 20–30 **architectural decisions**, які компанія реально хоче стандартизувати. Наприклад:

- App Router only;
- Server Components by default;
- де дозволений Zustand;
- чи є TanStack Query mandatory;
- де можна використовувати Context;
- як робимо API validation;
- яка testing strategy;
- Storybook role;
- Tailwind conventions;
- accessibility baseline;
- error handling;
- dependency policy.

А вже після цього деталізувати кожен пункт.

Бо є принципова різниця між:

> **"Ось 150 best practices для React"**

і

> **"Ось як саме наша компанія розробляє frontend."**

Другий варіант і є справжньою стандартизацією. І я б саме в цей бік рухав документ колеги.

# Quetion 1

але ти описав багато пунктів і додаткових підпунктів - виглядає як hadbook + examples а не фронтенд стаедарті. можливо вапто різдіти ці поняття ?

# Answer 2

Так. І я б **однозначно розділив**. Твоя інтуїція правильна: попередня структура вже починає перетворюватися на **Frontend Handbook**, а не на **Frontend Standards**.

Ключова різниця:

### Frontend Standards

Це **короткий набір обов'язкових engineering decisions**.

Відповідає на питання:

> **"Як ми повинні робити X у компанії?"**

Наприклад:

- MUST use TypeScript strict mode.
- MUST use Next.js App Router.
- Server Components are the default.
- MUST NOT access database directly from Client Components.
- TanStack Query is used for server-state management.
- MUST use accessible queries in tests.
- E2E tests cover critical user journeys.
- Tailwind is the default styling solution.

Тобто це фактично **contract / policy**.

---

### Frontend Handbook / Guidelines

А це вже:

> **"Як правильно застосовувати наші стандарти і які best practices ми рекомендуємо?"**

Тут уже можна мати:

- приклади;
- `useMemo` vs `useCallback`;
- як організовувати feature folders;
- як писати хороший Story;
- як mock API;
- як будувати forms;
- як використовувати Server Actions;
- patterns;
- anti-patterns;
- troubleshooting;
- migration examples.

Це набагато більший документ.

---

## Я б зробив 3 рівні

Не два, а навіть **три**:

```text
Frontend Engineering
│
├── Standards
│   └── WHAT / MUST
│
├── Guidelines / Handbook
│   └── HOW / SHOULD
│
└── Reference
    └── EXAMPLES / RECIPES / IMPLEMENTATION
```

### 1. Standards

**Короткий, authoritative документ.**

Наприклад:

```text
Frontend Standards

1. Technology
2. Architecture
3. TypeScript
4. React / Next.js
5. Styling
6. State & Data
7. Testing
8. Accessibility
9. Security
10. Performance
11. Code Quality
12. CI/CD
13. Dependencies
14. Exceptions
```

І в кожному максимум кілька конкретних правил.

Наприклад:

> **Server Components**
> Server Components are the default. `"use client"` MUST only be introduced when client-side capabilities are required.

Все. Без 3 сторінок пояснення.

---

### 2. Handbook / Guidelines

Тут уже:

> **Server vs Client Components**

і 5–10 сторінок:

```text
When to use Server Components
When to use Client Components
Common mistakes
Data fetching
Composition patterns
Examples
Anti-patterns
Performance implications
Testing
```

Посилання з Standard:

> See [Server & Client Components Guidelines].

---

### 3. Reference / Recipes

А це вже майже developer documentation:

```text
How to implement authentication
How to create a new feature
How to write a Storybook story
How to mock a Server Action
How to write Playwright fixtures
How to add a new API endpoint
How to configure TanStack Query
```

Можна навіть мати template/code snippets.

---

# І тоді твій початковий документ колеги я б суттєво скоротив

Його:

```text
Frontend Standardization

Stack
 └── Core libraries

Code quality
 ├── TypeScript
 ├── Linting
 └── Git hooks

Security

Testing
```

я б перетворив приблизно на:

```text
# Frontend Standards

## 1. Technology Stack
Canonical technologies and supported versions.

## 2. Architecture
Application architecture, project structure and dependency boundaries.

## 3. TypeScript
Type-safety requirements and TypeScript configuration.

## 4. React & Next.js
React and Next.js architectural rules and server/client boundaries.

## 5. State & Data Management
Rules for local state, server state, URL state and data fetching.

## 6. UI & Styling
Component architecture, Tailwind and design-system requirements.

## 7. Accessibility
Minimum accessibility requirements for all user-facing UI.

## 8. Testing
Required testing strategy and responsibilities of unit, component, visual and E2E tests.

## 9. Security
Frontend security requirements and sensitive-data handling.

## 10. Performance
Minimum performance requirements and performance budgets.

## 11. Code Quality
Linting, formatting, type checking and static analysis.

## 12. Git & CI/CD
Required automated checks and development workflow requirements.

## 13. Dependencies
Rules for introducing, upgrading and removing dependencies.

## 14. Exceptions
How and when standards can be intentionally violated.
```

І все.

Це вже виглядає як **стандарт**, а не як книжка.

---

### Ще важливіше: Standards ≠ Best Practices

Я б навіть не називав Handbook просто `Best Practices`.

Бо **best practice не обов'язково повинна бути company standard**.

Наприклад:

> `useCallback` може бути корисним для стабілізації reference.

Це best practice / guideline.

А:

> **We do not use `useCallback` for performance optimization without an identified rendering problem or measurement.**

— це вже **company standard**.

Тобто:

**Best Practice → рекомендація з екосистеми.**

**Guideline → наша рекомендована практика.**

**Standard → правило, якого команда повинна дотримуватися.**

Це дуже корисне розділення, особливо якщо ти хочеш зробити документ, який реально буде використовуватися при code review.

І я б ще додав до Standard для кожного правила невеликий маркер:

`MUST` / `MUST NOT` / `SHOULD` / `MAY`.

Тоді reviewer не сперечається, чи це «ну типу хороша практика», а бачить: **це company standard або ні**.
