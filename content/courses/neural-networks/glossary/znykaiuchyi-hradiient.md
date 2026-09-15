---
title: "Зникаючий градієнт"
date: 2026-09-01
tags:
  - глосарій
  - нейронні-мережі
draft: false
---
# Зникаючий градієнт

> [!definition] Визначення
> Проблема глибоких мереж: під час зворотного поширення градієнт багаторазово множиться на малі числа й до перших шарів доходить майже нульовим – вони фактично перестають навчатися.

> [!example] Приклад
> Сигмоїда має похідну не більшу за 0,25; після десяти шарів множник стає меншим за одну мільйонну.

## Пов'язані поняття

- [[courses/neural-networks/glossary/zvorotne-poshyrennia-pomylky|Зворотне поширення помилки]]
- [[courses/neural-networks/glossary/relu|ReLU]]
- [[courses/neural-networks/glossary/lstm|LSTM]]
- [[courses/neural-networks/glossary/funktsiia-aktyvatsii|Функція активації]]

## Де розглядається

- [[courses/neural-networks/lectures/10-populiarni-funktsii-aktyvatsii-ta-ikh-rol|Лекція 10. Популярні функції активації та їх роль]]
- [[courses/neural-networks/lectures/12-vstup-do-rekurentnykh-neironnykh-merezh-dlia|Лекція 12. Вступ до рекурентних нейронних мереж для роботи з послідовностями]]
