
## Интерфейсы

```ts
interface IAuthor {
  id: string;
  username: string;
}

interface IArticle {
  id: string;
  title: string;
  description?: string;
  getAuthor: () => Author;
}
```

## Типы данных

### Примитивные типы
- `boolean` — **логическое значение** (`true`, `false`).
- `number` — **числовое значение** (`-1`, `3.9`).
- `string` — **строковое значение** (`"notes"`, `'123'`).
- `null` — *специальное значение* **null**.
- `undefined` — *специальное значение* **undefined**.
- `symbol` — **символ**.
```ts
const symbol = Symbol('key');
const obj = {
    [symbol]: 'value'
};
console.log(obj[sym]); // "value"
console.log(Symbol('key') === symbol); // false
console.log(obj[Symbol('key')]) // undefined
```
- `any` — **произвольный тип** (ставится по умолчанию).
```ts
let foo: any;
foo = 1;
foo = ''; // ошибки не будет
```
- `void` — **отсутствие конкретного значения** (обычно возвращаемый тип функции).
```ts
const fn = (param: string): void => {
  console.log(param);
  // return отсутствует
}
```
- `never` — **значение**, которое **никогда не наступит** (обычно функции, возвращающие ошибку)
```ts
const createError = (message: string): never {
  throw new Error(message);
};
```
- `Array` — **массив** (`number[]`).
```ts
let foo: number[];
foo = [1, 2, 3];
let bar: Array<string>;
bar = ['n', 'o', 't', 'e', 's'];
```
- `Typle` — **кортеж** (`[string, number]`).
```ts
let foo: [string, number, boolean];
foo = ['notes', 17, true];
/* порядок важен */
foo = [17, 'notes', true]; // error
```
- `Enum` — **перечисление** (более дружелюбные имена для множества числовых значений).
```ts
enum Visibility { Visible, Hidden }
const state: Visibility = Visibility.Visible;
```

