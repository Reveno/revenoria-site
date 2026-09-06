---
title: "Хук"
date: 2026-08-12
tags:
  - глосарій
  - веб-програмування
draft: false
---
# Хук

> [!definition] Визначення
> Функція (з префіксом `use`), що дає функціональному компоненту React доступ до стану, побічних ефектів чи інших можливостей. Викликається лише на верхньому рівні компонента, у незмінному порядку між рендерами — інакше React переплутає, яке значення якому хуку належить.

> [!example] Приклад
> `const [count, setCount] = useState(0);` — базовий хук стану; `useEffect(() => {...}, [])` — хук для побічних ефектів.

## Де розглядається
- [[courses/web-programming/lectures/06-react-khuky-ta-keruvannia-stanom|Лекція 6. React-хуки та керування станом]] — useState, useEffect, useRef, useMemo, useCallback, власні хуки, Context
