---
title: "Middleware"
date: 2026-08-12
tags:
  - глосарій
  - веб-програмування
draft: false
---
# Middleware

> [!definition] Визначення
> Функція з сигнатурою `(req, res, next)`, що обробляє HTTP-запит на шляху від сервера Express до конкретного маршруту. Може пропустити запит далі через `next()`, зупинити його власною відповіддю, чи передати помилку далі через `next(err)` до централізованого обробника.

> [!example] Приклад
> `app.use(express.json())` – вбудоване middleware, що розбирає JSON-тіло запиту в `req.body`.

## Де розглядається
- [[courses/web-programming/lectures/09-express-middleware-ta-arkhitektura-zastosunku|Лекція 9. Express - middleware та архітектура застосунку]] – механізм, порядок виконання, власне middleware, обробка помилок
- [[courses/web-programming/lectures/08-node-js-ta-express-js-osnovy|Лекція 8. Node.js та Express.js - основи]] – перша згадка через express.json()
