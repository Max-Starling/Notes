- [О TypeScript](#о-typescript)
- [Типы данных (Data Types)](#типы-данных)
- [Класс (Class)](#класс-class)
- [Интерфейс (Interface)](#интерфейс-interface)
- [Тип и интерфейс(Type vs interface)](#тип-и-интерфейс-type-vs-interface)
- [Дженерики (Generics)](#дженерики-generics)

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
const throwError = (message: string): never {
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
const state: Visibility = Visibility.Visible; // 0
```

### `object` vs `Object` vs `{}` vs `Record<K, V>`
- `object` — **непримитивный тип** (non-primitive); *любой тип*, *кроме [примитивных](#примитивные-типы)*.
```ts
let foo: object;
foo = { prop: 'value' };
foo = ['value'];
foo = () => console.log('notes');
```
- `Object` — любой **JavaScript-объект** (соответствует интерфейсу `Object`, имеющий методы `toString()`, `valueOf()`, `hasOwnProperty()` и другие).
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

Фактически, `object` и `Object` очень похожи, поскольку все непримитивные типы в JavaScript происходят от объектов, так в чём же отличие?

По сути, разница лишь в хайлайтинге:
```ts
const foo: Object = function (){}; // ок
const bar: Object = function (){}; // ок

foo. // покажет доступные методы
bar. // не покажет ничего

// но при этом
foo.toString() // нет ошибки
bar.toString() // нет ошибки
```
Проведя больше тестов, я выяснил, что разница в поведении между ними касается лишь типа `Symbol`.
```ts
const func1: Object = function (){}; // ок
const func2: Object = function (){}; // ок
func1() // ошибка TS: `This expression is not callable. Type 'Object' has no call signatures`
func2() // ошибка TS: `This expression is not callable. Type '{}' has no call signatures`

const arr1: Object = []; // ок
const arr2: object = []; // ок
arr1.fill(2); // `Property 'fill' does not exist on type 'Object'`
arr2.fill(2); // `Property 'fill' does not exist on type 'object'`

const symbol1: Object = Symbol(''); // ок
const symbol2: object = Symbol(''); // ошибка TS: `Type 'typeof symbol2' is not assignable to type 'object'`

const boolean1: Object = new Boolean(true); // ок
const boolean2: object = new Boolean(true); // ок

const promise1: Object = Promise.resolve(true); // ок
const promise2: object = Promise.resolve(true); // ок
promise1.then(() => {}); // `Property 'then' does not exist on type 'Object'`
promise2.then(() => {}); // `Property 'then' does not exist on type 'object'`
```


- `{}` — **пустой тип**, **пустой объект**.
Обращение к его свойствам приведёт к ошибке, но остаётся возможность использовать все методы `Object`.
```ts
const foo = {};
if (true) {
  foo.a = 'notes'; // Error: Property 'a' does not exist on type '{}'
}
console.log(foo.toString()); // '[object Object]'
```

Стоит отметить, что тип `{}` задаётся автоматически, если присвоить пустой объект на этапе создания переменной вне зависимости от того, это `let` или `const`.
```typescript
const a = {};
a.foo = 1; // ошибка TS `Property 'foo' does not exist on type '{}'`
let b = {};
b.bar = 2; // ошибка TS `Property 'bar' does not exist on type '{}'`

// можно задать тип явно
const a: {} = {}

// можно сбить пользователя с толку
const env: {} = { // ошибки при объявлении нет
  OS: 'win32',
}
env.OS /* ошибка TS: `Property 'OS' does not exist on type '{}'`,
нет подсказок, какие свойства есть в объекте, но при этом значение `win32` возвращается
*/

// попытка обмануть компилятор тоже ни к чему не приведёт
env['OS'] /* ошибка TS: `Element implicitly has an 'any' type because expression of type '"OS"' can't be used to index type '{}'.
Property 'OS' does not exist on type '{}'.` */

// единсвенный способ избежать ошибки, отключить проверку следующей строки при помощи директивы `@ts-ignore`
// @ts-ignore
env.OS // 'win32' без ошибки TS

// тот же самый трюк с функцией, возвращающей `{}`
const getUserData = (): {} => ({ email: '17.max.starling@gmail.com' });
const user = getUserData();
user.email; /* вернёт '17.max.starling@gmail.com', но подсказки о том, что свойство `email` сущесвует, не будет,
а также будет ошибка TS `Property 'email' does not exist on type '{}'` */
```


### Детальный разбор `void`, сравнение с типом `undefined`

```typescript
function a(): void {} // без ошибок
function b(): void {
    return undefined; // всё ещё без ошибок (!)
}
function c(): void {
    return (-1 + 1); // ошибка: `Type 'number' is not assignable to type 'void'`
}
function d(): void {
    return void (-1 + 1); // нет ошибки, поскольку оператор `void` выполняет выражение, но возвращает `undefined
}
```
Не путайте тип `void` из TypeScript и оператор `void` из JavaScript :)

Стоит отметить, что тип `void` на первый взгляд не сильно отличается от `undefined` в плане ошибок TS.
```ts
void function a(): undefined {} // ок
function a(): undefined {} // ок
function b(): undefined { return undefined; } // ок
function с(): undefined { return void true; } // ок
```

Так в чём же разница?

Разница есть, но она больше семантическая. Ещё раз, `void` означает, что значение не будет возвращено, а `undefined` означает, что будет возвращён `undefined`.
Это важно в опреденеии некоторых функций, например, `Array.prototype.forEach`:
```ts
declare function forEach<T>(array: T[], callback: (item: T) => undefined): void;
let numbers: number[] = [];
forEach([0, 1, 2], item => numbers.push(item)); // ошибка `Type 'number' is not assignable to type 'undefined'`
```
Поскольку `Array.prototype.push` возвращает число, получаем ошибку, поскольку ожидался `undefined`.
Но если использовать `void`, то такой проблемы не будет, поскольку мы обещаем, что в реализации функции `forEach` не будет использовано возвращаемое значение callback-а.

Таким образом, судя по примеру выше, нельзя с точностью утверждать, что функция, которая возвращает `void`, действительно возвращает `undefined` - возвращаемое знаение может быть любым `any`.

```ts
declare function forEach<T>(array: T[], callback: (item: T) => void): void;
let numbers: number[] = [];
forEach([0, 1, 2], item => numbers.push(item)); // ок
```


### Детальные разбор `never`
```typescript
function a(): never {
    throw new Error();
}

function b(): never {
    if (Math.random() > 0.5) {
         throw new Error(); // ошибка `A function returning 'never' cannot have a reachable end point.`
    }
}

function b(): never | void {
    if (Math.random() > 0.5) {
         throw new Error(); // нет ошибки
    }
}
```

### Детальный разбор `enum`
Перечисления - это один из немногих типов в TypeScript, который имеет представление в JavaScript в виде объекта, который можно использовать в ходе выполнения программы.

```ts
enum Visibility { Visible, Hidden }

console.log(Visibility);
/* {
  "0": "Visible",
  "1": "Hidden",
  "Visible": 0,
  "Hidden": 1
}  */
console.log(Visibility[0]) // "Visible"
console.log(Visibility.Hidden) // 1 
```
Получили объект, в котором строки соответствуют числовым индексам и наоборот.

Выше показано, что происходит со значениями перечислений по умолчанию, теперь зададим значения явно.

```typescript
enum Color {
    RED = 'red',
    YELLOW = 'yellow',
    GREEN = 'green'
}
console.log(Color);
/* {
  "RED": "red",
  "YELLOW": "yellow",
  "GREEN": "green"
}  */

console.log(Color.YELLOW) // 'yellow'
console.log(Color['yellow']) // `undefined` и ошибка: `Property 'yellow' does not exist on type 'typeof Color'. Did you mean 'YELLOW'?`
```

Перечислениями также можно манипулировать при помощи `Object`-методов. Это может быть полезно при переборе всех допустимых значений, например, при написании валидации.
```ts
Object.values(Color) // ["red", "yellow", "green"]
Object.keys(Color) // ["RED", "YELLOW", "GREEN"]
Object.entries(Color) // [["RED", "red"], ["YELLOW", "yellow"], ["GREEN", "green"]] 
```

# Класс (Class)


<!-- 
# Поддержка ООП в TypeScriot

TypeScript предоставляет полноценную поддержку классов из ООП, JavaScript предоставляет лишь частичную поддержку. -->

**Классом** (англ. `class`) называют конструктор (строитель, генератор, создатель) объектов. В теле класса содержится вся информация, которую будет содержать объект после создания.

- [Объявление класса](#объявление-класса)
- [Создание объектов с помощью класса](#создание-объектов-с-помощью-класса)
- [Задание свойств в теле класса](#задание-свойств-в-теле-класса)
- [Передача параметров класса через конструктор и ключевое слово `this`](#передача-параметров-класса-через-конструктор-и-ключевое-слово-this)
- [Валидация параметров класса](#валидация-параметров-класса)
- [Задание методов класса](#задание-методов-класса)
- [Потеря и привязка контекста `this` в классе](#потеря-и-привязка-контекста-this-в-классе)

## Объявление класса
*Имена классам* даются с *большой буквы*.
```ts
/* объявление двух классов Bird и Person */
class Bird {}
class Person {}
```
## Создание объектов с помощью класса
Для создания объекта через класс или функцию-конструктор используется оператор `new`. 
```ts
/* создание двух объектов при помощи класса Bird */
const dove = new Bird();
const magpie = new Bird();
/* создание объекта при помощи класса Person */
const guest = new Person();
```

## Задание свойств в теле класса
```ts
/* задание свойств `name` и `age` в теле класса Person */
class Person {
  name: string = "He/She"; 
  age: number = 0;
}
const p = new Person();
console.log(p); // Person { name: "He/She", age: 0 }
```
Если *не задать тип* и *значение свойству*, то будет *выдано предупреждение*:
```ts
/* задание свойств `name` и `age` в теле класса Person */
class Person {
  name: string = "He/She";
  age; // TS: Member 'name' implicitly has an 'any' type.
}
const p = new Person();
console.log(p); // Person { name: "He/She" }
```
<!-- Как видно на примере выше, свойство `age` не задано, поэтому оно не отображается в объекте `p`.
Но если задать значение `undefined` явно, то свойство попадёт в объект

со значением `undefined` не отображается в объекте -->

## Передача параметров класса через конструктор и ключевое слово `this`

Класс может принимать параметры и использовать их в качестве аргументов внутри конструктора при создании объекта. Как и параметры функции, параметры класса могут быть обязательными и не обязательными (объявляются с `?`).

Чтобы *присвоить значение полю класса*, нужно использовать *ключевое слово* `this`, которое представляет собой *контекст*, то есть *всё, что касается создания или использования текущего объекта*.

```ts
/* задание свойств `name` и `age` в теле класса Person */
class Person {
  name: string;
  age: number;
  constructor(name: string, age?: number) {
    this.name = name;
    this.age = age || 0;
  }
}
/* в примере ниже будет ошибка, так как параметр `name` ялвяется обязательным
const someone = new Person();  // TS: Expected 1-2 arguments, but got 0. An argument for 'name' was not provided.

/* инициализация класса с передачей обязательного параметра */
const max = new Person("Max");
console.log(max); // Person { name: "Max", age: 0 }

/* инициализация класса с передачей обязательного и необязательного параметров */
const dan = new Person("Dan", 21);
console.log(dan); // Person { name: "Dan", age: 21 }
```
## Валидация параметров класса

Можно *налагать некоторые условия* на *параметры класса* и *проверять выполнение* этих *условий при *создании объекта* в *конструкторе*, то есть *валидировать параметры класса*. При *несоблюдении заданных условий* будет *выдаваться ошибка*.
```ts
/* задание свойств `name` и `age` в теле класса Person */
class Person {
  name: string;
  age: number;
  constructor(name: string, age: number) {
    /* валидация по параметру `name` */
    if (name.length < 2) {
      throw new Error("Name must be at least 2 characters long");
    }
    /* валидация по параметру `age` */
    if (age < 18) {
      throw new Error("Person must be 18 years of age or older");
    }
    this.name = name;
    this.age = age || 0;
  }
}
/* в примере ниже будет ошибка из-за непрошедшего валидацию поля `name` */
const g = new Person("G", 27);  // [ERR]: Name must be at least 2 characters long
/* в примере ниже будет ошибка из-за непрошедшего валидацию поля `age` */
const yo = new Person("Yo", 10);  // [ERR]: Person must be 18 years of age or older
/* в примере ниже ошибок нет */
const ns = new Person("NS", 39);
```
## Задание методов класса

Метод в классе можно объявить тремя способами, которые практически ничем не отличаются.
```ts
class Animal {
  name: string;
  constructor(name?: string) {
    this.name = name || "It";
  }

  /* объявление метода как свойства, значением которого является функция `function` (`Function Expression`) */
  walk = function() {
    console.log(this);
  }

  /* объявление метода как свойства, значением которого является стрелочная функция (`Arrow Function Expression`) */
  fly = () => {
    console.log(this);
  }
  
  /* объявление метода как метода класса */
  swim() {
    console.log(this);
  }
}
const duck = new Animal("Duck");
duck.swim(); // Animal { name: "Duck" }
duck.walk(); // Animal { name: "Duck" }
duck.fly(); // Animal { name: "Duck" }
```
Как видно на примере выше, если использовать обращение к методу через `.` и его вызов `()` в одном выражении, то все три объявления работают одинаково.

## Потеря и привязка контекста `this` в классе
Попробуем теперь *присвоить каждый метод класса* в *отдельные переменные* и *вызвать получившиеся функции* спустя *некоторое время*.
Чаще всего *такое необходимо* при *передаче метода класса* как *фунцкии обратного вызова* (`callback`) куда-либо. 
```ts
class Animal {
  name: string;
  constructor(name?: string) {
    this.name = name || "It";
  }
  walk = function() {
    console.log(this);
  }
  fly = () => {
    console.log(this);
  }
  swim() {
    console.log(this);
  }
}
const duck = new Animal("Duck");

/* потеря контекста */
const swim = duck.swim;
swim(); // undefined

/* контекст не потерялся */
const walk = duck.walk;
walk(); // Animal { name: "Duck" }

/* контекст утерян */
const fly = duck.fly;
fly(); // undefined
```
В данном примере *разрывается связь классом* и его *методом*, связь между `.` и `()`, что *приводит* к *потере контекста*. 

Из примера выше видно, что *потеряли контекст методы* `swim()` и `walk = function() {}`, поскольку *нестрелочные функции* могут иметь *свой контекст*. 

В то же время *стрелочная функция не может иметь контекст* и *присваивать его* ей тоже *нельзя*. Вместо этого *контекст задаётся ей в момент объявления* с *уровня выше*. В данном случае для `fly = () => {}` *контекст* взят из класса `Animal`, поэтому её *связь с классом будет сохраняться в любом случае*.

Сущесвует *несколько способов решения проблемы*
1) *Никогда не разрывать связь между обращением к методу* класса через `.` и его *вызовом* через `()` при присвоении в другую переменную.
```ts
const duck = new Animal("Duck");

const swim = () => duck.swim();
const walk = () => duck.walk();
const fly = () => duck.fly();

swim(); // Animal { name: "Duck" }
walk(); // Animal { name: "Duck" }
fly(); // // Animal { name: "Duck" }
```
2) *Явно привязать контекст* в *конструкторе класса* через `bind`
```ts
class Animal {
  name: string;
  constructor(name?: string) {
    this.name = name || "It";
    /* явная привязка контекста через `bind` */
    this.walk = this.walk.bind(this);
    this.swim = this.swim.bind(this);
  }
  walk = function() {
    console.log(this);
  }
  fly = () => {
    console.log(this);
  }
  swim() {
    console.log(this);
  }
}
const duck = new Animal("Duck");

const swim = duck.swim;
const walk = duck.walk;
const fly = duck.fly;

swim(); // Animal { name: "Duck" }
walk(); // Animal { name: "Duck" }
fly(); // Animal { name: "Duck" }
```
3) Всегда *использовать только стрелочные функции* в *качестве методов класса* в тех случаях, когда *есть риск потери контекста*.

Доказательство того, что *стрелочной функции нельзя привязать контекст*:
```ts
class Animal {
  name: string;
  constructor(name?: string) {
    this.name = name || "It";
    /* явная привязка контекста пустого объекта через `bind` всем трём методам */
    this.walk = this.walk.bind({});
    this.swim = this.swim.bind({});
    this.fly = this.fly.bind({});
  }
  walk = function() {
    console.log(this);
  }
  fly = () => {
    console.log(this);
  }
  swim() {
    console.log(this);
  }
}
const duck = new Animal("Duck");

const swim = duck.swim;
const walk = duck.walk;
const fly = duck.fly;

swim(); // {}
walk(); // {}
fly(); // Animal { name: "Duck" }
```
 

## Интерфейс (Interface)

**Интерфейс** (`Interface`) является абстрактным описанием того, что должен включать в себя объект, но *не содержит* никакой *реализации*, то есть *не содержит методов и полей свойств* - *только используемые* в них *типы*.

Сравните
```ts
class Person {
    name: string = "He/she"; 
    eat(food: string): void {
        console.log(this.name, "eats", this.food);
    }
}
```
```ts
interface IPerson {
    name: string;
    eat(food: string): void;
}
```

То есть интерфейс представляет собой лишь схему 

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

# Тип и интерфейс(Type vs interface)

И тип (Type), и интерфейс (Interface) описывают объекты, так что в простых случаях будут работать одинаково.
```ts
type User = { id: string; name: string }
```
```ts
interface User { id: string; name: string }
```

Различия:
* Интерфейсы поддерживают наследование (англ. `inheretance`) через `extends`, позволяющее создавать новый тип, дополняя уже существующий:
```ts
interface Customer extends User { company: string }
```
* Типы поддерживают пересечение (англ. `intersection`) с помощью `&` (`AND`), позволяющее комбинировать несколько типов в один, создавая новый тип:
```ts
type Customer = User & { company: string }
```
* Типы также поддерживают объединение (англ. `union`) с помощью `|` (`OR`):
```ts
type A = { foo: string }
type B = { bar: string }
type C = A | B // либо тип А, либо тип Б
```
* Интерфейсы поддерживают **слияние деклараций** (англ. `Declaration Merging`), типы - не поддерживают (будет ошибка):
```ts
interface User { id: string }
interface User { name: string }
const user: User = {
  id: 1,
  name: "John",
};
```
```ts
type User = { id: string }
type User = { name: string } // ❌ Error: Duplicate identifier 'User'
```
* Типы могут работать с примитивами, интерфейсы - не могут:
```ts
type Pet = 'cat' | 'dog'
type Pets = `{Pet}s` // 'cats' | 'dogs'
type ID = string | number
```
* Интерфейсы подходят для имплементации классов (англ. `implementation`) через `implements`:
```ts
interface Person {
  name: string;
  greet(): void;
}

class User implements Person {
  name: string;
  greet() { console.log('Hi!'); }
}
```

# Дженерики (Generics)
<!-- 
/*
- Допустим, у нас в системе есть пользователи, уникальные идентификаторы
которых являются числами
*/

{
  type Person = {
    id: number
    name: string
  }

  const max: Person = {
    id: 123,
    name: 'Max'
  }
}

/*
- В какой-то момент времени появляется требование использовать строковые значения
вместо числовых, но при этом старых пользователей изменить уже нельзя.

Этого можно достигнуть, создав ещё один тип и объявлять эти типы через "или"
(либо NumberPerson, либо StringPerson).
*/
{
  type NumberPerson = {
    id: number
    name: string
  }

  type StringPerson = {
    id: string
    name: string
  }

  const max: NumberPerson | StringPerson = {
    id: 123,
    name: 'Max',
  }

  const nastya: NumberPerson | StringPerson = {
    id: 'fafafa',
    name: 'Nastya',
  }
}


/* Таким образом мы имеем два типа, которые отличаются всего лишь
типом одного параметра. Это может работать, но получается много лишнего кода.
Представь, что каждый из типов содержал бы 100 строк кода и притом различия
были бы по-прежнему в одном параметре.

Чтобы не дублировать код и сделать возможным полиморфизм
(то есть обобщение нескольких типов в один)
придумали Generics. 
*/

{
  // В <> задаём параметр, который можем затем использовать при указании типа свойствам
  type GenericPerson<ID_TYPE> ={
    id: ID_TYPE
    name: string
  }

  const max: GenericPerson<number> = {
    id: 123,
    name: 'Max',
  }

  const nastya: GenericPerson<string> = {
    id: 'fafafa',
    name: 'Nastya',
  }
}

/* Это то, как Generics работают с объектами.
Посмотрим теперь на функции. */

/* просто перенос кода из примера выше, чтобы потом не дублировать */
type GenericPerson<ID_TYPE> = {
  id: ID_TYPE
  name: string
}

const nastya: GenericPerson<string> = {
  id: 'fafafa',
  name: 'Nastya',
}

const max: GenericPerson<number> = {
  id: 123,
  name: 'Max',
}

{
  const kissPerson = function <PERSON_ID_TYPE>(person: GenericPerson<PERSON_ID_TYPE>) {
    console.log('kiss', person.name);
  }

  // Настя у нас со строковым айди, поэтому мы передаём строку
  kissPerson<string>(nastya);

  // а вот Максим как старый пользователь с числовым айди, поэтому мы передаём число
  kissPerson<number>(max);
}

/* Если бы мы задали тип PERSON_ID_TYPE в параметрах функции напрямую, то поцеловать на ночь можно было
бы лишь один тип людей - либо строковых, либо числовых, поэтому параметр и выносится на уровень выше,
то есть на уровень вызова функции. Пример: */
{
  const kissPerson = function (person: GenericPerson<string>) {
    console.log('kiss', person.name);
  }
  // Настю поцеловать можно
  kissPerson(nastya);
  // а Максима нельзя
  kissPerson(max);
}

/* Но я всё же люблю хэппиэнды, поэтому покажу, как решить эту проблему */
{
  const kissPerson = function (person: GenericPerson<string | number>) {
    console.log('kiss', person.name);
  }
  // Настю поцеловать можно
  kissPerson(nastya);
  // и Максима можно тоже
  kissPerson(max);
}
/* а если вообще без Generics, то (person: NumberPerson | StringPerson) */

/* Рассмотрим пример c двумя разными параметрами на примере функции чата между двумя людьми */
{
  const makeChat = function <TYPE_A, TYPE_B>(personA: GenericPerson<TYPE_A>, personB: GenericPerson<TYPE_B>) {
    console.log(`${personA.name}: - Привет, ${personB.name}!`);
    console.log(`${personB.name}: - И тебе привет, ${personA.name}!`);
  }
  // Настя пишет первой Максиму
  makeChat<string, number>(nastya, max);
  // Максим пишет первым Насте
  makeChat<number, string>(max, nastya);
}

/* Пример c использованием Generics в теле функции. Представим, что есть некая Лера.
И она хочет взять кого-то выпить с собой.
Допустим, что Лера хочет определить себя как личность уже в теле функции. */
{
  const leraGoDrinkWith = function <LERA_ID_TYPE, PERSON_ID_TYPE>(
    leraId: LERA_ID_TYPE,
    person: GenericPerson<PERSON_ID_TYPE>
  ) {
    const lera: GenericPerson<LERA_ID_TYPE> = {
      id: leraId,
      name: 'Lera',
    };

    console.log(`
    Девушка по имени ${lera.name} с id "${lera.id}" говорит:
      - Привет, ${person.name}! Пойдём со мной гулять.
    `);
    // console.log(`${personB.name}: - И тебе привет, ${personA.name}!`);
  }
  // Лера с id 27 зовёт Настю гулять
  leraGoDrinkWith<number, string>(27, nastya);
}

 -->

# Вернуть тип в зависимости от параметра
* Определяем типы:
```ts
type User = {
  name: string;
  age: number;
};

type Admin = {
  name: string;
  permissions: string[];
};
```
* Создаём маппинг
```ts
type RoleMap = {
  user: User;
  admin: Admin;
};
```
* Создаём функцию и вызываем
```ts
function getData<T extends keyof RoleMap>(role: T): RoleMap[T] {}
const user = getData("user"); // вернёт тип User
const admin = getData("admin"); // вернёт тип Admin
```
Ещё пример
```ts
type ResponseMap = {
  success: { status: "ok"; data: any };
  loading: { status: "loading" };
  error: { status: "error"; message: string };
};

function getResponse<T extends keyof ResponseMap>(status: T): ResponseMap[T] {}

const successResponse = getResponse("success"); // вернется тип { status: "ok"; data: any }
const errorResponse = getResponse("error");     // вернется тип { status: "error"; message: string }
```
