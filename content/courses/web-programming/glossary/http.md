---
title: "HTTP"
date: 2026-08-12
tags:
  - глосарій
  - веб-програмування
draft: false
---
# HTTP

> [!definition] Визначення
> HyperText Transfer Protocol – протокол передачі даних між клієнтом і сервером у форматі запит-відповідь, без збереження стану (stateless). HTTPS – той самий HTTP поверх TLS-шифрування.

> [!example] Приклад
> `GET /courses HTTP/1.1` – запит методом GET на отримання списку курсів; сервер повертає статус-код (`200 OK`, `404 Not Found`) і тіло відповіді.

## Де розглядається
- [[courses/web-programming/lectures/01-iak-pratsiuie-veb-html-ta-css|Лекція 1. Як працює веб, HTML та CSS]] – методи, статус-коди, заголовки, кешування
- [[courses/web-programming/lectures/11-rest-api-ta-graphql|Лекція 11. REST API та GraphQL]] – HTTP-методи як основа REST
