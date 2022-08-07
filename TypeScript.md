- [О TypeScript](#о-typescript)
- [Типы данных (Data Types)](#типы-данных)
- [Класс (Class)](#класс-class)
- [Интерфейс (Interface)](#интерфейс-interface)
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

# Поддержка ООП в TypeScriot

TypeScript предоставляет полноценную поддержку классов из ООП, JavaScript предоставляет лишь частичную поддержку.

# Класс (Class)

**Классом** (англ. `class`) называют конструктор (строитель, генератор, создатель) объектов. В теле класса содержится вся информация, которую будет содержать объект после создания.

## Объявление класса
*Имена классам* даются с *большой буквы*.
```ts
/* объявление двух классов Bird и Person */
class Bird {}
class Person {}
```
## Создание объектов
Для создания объекта через класс или функцию-конструктор используется оператор `new`. 
```ts
/* создание двух объектов при помощи класса Bird */
const dove = new Bird();
const magpie = new Bird();
```

## Задание свойств в теле класса
```ts
/* задание свойств `name` и `age` в теле класса Person */
class Person {
  name: string = "He/She"; 
  age: number = 0;
}
const p = new Person(); // { name: "He/She", age: 0 }
console.log(p); // { name: "He/She", age: 0 }
```
Если *не задать тип* и *значение свойству*, то будет *выдано предупреждение*:
```ts
/* задание свойств `name` и `age` в теле класса Person */
class Person {
  name: string = "He/She";
  age; // TS: Member 'name' implicitly has an 'any' type.
}
const p = new Person();
console.log(p); // { name: "He/She" }
```
<!-- Как видно на примере выше, свойство `age` не задано, поэтому оно не отображается в объекте `p`.
Но если задать значение `undefined` явно, то свойство попадёт в объект

со значением `undefined` не отображается в объекте -->

## Передача параметров через конструктор

Класс может принимать параметры и использовать их в качестве аргументов внутри конструктора при создании объекта. Как и параметры функции, параметры класса могут быть обязательными и не обязательными (объявляются с `?`).
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
console.log(max); // { name: "Max", age: 0 }

/* инициализация класса с передачей обязательного и необязательного параметров */
const dan = new Person("Dan", 21);
console.log(dan); // { name: "Dan", age: 21 }
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
## Методы класса


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
