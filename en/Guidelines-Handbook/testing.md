# Testing — Guidelines

> **Handbook** level: explains the concepts and shows how to apply the decisions made in
> [Standards](../Standards/index.md). Rules at this level hold in code review, but the strategic
> contract lives there. Implementations live in [Reference-Recipes](../Reference-Recipes/).

## Concepts

`FE-TEST-10` requires tests to reach components through reusable entities. Here is what that means.
Three distinct things that are often conflated:

**Testkit** — a factory for the test environment. It prepares everything a component or page needs
in order to run at all: providers, mocked transport, data seed, timers. It answers "what world does
this test live in". One testkit serves many tests; a test does not assemble providers itself.

**Driver** (page object / component driver) — a wrapper around one component or screen that
encapsulates **element lookup and the actions on them**. The test says
`await loginDriver.submit({ email, password })` rather than querying for inputs and buttons. When the
markup changes, you fix the driver, not twenty tests. Internally the driver uses accessible queries
(`FE-TEST-04`) — which is exactly why that rule lives at the driver level instead of being scattered
across tests.

**API harness** — control over the network boundary: which responses the backend returns, which
errors, which delays. It is how a test controls the *state of the world*, while the driver controls
the *interface*. The harness is where mocking lives (`FE-TEST-07`: mocks cover someone else's
boundary, not your own code).

Division of responsibility: **the testkit prepares, the harness sets state, the driver acts, the test
asserts.** If a `getByRole` shows up in the body of a test, that almost always means a driver is
missing.

## How a test is written

Detail of `FE-TEST-10` and the strategy in index.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-TEST-03 | Tests **MUST** assert user-visible behaviour, not internal state, props, or class names. | review |
| FE-TEST-04 | DOM queries **MUST** prefer role, label, or text; `data-testid` is a last resort. | `eslint-plugin-testing-library` † |
| FE-TEST-05 | Interactions **MUST** be simulated with `userEvent`, never with `fireEvent`. | `eslint-plugin-testing-library` † |
| FE-TEST-07 | Code owned by this codebase **MUST NOT** be mocked inside its own tests. | review |

## Determinism

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-TEST-08 | Tests **MUST NOT** depend on the real network, real time, or state shared across tests. | review |
| FE-TEST-09 | A flaky test **MUST** be quarantined and ticketed, never masked with retries. | review |

## Tool boundaries

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-TEST-06 | Async Server Components and Server Actions **MUST** be verified with Playwright, not with RTL rendering. | review |
