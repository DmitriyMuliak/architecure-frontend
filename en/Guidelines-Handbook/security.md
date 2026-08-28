# Security — Guidelines

> **Handbook** level: explains the concepts and shows how to apply the decisions made in
> [Standards](../Standards/index.md). Rules at this level hold in code review, but the strategic
> contract lives there. Implementations live in [Reference-Recipes](../Reference-Recipes/).

## The trust boundary

Detail of `FE-SEC-02` and `FE-TS-04`.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-SEC-03 | Client Component props and Server Action return values **MUST** be explicit DTOs — the RSC payload is publicly readable. | review |
| FE-SEC-04 | All client-supplied data **MUST** be validated on the server before use. | review |
| FE-SEC-08 | State-changing Route Handlers **MUST** verify `Origin` against `Host` themselves — Server Actions get this automatically. | review |

## Untrusted content

Detail of `FE-SEC-05`.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-SEC-06 | User-supplied URLs used in `href`, navigation, or redirects **MUST** pass an allow-list that rejects the `javascript:` scheme. | review |
| FE-SEC-09 | Links with `target="_blank"` **MUST** carry `rel="noopener noreferrer"`, and `postMessage` handlers **MUST** check origin. | `eslint-plugin-react » react/jsx-no-target-blank` |

## Response headers

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-SEC-10 | Every app **MUST** send CSP, HSTS, `X-Content-Type-Options`, and `Referrer-Policy`; strict nonce-based CSP is deliberately deferred. | header smoke test † |
