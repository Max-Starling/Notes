- [О TypeScript](#о-typescript)
- [Типы данных (Data Types)](#типы-данных)
- [Класс (Class)](#класс)
- [Интерфейс (Interface)](#интерфейс)
- [Дженерики (Generics)](#generics)

# О TypeScript

**TypeScript** - это язык программирования, расширяющий возможности языка JavaScript. 

TypeScript привносит несколько полезных возможностей:
1) Наделяет JavaScript возможностью явного статического объявления типов данных.
2) Дополняет возможности JavaScript инструментами ООП-разработки (появляются интерфейсы, модификаторы доступа, дженерики и так далее), таким образом TypeScript может считаться полноценным ООП-языком, позволяющим реализовывать большинство теоритических практик парадигмы, а значит может послужить альтернативой некоторым другим традиционным объектно-ориентированным языкам.
3) В отличии от JavaScript - TypeScript имеет свою гибко настраиваемую систему модулей, которая использует технологию ES6-модули (`import`, `export`). Заметим, что сам JavaScript вообще не имеет модульной системы. Нативный JavaScript может импортируется только в тэгах `<script>`, вставленных в HTML. При этом в NodeJS встроена более старах модульная система CommonJS-модулей (`require`, `module.export`), поддержка ES6-модулей начала появляться лишь недавно и только для последних версий.

**TypeScript** обратно совместим с JavaScript, поскольку TypeScript компилируется в JavaScript перед запуском программы. Таким образом, компилятор запускает TypeScript, производится статическая проверка типов, выдаются ошибки приведения типов, а если ошибок нет, то происходит компиляция в JavaScript и далее работа происходит обычный запуск скрипта на языке JavaScript.

## Разница между TypeScript и JavaScript

TypeScript - компилируемый язык, который содержит все позможности языка JavaScript и расширяет их, а затем компилируется обратно в JavaScript.

JavaScript имеет неявную динамическую слабую типизацию. TypeScript позволяет делать её явной и статической в тех местах приложения, где это нужно. По умолчанию все переменные наделяются типом `any`, который позволяет иметь им любое значение. Таким образом, наличие TypeScript не обязывает разработчика указывать типы везде и всегда - он сам выбирает, где это нужно и насколько сильно. Также это позволяет плавно переписать любой JavaScript проект на TypeScript и не иметь 10000 ошибок в консоли при его подключении.


## Типы данных

- [Примитивные типы данных](#примитивные-типы-данных)

### Примитивные типы данных
Поскольку TypeScript добавляет лишь инструменты типизации и после всех проверок компилируется в JavaScript, он не может вносить то, что нельзя было бы перевести в JavaScript.

Примитивные типы совпадают с теми, которые есть в JavaScript. 

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
- `Tuple` — **кортеж** (`[string, number]`).
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
- `Object` — любой **JavaScript-объект** (соответствует интерфейсу `Object`, включающему в себя `toString()`, `valueOf()`, `hasOwnProperty()` и другие методы).
```ts
let obj: Object;
obj = {};
```
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

## Класс

## Интерфейс

**Интерфейс** (Interface) является абстрактным описанием того, что должен включать в себя объект, но не содержит никакой реализации. 

*Интерфейс* в *TypeScript* является *виртуальной структурой*: он *существует только* в *контексте языка*. Компилятор при помощи интерфейсов и прочих способов типизации проводит проверку типов, а затем переводит код в JavaScript, куда интерфейсы не попадают.

Интерфейсы, как и классы, именуют с большой буквы. Часто можно встретить заглавную `I` в начале, чтобы разрешить конфликт имён классов и интерфейсов.

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
  /* может квакать, значит утка */
  quack(): void {
    console.log('quack!');
  },
};
```

### Абстрактные классы и интерфейсы

**Абстрактный класс** (Abstract class) содержит некоторые *абстрактные методы*, которые *должны быть реализованы его наследниками*. *Помимо абстрактных методов*, в нём могут также содержаться и *обычные методы*. Они характеризуют поведение по умолчанию и их реализовывать не обязательно.
```ts
abstract class Duck {
  abstract eat(): void; // утки в разных странах могут есть разную еду
  makeSound(): void {
    console.log('quack!'); // но все они издают похожий звук
  }
}

class Mallard extends Duck {
  eat() { /* ... */ }
  // дикая утка наследует метод quack от абстрактного родительского класса
}
```

*Интерфейс*, в отличие от любого класса, вообще *не может содержать реализации*. Поэтому от него *не наследуют* (extends), а его *реализуют* (implements).
```ts
interface IDuck {
  eat(): void;
  makeSound(): void;
}

class Mallard implements IDuck {
  eat() { /* ... */ }
  makeSound() { /* ... */ }
};
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

Такая возможность связана с тем, что в TypeScript (как и в JavaScript) можно создать объект без класса.
```ts
class Human {
  sex: string;
  constructor(sex: string) {
    this.sex = sex;
  }

  run():void {
    console.log('run');
  }
}
/* объекты human1 и human2 реализуют один и тот же интерфейс */
const human1 = new Human('male');
const human2 = {
  sex: 'male',
  run():void {
    console.log('run');
  }
};
```
В этом и есть смысл: можно взять интерфейс класса и использовать его.
```ts
interface IHuman extends Human {}

let human3: IHuman;
human3 = { ...human2 };
```
Более того, мы можем расширить этот интерфейс. И таким образом заменить наследование композицией (*Composite Reuse Principle*), реализуя расширенный интерфейс вместо переопределения методов родительского класса.
```ts
class Bird { /* ... */ };

interface IFlyingBird extends Bird {
  fly: () => void;
}

class FlyingBird implements IFlyingBird { /* ... */ }
```
Можно также найти применение наследованию интерфейса от класса при использовании Generics.
```ts
class Translator<From, To> { /* ... */ }
interface EngRusTranslator extends Translator<Russian, English> {}

const translate = (translator: EngRusTranslator) => { /* ... */ }
```

Интерфейсы наследуют всё, включая приватные и защищённые члены базового класса.

Если базовый класс содержит приватные или защищённые свойства и методы, то наследующий от него интерфейс может быть реализован только базовым классом или его наследником.
