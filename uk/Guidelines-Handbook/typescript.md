# TypeScript — Guidelines

> Рівень **Handbook**: пояснює поняття й показує, як застосовувати рішення зі
> [Standards](../Standards/index-uk.md). Правила цього рівня діють у code review, але стратегічний
> контракт — там. Реалізації — у [Reference-Recipes](../Reference-Recipes/).

## Escape hatches

Деталізація `FE-TS-02`.

| ID | Правило | Контроль |
| --- | --- | --- |
| FE-TS-02 | Код **MUST NOT** використовувати `any`, окрім значення в `catch`; використовуй `unknown` і звужуй тип. | `typescript-eslint » no-explicit-any` |
| FE-TS-03 | Пригнічена помилка типів **MUST** використовувати `@ts-expect-error` з обґрунтуванням; `@ts-ignore` заборонений. | `typescript-eslint » ban-ts-comment` |
| FE-TS-08 | Type assertions і non-null `!` **MUST** супроводжуватися коментарем, чому висновок типу неможливий. | review |

## Форма типів

| ID | Правило | Контроль |
| --- | --- | --- |
| FE-TS-05 | Типи DTO та API **MUST** виводитися з тієї самої однієї схеми, а не дублюватися руками. | review |
| FE-TS-06 | Новий код **MUST NOT** оголошувати TypeScript `enum`; використовуй union-літерал або `as const`-об'єкт. | `eslint » no-restricted-syntax` † |
| FE-TS-07 | Форми об'єктів **MUST** описуватись через `type`; `interface` — лише для declaration merging або контрактів класів. | `typescript-eslint » consistent-type-definitions` |

## Прапорці компілятора

Деталізація `FE-TS-01`.

| ID | Правило | Контроль |
| --- | --- | --- |
| FE-TS-09 | Нові пакети **SHOULD** одразу вмикати `noUncheckedIndexedAccess` і `exactOptionalPropertyTypes`. | `tsconfig` |
