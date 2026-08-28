# Next.js — Guidelines

> **Handbook** level: explains the concepts and shows how to apply the decisions made in
> [Standards](../Standards/index.md). Rules at this level hold in code review, but the strategic
> contract lives there. Implementations live in [Reference-Recipes](../Reference-Recipes/).

## The server boundary

The mechanism that carries out `FE-SEC-07` (secrets never reach the client bundle) and `FE-NEXT-13`
(`'use server'` only in `*.actions.ts`). `server-only` throws **at build time**, so it is the only
boundary here that does not rest on a reviewer paying attention.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-NEXT-02 | Components **MUST** default to Server Components; `"use client"` is added only at interactive leaves. | review |
| FE-NEXT-03 | A module holding secrets or server SDKs **MUST** import `server-only`. | `server-only` (build error) |
| FE-NEXT-13 | `'use server'` **MUST** appear only in dedicated `*.actions.ts` files — the set of RPC endpoints has to be enumerable. | `eslint » no-restricted-syntax` † |

## Versions and runtime

Carries out `FE-LIB-NEXT`.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-NEXT-01 | Routes **MUST** use the App Router; the Pages Router is banned. | CI check † |
| FE-STACK-03 | `[N16]` Projects **MUST** run Node ≥ 20.9; Node 18 is unsupported. | CI setup |
| FE-NEXT-04 | Code **MUST** await `cookies()`, `headers()`, `draftMode()`, `params`, and `searchParams`. | `tsc` (`[N16]` removes the synchronous escape hatch) |

## Caching and revalidation

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-NEXT-05 | A route relying on cached data **MUST** declare that intent explicitly via `fetch` options or segment config. | review |
| FE-NEXT-06 | Cache invalidation **MUST** use `revalidateTag` / `revalidatePath`, never a manual cache-busting workaround. | `tsc` on `[N16]` |
| FE-NEXT-15 | `[N16]` Projects **MUST** enable `cacheComponents`; for Next 15 projects on canary PPR this is a separate migration, not a switch. | `next.config.ts` |

## Middleware / proxy

Detail of `FE-DATA-07` (read transport) and `FE-SEC-02` (authorization).

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-NEXT-07 | Middleware / proxy **MUST** be scoped with `matcher` and contain no blocking I/O or heavy computation. | review |
| FE-NEXT-08 | `[N16]` The interception file **MUST** be named `proxy.ts` and export `proxy`, on the Node runtime. | codemod + review |
| FE-NEXT-14 | A BFF proxy Route Handler **MUST** forward an explicit header allow-list, use `redirect: 'manual'`, and send no CORS headers. | review |

## Server Actions

Detail of `FE-DATA-08` and `FE-DATA-14`. Two properties of actions that are regularly misread:

- **They are sequential.** Next dispatches Server Actions one at a time so the re-rendered tree stays
  consistent. `Promise.all([action1(), action2()])` does not give you parallelism — it is an illusion,
  the requests queue up. Genuinely parallel requests need a Route Handler.
- **They buffer the request body.** A Server Action receives already fully parsed `FormData`, so the
  entire file lands in process memory first. There is no streaming access to the body.

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-NEXT-16 | `Promise.all` over Server Actions **MUST NOT** be used for parallelism — they execute sequentially. | review |
| FE-NEXT-17 | A Server Action **MUST NOT** accept heavy file uploads; the proxy to the backend **MUST** be a streaming Route Handler. | review |

## Page rendering

| ID | Rule | Enforced by |
| --- | --- | --- |
| FE-NEXT-09 | Every top-level route segment **MUST** provide `loading.tsx`, `error.tsx`, and `not-found.tsx`; the root also `global-error.tsx`. | CI script † |
| FE-NEXT-10 | SEO metadata **MUST** come from `generateMetadata` / the `metadata` export, never from hand-written `<head>` tags. | `@next/eslint-plugin-next » no-head-element` |
| FE-NEXT-12 | Localized routes **MUST** use next-intl's `[locale]` segment, with locale negotiation in middleware / proxy. | review |
