---
title: "Лабораторна робота 3. Типізація даних і функцій засобами TypeScript"
date: 2026-10-08
tags:
  - веб-програмування
draft: false
---
# Типізація даних і функцій застосунку засобами TypeScript

> [!abstract] Коротко
> Ви перенесете логіку «менеджера завдань» зі звичайного JavaScript у TypeScript із суворим режимом: опишете інтерфейси, об'єднання типів, узагальнені функції та перелік статусів з перевіркою вичерпності. Навмисно зробите кілька помилок і прочитаєте повідомлення компілятора. Головне ж – зрозумієте межу, де типи закінчуються: дані з мережі компілятор не перевіряє, тому ви напишете власну перевірку форми відповіді під час виконання.

## Мета і результати навчання

Після виконання роботи ви вмієте:

- [ ] налаштувати проєкт TypeScript із `strict` і пояснити, які перевірки він вмикає;
- [ ] описувати форму даних інтерфейсами й псевдонімами типів;
- [ ] працювати з об'єднаннями типів і звужувати їх (`typeof`, `in`, дискримінантне поле);
- [ ] писати узагальнені функції та обмежувати параметр типу через `extends`;
- [ ] гарантувати вичерпну обробку варіантів за допомогою типу `never`;
- [ ] перевіряти дані «ззовні» функцією-захисником типу.

## Професійні компетентності

Після виконання лабораторної роботи здобувач освіти повинен навчитися описувати типи даних, інтерфейси й узагальнення та налаштовувати перевірку типів у проєкті.

Відповідно до освітньої програми робота формує компетентності:

- СК03. Здатність застосовувати теоретичні та емпіричні знання для розроблення, тестування, впровадження та супроводу програмного забезпечення.
- СК07. Здатність розробляти модулі і компоненти програмного забезпечення за допомогою типових алгоритмів та інструментів.

## Перелік необхідного обладнання та програмного забезпечення

- Персональний комп'ютер, редактор коду (VS Code; підсвічування помилок TypeScript вбудоване).
- Node.js 20 LTS або новіший, менеджер пакунків `npm`.
- Компілятор TypeScript 5.x (встановлюється в проєкт), утиліта `tsx` для запуску без окремої компіляції.

## Теоретична частина

### Що дають типи і чого вони не дають

TypeScript – це JavaScript із додатковим шаром статичних типів. Компілятор перевіряє типи **до запуску**, а потім видаляє їх повністю: у виконуваному коді від них не лишається нічого. З цього випливає дві речі. Перша: типи не уповільнюють готовий код, бо на момент виконання їх просто немає. Друга, більш підступна: типи гарантують узгодженість лише всередині вашого коду. Коли в програму приходять дані з файлу, форми чи мережі, TypeScript не знає, що там насправді, і вірить вашому твердженню. Для таких меж потрібна перевірка під час виконання, і їй присвячено завдання для самостійної роботи.

### Інтерфейс і псевдонім типу

Обидві конструкції описують форму об'єкта, і для об'єктів вони майже взаємозамінні. Інтерфейс можна розширювати (`extends`) та доповнювати повторним оголошенням; псевдонім `type` вміє більше: описує об'єднання, кортежі, умовні типи. Домовимося так: об'єкти й публічні контракти – `interface`, усе інше – `type`.

### Об'єднання, звуження і дискримінант

Тип `string | number` означає «або те, або інше». Щоб скористатися значенням, компілятору треба довести, яким саме воно є; перевірка `typeof x === 'string'` **звужує** тип усередині гілки. Для об'єктів зручніше мати спільне літеральне поле, за яким їх відрізняють, – **дискримінант**: `{ kind: 'circle', r: number } | { kind: 'rect', w: number, h: number }`.

> [!definition] Звуження типу (narrowing)
> Уточнення типу значення всередині гілки коду на підставі перевірки (`typeof`, `instanceof`, `in`, порівняння з літералом). Після звуження компілятор дозволяє лише ті операції, що правильні для вужчого типу.

### Узагальнення

Функція `identity(x)` повертає те, що отримала. Без узагальнень ми пишемо `any` і втрачаємо зв'язок між входом і виходом. Узагальнений параметр `T` цей зв'язок зберігає: `identity<string>('a')` повертає `string`. Параметр можна обмежити: `<T extends { id: number }>` означає «будь-який тип, що має числове поле `id`».

### Режим `strict`

Це не одна перевірка, а набір. Найкорисніші: `noImplicitAny` (заборона неявного `any`), `strictNullChecks` (`null` та `undefined` не входять у інші типи), `strictFunctionTypes`, `strictPropertyInitialization`. Саме `strictNullChecks` ловить найпоширенішу помилку JavaScript – «Cannot read properties of undefined».

## Підготовка

```bash
mkdir lab03-ts && cd lab03-ts
npm init -y
npm install --save-dev typescript tsx @types/node
npx tsc --init
```

Відкрийте згенерований `tsconfig.json` і залиште лише значущі параметри (решту коментарів можна видалити):

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "outDir": "dist",
    "rootDir": "src",
    "skipLibCheck": true
  },
  "include": ["src"]
}
```

У `package.json` додайте `"type": "module"` і скрипти:

```json
"scripts": {
  "check": "tsc --noEmit",
  "start": "tsx src/index.ts",
  "build": "tsc"
}
```

Команда `npm run check` перевіряє типи, нічого не створюючи. Саме нею ви користуватиметеся найчастіше.

> [!info] Для допитливих
> Параметр `noUncheckedIndexedAccess` додає до результатів індексації `| undefined`: `arr[0]` має тип `T | undefined`. Це неприємно спочатку і дуже корисно потім, бо змушує пам'ятати, що масив може бути порожнім.

## Хід роботи

### Етап 1. Модель предметної області

Створіть `src/types.ts`. Статус описуємо об'єднанням літералів, а не довільним рядком: опечатка в `'dnoe'` стане помилкою компіляції.

```ts
export type Status = 'todo' | 'in-progress' | 'done' | 'archived';

export interface Task {
  readonly id: number;
  title: string;
  status: Status;
  priority: 1 | 2 | 3;
  tags: string[];
  due?: Date;
  assignee?: User;
}

export interface User {
  id: number;
  name: string;
  email: string;
}

export type NewTask = Pick<Task, 'title'> & Partial<Omit<Task, 'id' | 'title'>>;
```

Зверніть увагу на допоміжні типи. `Pick<Task, 'title'>` бере з `Task` лише `title`. `Omit<Task, 'id' | 'title'>` – усе, окрім `id` і `title`. `Partial<…>` робить усі поля необов'язковими. Разом вони кажуть: «щоб створити завдання, обов'язково потрібна лише назва». Модифікатор `readonly` робить `id` незмінним після створення.

### Етап 2. Функція створення

Створіть `src/tasks.ts`.

```ts
import type { NewTask, Task, Status } from './types.js';

let nextId = 1;

export function createTask(input: NewTask): Task {
  return {
    id: nextId++,
    status: 'todo',
    priority: 2,
    tags: [],
    ...input,
  };
}

export function setStatus(task: Task, status: Status): Task {
  return { ...task, status };
}
```

Спробуйте виклики й подивіться, що скаже редактор:

```ts
createTask({ title: 'Підготувати лабораторну' });           // добре
createTask({ title: 'Тест', priority: 5 });                 // помилка: 5 не входить у 1 | 2 | 3
createTask({ title: 'Тест', status: 'finished' });          // помилка: невідомий статус
createTask({ name: 'Тест' });                               // помилка: немає title
```

Усі три помилки видно до запуску. У JavaScript вони стали б тихими багами в базі даних.

**Контрольна точка 1.** Запишіть у звіт текст повідомлення компілятора для виклику з `priority: 5` і поясніть, на що воно вказує.

### Етап 3. Об'єднання типів і звуження

```ts
// src/format.ts
import type { Task } from './types.js';

export function formatDue(due: Date | string | number | undefined): string {
  if (due === undefined) return 'без терміну';
  if (typeof due === 'string') return `рядок: ${due}`;
  if (typeof due === 'number') return new Date(due).toLocaleDateString('uk-UA');
  return due.toLocaleDateString('uk-UA');
}

export function assigneeName(task: Task): string {
  return task.assignee?.name ?? 'не призначено';
}
```

У кожній гілці `if` тип `due` вужчий: після двох перевірок компілятор знає, що лишився `Date`, і дозволяє виклик `.toLocaleDateString`. Спробуйте прибрати перевірку `undefined` і подивіться на помилку.

Тепер дискримінантне об'єднання для різних типів подій:

```ts
export type TaskEvent =
  | { kind: 'created'; task: Task }
  | { kind: 'renamed'; id: number; title: string }
  | { kind: 'deleted'; id: number };

export function describe(event: TaskEvent): string {
  switch (event.kind) {
    case 'created': return `Створено «${event.task.title}»`;
    case 'renamed': return `Перейменовано #${event.id} на «${event.title}»`;
    case 'deleted': return `Видалено #${event.id}`;
  }
}
```

Параметр `noImplicitReturns` змусить компілятор перевірити, що всі випадки покриті. Нижче ми зробимо цю перевірку явною.

### Етап 4. Узагальнені функції

```ts
// src/generic.ts
export function identity<T>(value: T): T {
  return value;
}

export function findById<T extends { id: number }>(list: readonly T[], id: number): T | undefined {
  return list.find(item => item.id === id);
}

export function groupBy<T, K extends string | number>(
  list: readonly T[],
  getKey: (item: T) => K,
): Record<K, T[]> {
  const result = {} as Record<K, T[]>;
  for (const item of list) {
    const key = getKey(item);
    (result[key] ??= []).push(item);
  }
  return result;
}

export function pluck<T, K extends keyof T>(list: readonly T[], key: K): T[K][] {
  return list.map(item => item[key]);
}
```

Перевірте, як виводяться типи:

```ts
const n = identity(42);               // n: number
const s = identity('a');              // s: string
const t = findById(tasks, 3);         // t: Task | undefined
const byStatus = groupBy(tasks, task => task.status);   // Record<Status, Task[]>
const titles = pluck(tasks, 'title'); // string[]
pluck(tasks, 'titel');                // помилка: 'titel' не є ключем Task
```

Тип `K extends keyof T` у `pluck` – ключова ідея: параметр типу залежить від іншого, і компілятор не дозволить назвати неіснуюче поле. У JavaScript така опечатка повертала б масив `undefined` без жодного попередження.

> [!warning] Типова помилка
> `as` – не перетворення, а обіцянка. Рядок `const result = {} as Record<K, T[]>` ми дозволили собі, бо знаємо, що заповнимо об'єкт нижче. Якщо ж ви пишете `data as Task` щодо відповіді мережі, ви обіцяєте компілятору те, чого не перевіряли. Це і є джерело більшості дефектів у типізованих проєктах.

### Етап 5. Вичерпна обробка: тип `never`

Додамо виконання за статусом і зробимо так, щоб новий статус неможливо було забути.

```ts
// src/status.ts
import type { Status } from './types.js';

export function assertNever(x: never): never {
  throw new Error(`Необроблений варіант: ${JSON.stringify(x)}`);
}

export function label(status: Status): string {
  switch (status) {
    case 'todo':        return 'До виконання';
    case 'in-progress': return 'У роботі';
    case 'done':        return 'Виконано';
    case 'archived':    return 'В архіві';
    default:            return assertNever(status);
  }
}
```

У гілці `default` змінна `status` має тип `never`: усі варіанти вже розглянуті, значення там бути не може. Тепер додайте до `Status` п'ятий варіант, наприклад `'blocked'`, і **не** чіпайте `label`. Виконайте `npm run check`: компілятор покаже, що `'blocked'` не можна присвоїти `never`. Ви щойно отримали автоматичний перелік усіх місць, які треба оновити. Після експерименту виправте функцію.

### Етап 6. Навмисні помилки

Створіть файл `src/errors.ts` (його потім можна видалити) і по черзі відтворіть п'ять помилок. Для кожної запишіть у таблицю звіту: фрагмент, текст повідомлення, прапорець `strict`, що її спричиняє.

| № | Фрагмент | Що має статися |
|---|---|---|
| 1 | `function f(x) { return x * 2 }` | неявний `any` для параметра |
| 2 | `const t: Task = tasks[0];` | при `noUncheckedIndexedAccess`: `Task \| undefined` не присвоюється `Task` |
| 3 | `const m: string = null;` | `strictNullChecks` |
| 4 | `task.id = 10;` | `id` лише для читання |
| 5 | `label('finished')` | аргумент не належить `Status` |

Потім вимкніть `"strict": true` у `tsconfig.json`, запустіть `npm run check` ще раз і порахуйте, скільки помилок зникло. Поверніть налаштування назад.

### Етап 7. Головний файл

```ts
// src/index.ts
import { createTask, setStatus } from './tasks.js';
import { groupBy, pluck, findById } from './generic.js';
import { label } from './status.js';
import { describe } from './format.js';

const tasks = [
  createTask({ title: 'Налаштувати проєкт', priority: 1 }),
  createTask({ title: 'Описати типи', tags: ['ts'] }),
  setStatus(createTask({ title: 'Прочитати лекцію' }), 'done'),
];

const byStatus = groupBy(tasks, t => t.status);
for (const [status, list] of Object.entries(byStatus)) {
  console.log(label(status as keyof typeof byStatus), pluck(list, 'title'));
}
console.log(findById(tasks, 2)?.title ?? 'не знайдено');
console.log(describe({ kind: 'created', task: tasks[0]! }));
```

Запустіть `npm start` і `npm run check`.

**Контрольна точка 2.** `npm run check` не видає жодної помилки; `npm start` виводить згруповані завдання. Знімок обох команд у звіт.

## Завдання для самостійного виконання

Опишіть типи для відповіді зовнішнього програмного інтерфейсу та функцію-перевірку, що підтверджує форму отриманих даних під час виконання.

Це найважливіша частина роботи: вона показує, чому типи самі не захищають від неправильних даних. Візьмемо відкритий API без ключа `https://jsonplaceholder.typicode.com/todos/1`, який повертає `{ userId, id, title, completed }`.

1. Опишіть інтерфейс `TodoDto` за документацією.
2. Напишіть захисник типу з `unknown`:

   ```ts
   export function isTodoDto(value: unknown): value is TodoDto {
     if (typeof value !== 'object' || value === null) return false;
     const v = value as Record<string, unknown>;
     return typeof v.userId === 'number'
       && typeof v.id === 'number'
       && typeof v.title === 'string'
       && typeof v.completed === 'boolean';
   }
   ```

3. Напишіть `async function fetchTodo(id: number): Promise<TodoDto>`, яка викликає `fetch`, перевіряє `response.ok`, розбирає JSON як `unknown` (а не `any`), викликає `isTodoDto` і кидає зрозумілу помилку, якщо форма неправильна.
4. Перетворіть DTO на доменний тип `Task` окремою функцією `todoToTask`.
5. Перевірте функцію на трьох вхідних наборах: правильному, з відсутнім полем і з неправильним типом поля (підставте об'єкти напряму у `isTodoDto`, мережа для цього не потрібна).
6. У звіті поясніть: що було б, якби ви написали `const data = (await res.json()) as TodoDto` без перевірки, і коли ви дізналися б про помилку.

## Завдання підвищеної складності

> [!example] Рівень «добре» (необов'язково)
> Замініть ручну перевірку на схему бібліотеки **Zod**: `const TodoSchema = z.object({...})`, `type TodoDto = z.infer<typeof TodoSchema>`. Порівняйте обсяг коду й зручність: тип виводиться зі схеми, а не пишеться окремо.

> [!example] Рівень «відмінно» (необов'язково)
> Напишіть типізовану функцію `get<T>(url: string, schema: (v: unknown) => v is T): Promise<T>` і функцію `pick<T, K extends keyof T>(obj: T, ...keys: K[]): Pick<T, K>`. Додайте умовний тип `Unwrap<T> = T extends Promise<infer U> ? U : T` та перевірте його на `Promise<number>` і `string`.

## Вимоги до звіту

1. Титульна сторінка.
2. Лістинг `types.ts`, `generic.ts`, `status.ts` та пояснення, чому саме так описано `NewTask`.
3. Таблиця п'яти навмисних помилок з повідомленнями компілятора.
4. Результат експерименту «`strict` вимкнено»: кількість помилок до і після.
5. Опис роботи захисника типу та результат трьох тестових вхідних наборів.
6. Висновки: що TypeScript гарантує, а що ні.

## Критерії оцінювання

| Критерій | Балів |
|---|---|
| Налаштований `tsconfig` зі `strict`, `npm run check` без помилок | 15 |
| Інтерфейси, об'єднання, допоміжні типи (`Pick`, `Omit`, `Partial`) | 20 |
| Узагальнені функції з обмеженням параметра типу | 20 |
| Вичерпна обробка через `never`, експеримент з новим варіантом | 10 |
| Захисник типу для даних з мережі | 15 |
| Таблиця навмисних помилок і звіт | 10 |
| Захист | 10 |
| **Разом** | **100** |

## Контрольні питання

> [!question] Перевірте себе
> 1. Чи впливає типізація на швидкість виконання готового коду?
> 2. Чим інтерфейс відрізняється від псевдоніма типу?
> 3. Що таке звуження типу?
> 4. Навіщо потрібні узагальнення?
> 5. Які перевірки вмикає режим `strict`?
> 6. Чому `unknown` безпечніший за `any`?
> 7. Що таке дискримінантне об'єднання і навіщо тип `never` у гілці `default`?
> 8. Чому `as` не перевіряє дані під час виконання?

> [!exam] На іспиті
> Класичне питання: «Тип `Task` описано, але сервер повернув `{ id: "7" }`. Чому TypeScript не попередив?» Відповідь: типи зникають після компіляції, перевірка під час виконання потрібна на межі системи.

## Далі

Типізовані інтерфейси ви застосуєте в [[courses/web-programming/labs/04-stvorennia-komponentnoho-interfeisu-na-react|наступній лабораторній]]: пропси React-компонента описуються тими самими інтерфейсами. Теорія – [[courses/web-programming/lectures/04-typescript-dlia-vebrozrobky|лекція 4]]; про формат відповідей зовнішніх сервісів – [[courses/web-programming/glossary/rest|REST]].

## Література

- TypeScript Handbook (typescriptlang.org/docs)
- Basarat Ali Syed, TypeScript Deep Dive (basarat.gitbook.io/typescript)
- Matt Pocock, Total TypeScript (totaltypescript.com), безкоштовні вправи
