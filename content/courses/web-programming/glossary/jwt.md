---
title: "JWT"
date: 2026-08-12
tags:
  - глосарій
  - веб-програмування
draft: false
---
# JWT

> [!definition] Визначення
> JSON Web Token — самодостатній токен автентифікації у форматі `header.payload.signature`. Дані й роль користувача записані прямо в токені; підпис гарантує, що вміст не підмінили, тому серверу не потрібно окремо зберігати сесію.

> [!example] Приклад
> `jwt.sign({ userId, role }, secret, { expiresIn: "1h" })` — видача токена після логіну; `jwt.verify(token, secret)` — перевірка при кожному наступному запиті.

## Де розглядається
- [[courses/web-programming/lectures/12-avtentyfikatsiia-ta-bezpeka-vebdodatkiv|Лекція 12. Автентифікація та безпека вебдодатків]] — JWT проти сесій, ролі, безпечне зберігання на клієнті
