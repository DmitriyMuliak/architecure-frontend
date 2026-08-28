# Next.js — Guidelines

> Рівень **Handbook**: пояснює поняття й показує, як застосовувати рішення зі
> [Standards](../Standards/index-uk.md). Правила цього рівня діють у code review, але стратегічний
> контракт — там. Реалізації — у [Reference-Recipes](../Reference-Recipes/).

## Межа сервера

Механізм виконання `FE-SEC-07` (секрети не потрапляють у клієнтський бандл) і `FE-NEXT-13`
(`'use server'` лише в `*.actions.ts`). `server-only` кидає помилку **на етапі білду**, тож це
єдина межа тут, яка не тримається на уважності рев'ювера.

| ID | Правило | Контроль |
| --- | --- | --- |
| FE-NEXT-02 | Компоненти **MUST** бути Server Components за замовчуванням; `"use client"` додається лише на інтерактивних листках. | review |
| FE-NEXT-03 | Модуль із секретами або серверними SDK **MUST** імпортувати `server-only`. | `server-only` (помилка білду) |
| FE-NEXT-13 | `'use server'` **MUST** зустрічатися лише у виділених файлах `*.actions.ts` — множина RPC-ендпоінтів має бути перелічуваною. | `eslint » no-restricted-syntax` † |

## Версії та рантайм

Виконання `FE-LIB-NEXT`.

| ID | Правило | Контроль |
| --- | --- | --- |
| FE-NEXT-01 | Роути **MUST** використовувати App Router; Pages Router заборонений. | CI check † |
| FE-STACK-03 | `[N16]` Проєкти **MUST** працювати на Node ≥ 20.9; Node 18 не підтримується. | CI setup |
| FE-NEXT-04 | Код **MUST** очікувати (`await`) `cookies()`, `headers()`, `draftMode()`, `params` і `searchParams`. | `tsc` (`[N16]` прибирає синхронний обхідний шлях) |

## Кешування та ревалідація

| ID | Правило | Контроль |
| --- | --- | --- |
| FE-NEXT-05 | Роут, що спирається на закешовані дані, **MUST** декларувати це явно через опції `fetch` або конфіг сегмента. | review |
| FE-NEXT-06 | Інвалідація кешу **MUST** відбуватися через `revalidateTag` / `revalidatePath`, а не через ручні обхідні прийоми. | `tsc` на `[N16]` |
| FE-NEXT-15 | `[N16]` Проєкти **MUST** вмикати `cacheComponents`; для проєктів на Next 15 з canary-PPR це окрема міграція, не перемикач. | `next.config.ts` |

## Middleware / proxy

Деталізація `FE-DATA-07` (транспорт читань) і `FE-SEC-02` (авторизація).

| ID | Правило | Контроль |
| --- | --- | --- |
| FE-NEXT-07 | Middleware / proxy **MUST** обмежуватися `matcher` і не містити блокуючого I/O чи важких обчислень. | review |
| FE-NEXT-08 | `[N16]` Файл перехоплення **MUST** називатися `proxy.ts` і експортувати `proxy`, на Node runtime. | codemod + review |
| FE-NEXT-14 | BFF-проксі Route Handler **MUST** форвардити явний allow-list заголовків, використовувати `redirect: 'manual'` і не віддавати жодних CORS-заголовків. | review |

## Server Actions

Деталізація `FE-DATA-08` і `FE-DATA-14`. Дві властивості екшенів, які регулярно сприймають хибно:

- **Вони послідовні.** Next відправляє Server Actions по одній, щоб перерендерене дерево залишалось
  узгодженим. `Promise.all([action1(), action2()])` не дає паралельності — це ілюзія, запити
  вишикуються в чергу. Для справді паралельних запитів потрібен Route Handler.
- **Вони буферизують тіло запиту.** Server Action отримує вже повністю розпарсену `FormData`,
  тобто весь файл спершу опиняється в пам'яті процесу. Стрімінгового доступу до тіла немає.

| ID | Правило | Контроль |
| --- | --- | --- |
| FE-NEXT-16 | `Promise.all` над Server Actions **MUST NOT** використовуватися для розпаралелювання — вони виконуються послідовно. | review |
| FE-NEXT-17 | Server Action **MUST NOT** приймати завантаження важких файлів; проксі до бекенду **MUST** бути стрімінговим Route Handler. | review |

## Рендеринг сторінки

| ID | Правило | Контроль |
| --- | --- | --- |
| FE-NEXT-09 | Кожен верхньорівневий сегмент роуту **MUST** мати `loading.tsx`, `error.tsx` і `not-found.tsx`; корінь — також `global-error.tsx`. | CI-скрипт † |
| FE-NEXT-10 | SEO-метадані **MUST** надходити з `generateMetadata` / експорту `metadata`, ніколи з ручних тегів `<head>`. | `@next/eslint-plugin-next » no-head-element` |
| FE-NEXT-12 | Локалізовані роути **MUST** використовувати сегмент `[locale]` з next-intl, з визначенням локалі в middleware / proxy. | review |
