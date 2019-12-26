
## Классы

## Интерфейсы

**Интерфейс** (Interface) является абстрактным описанием того, что должен включать в себя объект. 


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

Использование интерфейсов похоже на утиную типизацию.
```ts
interface IDuck {
  quack(): void;
}

const obj: IDuck = {
  quack(): void {
    console.log('quack!');
  }
}
```


### Наследование интерфейса от класса

В TypeScript есть возможность наследовать интерфейс от класса. 
```ts
class Fish {
  private age(): string;
  swim(): void;
}
interface IFlyingFish extends Fish {
  fly: () => void;
};
```
Такая

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
### Остальные типы
- `Array` — **массив** (`number[]`).
```ts
let foo: number[];
foo = [1, 2, 3];
let bar: Array<string>;
bar = ['n', 'o', 't', 'e', 's'];
```
- `any` — **произвольный тип** (используется по умолчанию, если тип не указан).
```ts
let foo: any;
foo = 1;
foo = '';
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

### object vs Object vs {}
- `object` — **непримитивный тип** (non-primitive); *любой тип*, *кроме [примитивных](#примитивные-типы)*.
```ts
let foo: object;
foo = { prop: 'value' };
foo = ['value'];
foo = ():void => console.log('notes');
```
- `Object` — любой **JavaScript-объект** (интерфейс `Object` включает в себя `toString()`, `valueOf()`, `hasOwnProperty()` и другие методы.
```ts
interface Object {
  toString(): string;
  hasOwnProperty(v: string): boolean;
  /* ... */
}
```
- `{}` — **пустой тип**, **пустой объект**.
Обращение к его свойствам приведёт к ошибке, но остаётся возможность использовать все методы `Object`.
```ts
const foo = {};
foo.a = 'notes'; // Error: Property 'a' does not exist on type '{}'
console.log(foo.toString()); // '[object Object]'
```
