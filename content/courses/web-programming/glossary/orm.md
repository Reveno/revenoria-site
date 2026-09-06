---
title: "ORM"
date: 2026-08-12
tags:
  - глосарій
  - веб-програмування
draft: false
---
# ORM

> [!definition] Визначення
> Object-Relational Mapping — бібліотека, що дозволяє працювати з реляційною базою даних через обʼєкти й методи мови програмування замість «сирого» SQL. Сама «перекладає» виклики функцій у SQL-запити й результат назад у звичні обʼєкти.

> [!example] Приклад
> `prisma.course.findMany({ where: { module: "01-osnovy" } })` замість `SELECT * FROM courses WHERE module = '01-osnovy'`.

## Де розглядається
- [[courses/web-programming/lectures/10-bazy-danykh-ta-orm|Лекція 10. Бази даних та ORM]] — Prisma, моделювання схеми, звʼязки, міграції, CRUD-операції
