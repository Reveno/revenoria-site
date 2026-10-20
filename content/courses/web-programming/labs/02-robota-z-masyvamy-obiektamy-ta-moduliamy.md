---
title: "Лабораторна робота 2. Робота з масивами, об'єктами та модулями JavaScript"
date: 2026-10-08
tags:
  - веб-програмування
draft: false
---
# Робота з масивами, об'єктами та модулями JavaScript

> [!abstract] Коротко
> Ви побудуєте невелику бібліотеку аналітики над масивом з двох десятків замовлень: фільтрування, впорядкування, підсумки через `reduce`, групування, деструктуризація параметрів. Код розділите на ES-модулі, а правильність підтвердите автоматичними тестами на вбудованому `node:test` – включно з крайніми випадками. Наприкінці самі реалізуєте `map`, щоб зрозуміти, як працює те, чим користуєтеся щодня.

## Мета і результати навчання

Після виконання роботи ви вмієте:

- [ ] обробляти колекції методами `map`, `filter`, `reduce`, `find`, `some`, `every` без ручних циклів;
- [ ] писати чисті функції, що не змінюють вхідні дані;
- [ ] застосовувати деструктуризацію, значення за замовчуванням, `spread` і `rest`, оператори `?.` та `??`;
- [ ] розділяти код на ES-модулі з іменованим експортом і експортом за замовчуванням;
- [ ] писати модульні тести та покривати крайні випадки.

## Професійні компетентності

Після виконання лабораторної роботи здобувач освіти повинен навчитися обробляти колекції даних методами масивів, працювати з об'єктами й деструктуризацією та організовувати код у модулі.

Відповідно до освітньої програми робота формує компетентності:

- СК03. Здатність застосовувати теоретичні та емпіричні знання для розроблення, тестування, впровадження та супроводу програмного забезпечення.
- СК07. Здатність розробляти модулі і компоненти програмного забезпечення за допомогою типових алгоритмів та інструментів.

## Перелік необхідного обладнання та програмного забезпечення

- Персональний комп'ютер, редактор коду (Visual Studio Code).
- Node.js версії 20 LTS або новішої (перевірка: `node --version`).
- Термінал (Bash, PowerShell або термінал VS Code).

## Варіанти завдання

Предметна область – за номером у журналі (номер за модулем 5). Структура даних однакова, змінюються назви полів і зміст підсумків.

| Варіант | Сутність | Текстове поле для групування | Числові поля |
|---|---|---|---|
| 1 | Замовлення інтернет-магазину | `category` | `price`, `qty` |
| 2 | Квитки на заходи | `venue` | `price`, `seats` |
| 3 | Позики в бібліотеці | `genre` | `days`, `fine` |
| 4 | Тренування | `sport` | `minutes`, `calories` |
| 0 | Витрати бюджету | `type` | `amount`, `count` |

Нижче по тексту наведено приклад для варіанта 1; для інших замініть назви полів.

## Теоретична частина

### Декларативна обробка колекцій

Ручний цикл змушує описувати, **як** обчислити результат: заводимо лічильник, перебираємо, перевіряємо, складаємо. Методи масивів дозволяють описати, **що** ми хочемо отримати. `filter` залишає елементи, що задовольняють умову, `map` перетворює кожен елемент, `reduce` згортає все в одне значення. Ланцюжок із трьох методів читається як речення: «візьми замовлення електроніки, порахуй суму кожного і додай».

Важлива властивість цих методів: вони **не змінюють** початковий масив, а повертають новий. Код без прихованих побічних ефектів простіше тестувати і складніше зламати: жодна функція не «підсовує» несподіваних змін у дані, якими користуються інші.

> [!definition] Чиста функція
> Функція, результат якої залежить лише від вхідних аргументів і яка не змінює нічого поза собою (вхідні дані, глобальні змінні, файли). З однаковими аргументами завжди повертає однаковий результат.

### Пастка методу `sort`

Метод `sort` – виняток: він **змінює масив на місці**. Крім того, без функції порівняння він сортує елементи як рядки, тому `[10, 9, 1].sort()` дасть `[1, 10, 9]`. У сучасному JavaScript (Node.js 20+) для безпечного варіанта існує `toSorted`, що повертає копію. Саме його ми використаємо.

### Деструктуризація, `spread` і `rest`

Деструктуризація витягує потрібні поля з об'єкта чи елементи з масиву одним виразом: `const { id, price } = order`. Її можна ставити прямо в параметри функції та задавати значення за замовчуванням, а для вкладених полів – застосовувати оператор необов'язкового доступу `?.` та злиття `??`. Три крапки мають два значення залежно від місця: у виклику чи літералі `spread` розгортає (`{ ...order, qty: 2 }`), у параметрах функції `rest` збирає (`(first, ...others)`).

### Модулі

Один файл – один модуль. Те, що модуль має показати зовнішньому світу, позначається `export`, а той, хто хоче це використати, пише `import`. Іменований експорт (`export function groupBy`) може бути в модулі багато, і при імпорті імена беруть у фігурних дужках. Експорт за замовчуванням (`export default`) один на модуль і імпортується під довільним іменем. Практичне правило: віддавайте перевагу іменованому експорту, бо інструменти редактора краще допомагають, а імена не розходяться по проєкту.

## Підготовка

```bash
mkdir lab02-orders && cd lab02-orders
npm init -y
```

Відкрийте `package.json` і додайте два рядки: `"type": "module"` (щоб Node розумів `import`/`export`) та скрипти.

```json
{
  "name": "lab02-orders",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "start": "node src/index.js",
    "test": "node --test"
  }
}
```

Створіть структуру:

```
lab02-orders/
  package.json
  src/
    data.js
    orders.js
    index.js
  test/
    orders.test.js
```

## Хід роботи

### Етап 1. Дані

У `src/data.js` розмістіть масив мінімум із 10 записів (для звіту раджу 12). Є важливі вимоги: щонайменше два записи з однаковою категорією, один запис без необов'язкового поля `coupon`, і дати у форматі ISO.

```js
export const orders = [
  { id: 1,  customer: 'Олена', category: 'електроніка', price: 1200, qty: 1, date: '2026-09-02', coupon: 'AUTUMN' },
  { id: 2,  customer: 'Ігор',  category: 'книги',       price: 300,  qty: 3, date: '2026-09-03' },
  { id: 3,  customer: 'Олена', category: 'книги',       price: 450,  qty: 2, date: '2026-09-05' },
  { id: 4,  customer: 'Марія', category: 'одяг',        price: 800,  qty: 1, date: '2026-09-06', coupon: 'WELCOME' },
  { id: 5,  customer: 'Петро', category: 'електроніка', price: 5400, qty: 1, date: '2026-09-07' },
  { id: 6,  customer: 'Марія', category: 'книги',       price: 250,  qty: 4, date: '2026-09-09' },
  { id: 7,  customer: 'Ігор',  category: 'одяг',        price: 1500, qty: 2, date: '2026-09-11' },
  { id: 8,  customer: 'Олена', category: 'електроніка', price: 300,  qty: 5, date: '2026-09-12' },
  { id: 9,  customer: 'Петро', category: 'книги',       price: 600,  qty: 1, date: '2026-09-14' },
  { id: 10, customer: 'Марія', category: 'електроніка', price: 2100, qty: 1, date: '2026-09-15', coupon: 'AUTUMN' },
  { id: 11, customer: 'Ігор',  category: 'одяг',        price: 400,  qty: 3, date: '2026-09-17' },
  { id: 12, customer: 'Петро', category: 'книги',       price: 150,  qty: 6, date: '2026-09-20' },
];
```

### Етап 2. Фільтрування, впорядкування, підсумки

У `src/orders.js` створимо функції, кожна з яких приймає масив і повертає новий результат. Зверніть увагу на деструктуризацію в параметрах і на значення за замовчуванням: вони документують контракт функції прямо в підписі.

```js
export const total = ({ price, qty = 1 }) => price * qty;

export function filterBy(list, predicate) {
  return list.filter(predicate);
}

export function sortBy(list, field, direction = 'asc') {
  const sign = direction === 'desc' ? -1 : 1;
  return list.toSorted((a, b) => {
    const x = a[field];
    const y = b[field];
    if (x === y) return 0;
    return (x > y ? 1 : -1) * sign;
  });
}

export function sumBy(list, getValue = total) {
  return list.reduce((acc, item) => acc + getValue(item), 0);
}
```

Розгляньте `reduce` уважніше. Другий аргумент, `0`, – початкове значення **акумулятора**. На кожному кроці функція отримує поточне значення акумулятора й чергового елемента та повертає нове значення акумулятора. Якщо початкове значення не вказати, `reduce` на порожньому масиві кине помилку, а на непорожньому візьме за початок перший елемент. Тому початкове значення вказуємо завжди.

> [!warning] Типова помилка
> Не пишіть `list.sort(...)` у функціях обробки: ви змінюєте масив, який отримали від викликача. Якщо два місця коду посилаються на той самий масив, одне з них побачить несподівано змінений порядок. Використовуйте `toSorted` або `[...list].sort(...)`.

### Етап 3. Групування й пошук

```js
export function groupBy(list, key) {
  return list.reduce((groups, item) => {
    const name = typeof key === 'function' ? key(item) : item[key];
    (groups[name] ??= []).push(item);
    return groups;
  }, {});
}

export function summarize(list, key = 'category') {
  const groups = groupBy(list, key);
  return Object.entries(groups).map(([name, items]) => ({
    name,
    count: items.length,
    sum: sumBy(items),
    average: items.length ? sumBy(items) / items.length : 0,
  }));
}

export function topCustomer(list) {
  const byCustomer = summarize(list, 'customer');
  return byCustomer.toSorted((a, b) => b.sum - a.sum)[0] ?? null;
}
```

Оператор `??=` присвоює значення лише тоді, коли в змінної `null` або `undefined`. Рядок `(groups[name] ??= []).push(item)` читається так: «візьми групу з цим іменем; якщо її ще нема, створи порожню; додай елемент». Функція `key` може бути рядком (ім'я поля) або функцією (обчислюваний ключ): `groupBy(orders, o => o.date.slice(0, 7))` згрупує за місяцями.

> [!info] Для допитливих
> У Node.js 21 і новіших є вбудований `Object.groupBy(list, fn)`. Ми пишемо власну функцію, щоб зрозуміти механізм і щоб код працював на Node.js 20 LTS. У звіті порівняйте результат вашої функції з `Object.groupBy`, якщо ваша версія Node його підтримує.

### Етап 4. Пошук, деструктуризація і `spread`

Додайте до `src/orders.js`:

```js
export function withCoupon(list) {
  return list.filter(({ coupon }) => coupon !== undefined);
}

export function applyDiscount(order, { percent = 10, minTotal = 0 } = {}) {
  if (total(order) < minTotal) return order;
  return { ...order, price: Math.round(order.price * (100 - percent)) / 100 };
}

export function hasAny(list, predicate) {
  return list.some(predicate);
}

export function allPaid(list) {
  return list.every(({ price }) => price > 0);
}

export function findById(list, id) {
  return list.find(order => order.id === id) ?? null;
}
```

Функція `applyDiscount` повертає **новий** об'єкт через `spread` і не чіпає оригінал. Параметр `{ percent = 10, minTotal = 0 } = {}` – деструктуризація зі значеннями за замовчуванням, а кінцеве `= {}` дозволяє викликати функцію взагалі без другого аргументу.

### Етап 5. Головний файл і запуск

```js
// src/index.js
import { orders } from './data.js';
import {
  filterBy, sortBy, sumBy, summarize, topCustomer, findById, withCoupon, applyDiscount,
} from './orders.js';

const expensive = filterBy(orders, ({ price, qty }) => price * qty > 1000);
console.log('Замовлення дорожчі за 1000:', expensive.map(o => o.id));

console.log('Топ-3 за вартістю:');
console.table(sortBy(orders, 'price', 'desc').slice(0, 3));

console.log('Загалом:', sumBy(orders));
console.table(summarize(orders));
console.log('Найактивніший покупець:', topCustomer(orders));
console.log('Зі знижковим купоном:', withCoupon(orders).length);

const order = findById(orders, 5);
console.log('До знижки:', order.price, 'Після:', applyDiscount(order, { percent: 20 }).price);
```

Запустіть: `npm start`. Результат має містити таблицю з підсумками по категоріях.

**Контрольна точка 1.** Запишіть у звіт значення `sumBy(orders)` для вашого набору даних і підтвердіть його вручну (калькулятором або електронною таблицею) для будь-якої однієї категорії.

### Етап 6. Тести на крайні випадки

Тести у Node.js не потребують сторонніх бібліотек. Створіть `test/orders.test.js`:

```js
import { test } from 'node:test';
import assert from 'node:assert/strict';
import { sumBy, groupBy, summarize, topCustomer, sortBy, findById, applyDiscount } from '../src/orders.js';

const sample = [
  { id: 1, customer: 'A', category: 'x', price: 100, qty: 2 },
  { id: 2, customer: 'B', category: 'x', price: 50 },
  { id: 3, customer: 'A', category: 'y', price: 10, qty: 5 },
];

test('sumBy рахує суму, qty за замовчуванням 1', () => {
  assert.equal(sumBy(sample), 100 * 2 + 50 + 10 * 5);
});

test('порожній масив: сума 0, групи порожні, топ-покупця немає', () => {
  assert.equal(sumBy([]), 0);
  assert.deepEqual(groupBy([], 'category'), {});
  assert.deepEqual(summarize([]), []);
  assert.equal(topCustomer([]), null);
});

test('відсутнє поле: групується під ключем undefined, а не падає', () => {
  const groups = groupBy([{ id: 1 }, { id: 2, category: 'x' }], 'category');
  assert.equal(groups.undefined.length, 1);
  assert.equal(groups.x.length, 1);
});

test('sortBy не змінює вхідний масив', () => {
  const copy = structuredClone(sample);
  sortBy(sample, 'price', 'desc');
  assert.deepEqual(sample, copy);
});

test('sortBy: за спаданням і за зростанням', () => {
  assert.deepEqual(sortBy(sample, 'price', 'desc').map(o => o.id), [1, 2, 3]);
  assert.deepEqual(sortBy(sample, 'price').map(o => o.id), [3, 2, 1]);
});

test('findById повертає null, якщо запису немає', () => {
  assert.equal(findById(sample, 999), null);
});

test('applyDiscount повертає новий об\'єкт і не змінює оригінал', () => {
  const order = { id: 1, price: 200, qty: 1 };
  const result = applyDiscount(order, { percent: 25 });
  assert.equal(result.price, 150);
  assert.equal(order.price, 200);
  assert.notEqual(result, order);
});
```

Запустіть `npm test`. Усі тести мають бути зеленими. Потім **навмисно зламайте** `sortBy`, замінивши `toSorted` на `sort`, і подивіться, який тест упаде і яке повідомлення покаже `assert`. Саме так тест доводить свою корисність.

**Контрольна точка 2.** Знімок виводу `npm test` зі статистикою `pass` і `fail` – у звіт. Додайте ще **два власні тести** на крайні випадки (наприклад, купон, що відсутній, та порожній рядок як ключ групування).

### Етап 7. Модуль зі значенням за замовчуванням

Додайте окремий файл `src/format.js`, щоб побачити різницю двох видів експорту:

```js
// src/format.js
const uah = new Intl.NumberFormat('uk-UA', { style: 'currency', currency: 'UAH' });

export function money(value) {
  return uah.format(value);
}

export default function report(rows) {
  return rows.map(r => `${r.name.padEnd(14)} ${String(r.count).padStart(3)}  ${money(r.sum)}`).join('\n');
}
```

І підключіть:

```js
import report, { money } from './format.js';
console.log(report(summarize(orders)));
console.log('Всього:', money(sumBy(orders)));
```

## Типові помилки

> [!warning] Типова помилка
> - `Cannot use import statement outside a module` – у `package.json` немає `"type": "module"`.
> - Розширення `.js` у шляху імпорту обов'язкове в ES-модулях Node.js: `import … from './orders.js'`, а не `'./orders'`.
> - `forEach` замість `map`: `forEach` нічого не повертає, і змінна дорівнює `undefined`.
> - Мутація у `reduce`: `acc.push(...)` у масиві-акумуляторі – допустимо, якщо акумулятор ваш; але зміна елементів вхідного масиву – ні.
> - `list.sort()` без компаратора на числах – лексикографічне впорядкування.
> - `price * qty` для грошей: `0.1 + 0.2 !== 0.3`. Для сум у реальних системах зберігають копійки цілими або використовують десяткову бібліотеку.

## Завдання для самостійного виконання

Реалізуйте власну функцію, що повторює поведінку `map`, не використовуючи вбудований метод. Порівняйте результати на тестових даних.

Вимоги до цієї версії завдання:

1. Створіть `src/myArray.js` з функціями `myMap(list, fn, thisArg)`, `myFilter` і `myReduce` (з урахуванням випадку, коли початкове значення не передано).
2. Функція `fn` має отримувати три аргументи так само, як і в оригіналі: елемент, індекс, масив.
3. Розріджені масиви (`[1, , 3]`) повинні пропускати «дірки», як і вбудований `map`.
4. Напишіть тести, які порівнюють `myMap` з `Array.prototype.map` на 5 різних масивах, включно з порожнім і розрідженим.
5. Поясніть у звіті, чому `myReduce([], fn)` без початкового значення має кидати `TypeError`.

## Завдання підвищеної складності

> [!example] Рівень «добре» (необов'язково)
> Реалізуйте функцію `pipe(...fns)`, яка послідовно застосовує функції до масиву, і перепишіть аналітику як конвеєр: `pipe(withCoupon, list => sortBy(list, 'date'), summarize)(orders)`. Додайте `compose` (у зворотному порядку) і поясніть різницю.

> [!example] Рівень «відмінно» (необов'язково)
> Зробіть `groupBy` ледачим: поверніть ітератор, який видає групи по одній, а не будує весь об'єкт одразу (генератор `function*`). Виміряйте час і пам'ять через `console.time` та `process.memoryUsage()` на масиві з 1 000 000 згенерованих записів і порівняйте з eager-версією.

## Вимоги до звіту

1. Титульна сторінка, варіант завдання.
2. Лістинг ключових функцій (`sumBy`, `groupBy`, `summarize`) з поясненням, чому вони чисті.
3. Знімок виводу `npm start` і `npm test`.
4. Таблиця «було / стало» для експерименту з `sort` і `toSorted`: який результат отримали, чому.
5. Результат порівняння `myMap` і `Array.prototype.map`.
6. Відповіді на 3 контрольні питання на ваш вибір.
7. Висновки.

Код здається у вигляді репозиторію (без теки `node_modules`, додайте її в `.gitignore`).

## Критерії оцінювання

| Критерій | Балів |
|---|---|
| Коректність функцій обробки (фільтр, сортування, `reduce`, групування) | 25 |
| Чистота функцій: відсутність мутацій вхідних даних | 15 |
| Модульна структура, експорти, імпорти | 15 |
| Тести: покриття крайніх випадків, усі зелені | 20 |
| Власна реалізація `map` і порівняння | 10 |
| Звіт | 5 |
| Захист | 10 |
| **Разом** | **100** |

## Контрольні питання

> [!question] Перевірте себе
> 1. Чим `map` відрізняється від `forEach`?
> 2. Як працює `reduce` і що таке акумулятор?
> 3. Що таке деструктуризація та де вона корисна?
> 4. Чим іменований експорт відрізняється від експорту за замовчуванням?
> 5. Чому методи, які не змінюють початковий масив, вважають безпечнішими?
> 6. Що поверне `[10, 9, 1].sort()` і чому?
> 7. У чому різниця між `||` і `??`?
> 8. Що таке «чиста функція» і чому її легше тестувати?

> [!exam] На іспиті
> Завдання на папері: «Напишіть `reduce`, що рахує кількість елементів кожного типу в масиві». Типова помилка – забути початкове значення `{}` і мутувати перший елемент вхідного масиву.

## Далі

У наступній лабораторній ви додасте до цього коду статичні типи: [[courses/web-programming/labs/03-typizatsiia-danykh-i-funktsii-zasobamy|Лабораторна робота 3. Типізація даних і функцій засобами TypeScript]]. Теорія з синтаксису – у [[courses/web-programming/lectures/02-javascript-es2024|лекції 2]], а асинхронні функції, які ви побачите в практичній роботі 1, пояснює [[courses/web-programming/glossary/promise|Promise]].

## Література

- MDN Web Docs: Array, Destructuring assignment, JavaScript modules (developer.mozilla.org)
- Node.js Documentation: Test runner, ECMAScript modules (nodejs.org)
- javascript.info: Array methods, Destructuring, Modules (javascript.info)
