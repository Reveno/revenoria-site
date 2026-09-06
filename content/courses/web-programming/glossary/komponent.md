---
title: "Компонент"
date: 2026-08-12
tags:
  - глосарій
  - веб-програмування
draft: false
---
# Компонент

> [!definition] Визначення
> Самодостатня, повторно використовувана функція, що повертає JSX — опис частини інтерфейсу залежно від переданих пропсів. Основний будівельний блок React-застосунку; назва завжди з великої літери (PascalCase).

> [!example] Приклад
> `function ProductCard({ name, price }) { return <div>{name}: {price} грн</div>; }` — використовується як `<ProductCard name="..." price={...} />`.

## Де розглядається
- [[courses/web-programming/lectures/05-react-komponenty-jsx-stan-i-propsy|Лекція 5. React - компоненти, JSX, стан і пропси]] — компоненти, пропси, children, умовний рендер, списки
- [[courses/web-programming/lectures/06-react-khuky-ta-keruvannia-stanom|Лекція 6. React-хуки та керування станом]] — стан і побічні ефекти всередині компонента
