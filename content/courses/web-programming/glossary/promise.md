---
title: "Promise"
date: 2026-08-12
tags:
  - глосарій
  - веб-програмування
draft: false
---
# Promise

> [!definition] Визначення
> Обʼєкт, що представляє результат асинхронної операції, яка ще може бути не завершена. Має стан `pending` → `fulfilled` або `rejected`; `async`/`await` – синтаксичний спосіб зручно працювати з Promise, що дозволяє перехоплювати помилки через звичайний `try`/`catch`.

> [!example] Приклад
> `const data = await fetch(url).then(r => r.json());` – очікування мережевого запиту без блокування решти сторінки.

## Де розглядається
- [[courses/web-programming/lectures/03-dom-podii-ta-asynkhronnist|Лекція 3. DOM, події та асинхронність]] – стани Promise, async/await, fetch, event loop
- [[courses/web-programming/lectures/02-javascript-es2024|Лекція 2. JavaScript ES2024+]] – try/catch і межі його роботи з асинхронним кодом
