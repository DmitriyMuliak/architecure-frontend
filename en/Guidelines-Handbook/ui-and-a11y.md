# UI, styling and accessibility — Guidelines

> **Handbook** level: explains the concepts and shows how to apply the decisions made in
> [Standards](../Standards/index.md). Rules at this level hold in code review, but the strategic
> contract lives there. Implementations live in [Reference-Recipes](../Reference-Recipes/).

## Styling

Detail of `FE-UI-01` and `FE-UI-02`.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-UI-02 | Colours and numeric values **MUST** come from semantic `@theme` tokens; a raw hex/px needs a documented exception. | `eslint-plugin-better-tailwindcss` † |
| FE-UI-03 | `@apply` **MUST NOT** be used; extract a `@utility` or a component instead. | CI grep † |
| FE-UI-04 | Conditional and state-driven classes **MUST** be composed with `cx()` / `sortCx()` and `data-*`, never by string concatenation. | review |
| FE-UI-05 | A Figma stroke excluded from auto-layout **MUST** be implemented as `inset-shadow` / `ring`, never as a CSS `border`. | review |
| FE-UI-10 | Inline `style` **MUST NOT** be used except for values computed at runtime. | review |

## Component API

Detail of `FE-UI-07`.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-UI-07 | Variants **MUST** be discrete typed props or one string union — not a config object and not a set of booleans. | review |
| FE-UI-09 | Every exported `packages/ui` component **MUST** ship co-located stories covering its default and all interactive states. | review |
| FE-UI-06 | Interactive primitives **MUST** be built on `react-aria-components`, not on bespoke keyboard/focus handling. | review |
| FE-UI-08 | Shared components **MUST** pass `className` through and expose state via `data-*`; consumers never target internals. | `eslint-plugin-boundaries » boundaries/dependencies` † + review |

## Accessibility

Detail of `FE-A11Y-01`. Each of these rules is a distinct WCAG 2.2 AA success criterion.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-A11Y-02 | Semantic HTML **MUST** come before ARIA; `aria-*` only where semantics are insufficient. | `eslint-plugin-jsx-a11y` † |
| FE-A11Y-03 | Every interactive element **MUST** be operable by keyboard alone with a visible focus indicator. | manual |
| FE-A11Y-04 | Every interactive element **MUST** expose a non-empty accessible name. | `addon-a11y` |
| FE-A11Y-05 | Form inputs and their errors **MUST** be programmatically associated with a label. | `eslint-plugin-jsx-a11y` † |
| FE-A11Y-06 | Dialogs **MUST** trap focus while open and restore it on close. | manual |
