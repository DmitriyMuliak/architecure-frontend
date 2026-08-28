# Безпека — Guidelines

> Рівень **Handbook**: пояснює поняття й показує, як застосовувати рішення зі
> [Standards](../Standards/index-uk.md). Правила цього рівня діють у code review, але стратегічний
> контракт — там. Реалізації — у [Reference-Recipes](../Reference-Recipes/).

## Межа довіри

Деталізація `FE-SEC-02` і `FE-TS-04`.

| ID | Правило | Контроль |
| --- | --- | --- |
| FE-SEC-03 | Props клієнтських компонентів і значення, що повертають Server Actions, **MUST** бути явними DTO — RSC payload читається публічно. | review |
| FE-SEC-04 | Усі дані від клієнта **MUST** валідуватися на сервері перед використанням. | review |
| FE-SEC-08 | Route Handlers, що змінюють стан, **MUST** самі перевіряти `Origin` проти `Host` — Server Actions отримують це автоматично. | review |

## Недовірений вміст

Деталізація `FE-SEC-05`.

| ID | Правило | Контроль |
| --- | --- | --- |
| FE-SEC-06 | URL від користувача, використані в `href`, навігації або редиректах, **MUST** проходити allowlist із відкиданням схеми `javascript:`. | review |
| FE-SEC-09 | Посилання з `target="_blank"` **MUST** мати `rel="noopener noreferrer"`, а обробники `postMessage` **MUST** перевіряти origin. | `eslint-plugin-react » react/jsx-no-target-blank` |

## Заголовки відповіді

| ID | Правило | Контроль |
| --- | --- | --- |
| FE-SEC-10 | Кожен застосунок **MUST** віддавати CSP, HSTS, `X-Content-Type-Options` і `Referrer-Policy`; строгий CSP з nonce свідомо відкладено. | smoke-тест хедерів † |
