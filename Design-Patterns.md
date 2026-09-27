# Паттерны проектирования

*Заметка* о *типовых решениях локальных задач* в коде: как *удобно создавать объекты*, как *собирать из них более крупные структуры* и как *распределять обязанности* между ними. Чем паттерн проектирования отличается от *архитектурного стиля* и *архитектурного паттерна* — в *заметке про* [*архитектуру и проектирование*](./Architecture-Design.md#определения).

- [Откуда взялись паттерны](#откуда-взялись-паттерны)
- [Порождающие](#порождающие)
- [Структурные](#структурные)
- [Поведенческие](#поведенческие)
- [Похожие паттерны](#похожие-паттерны)

## Откуда взялись паттерны

Идея *языка паттернов* пришла в программирование из *строительной архитектуры*: архитектор *Кристофер Александер* описывал *повторяющиеся решения* в *планировке городов и зданий* (книга «A Pattern Language»). Программисты заметили, что и в коде *одни и те же задачи* раз за разом *решаются похожим образом*, и начали *записывать такие решения*: как решение называется, какую задачу решает, как устроено и чем за него приходится платить.

Самая известная книга о паттернах — «[Design Patterns: Elements of Reusable Object-Oriented Software](https://www.informit.com/store/design-patterns-elements-of-reusable-object-oriented-9780201633610)», вышедшая *в октябре 1994 года*. Её авторов — *Эриха Гамму*, *Ричарда Хелма*, *Ральфа Джонсона* и *Джона Влиссидеса* — прозвали **«Бандой четырёх»** (англ. `Gang of Four`, `GoF`), а *23 паттерна* из книги — *паттернами GoF*. В книге они разбиты на *три группы* ([*оглавление*](https://web.archive.org/web/2002/http://hillside.net/patterns/DPBook/Contents.html)):
* **Порождающие** (англ. `Creational`), *5 паттернов*: *Абстрактная фабрика*, *Строитель*, *Фабричный метод*, *Прототип*, *Одиночка*.
* **Структурные** (англ. `Structural`), *7 паттернов*: *Адаптер*, *Мост*, *Компоновщик*, *Декоратор*, *Фасад*, *Приспособленец*, *Заместитель*.
* **Поведенческие** (англ. `Behavioral`), *11 паттернов*: *Цепочка обязанностей*, *Команда*, *Интерпретатор*, *Итератор*, *Посредник*, *Хранитель*, *Наблюдатель*, *Состояние*, *Стратегия*, *Шаблонный метод*, *Посетитель*.

Примеры кода в книге написаны на *C++* и *Smalltalk*, но *сами решения от языка не зависят*.

Спустя годы авторы признавали, что *составили бы каталог иначе*. В [*интервью 2009 года*](https://www.informit.com/articles/article.aspx?p=1404056) Эрих Гамма рассказал о *черновике*, набросанном ещё *в 2005 году*: *сменить группировку*, чтобы *отделить важные паттерны от редких*, *обобщить Фабричный метод до Фабрики*, а в *порождающие паттерны* добавить *Внедрение зависимостей* — оно и в этой заметке стоит среди порождающих.

*Набор нужных паттернов зависит от языка*. В *1996 году* Питер Норвиг [*показал*](https://norvig.com/design-patterns/), что в *динамических языках* (Lisp, Dylan) *16 из 23 паттернов GoF* реализуются *качественно проще*, чем в *C++*, хотя бы для части случаев, а некоторые *становятся вовсе незаметны*. Например, *Команда*, *Стратегия*, *Шаблонный метод* и *Посетитель* *упрощаются благодаря функциям первого класса*. В *JavaScript* функции тоже [*объекты первого класса*](./FunctionalProgramming.md#объекты-и-функции-первого-класса), поэтому ниже *рядом с классами* встречаются и *решения на функциях*.

И главное: паттерн — *не цель*. Как сказано в [*определениях*](./Architecture-Design.md#архитектурный-паттерн), паттерны стоит применять *только при необходимости*, иначе они *лишь усложнят код*.

## Порождающие

**Порождающие паттерны** отвечают за *удобное и безопасное создание* *новых объектов* или *групп объектов*.

- [Одиночка](#одиночка) (англ. `Singleton`)
- [Внедрение зависимостей](#внедрение-зависимостей) (англ. `Dependency Injection`)
- [Фабричный метод](#фабричный-метод) (англ. `Factory Method`)
- [Абстрактная фабрика](#абстрактная-фабрика) (англ. `Abstract Factory`)
- [Строитель](#строитель) (англ. `Builder`)
- [Прототип](#прототип) (англ. `Prototype`)

### Одиночка

**Одиночка** (англ. `Singleton`) — порождающий паттерн, *гарантирующий существование только одного объекта* *определённого класса*. Одиночка позволяет *обратиться к этому объекту* *из любого места в приложении*.

*Жизненный пример*: *журнал посещений на проходной*. Сколько бы людей ни пришло, все *записываются в один и тот же журнал*, и *любой охранник* найдёт его *на месте*.

Часто *считается антипаттерном*, поскольку *нарушает модульность кода* (похож на глобальную переменную).

Так думает и *один из авторов книги GoF* Эрих Гамма: в [*интервью 2009 года*](https://www.informit.com/articles/article.aspx?p=1404056) он говорил, что *был бы за то, чтобы убрать Одиночку* из каталога, потому что его использование «*почти всегда — признак проблем в дизайне*» (англ. `almost always a design smell`).

Для реализации одиночки пишется *класс с приватным конструктором*; *статическим полем*, хранящим *экземпляр* (англ. `instance`) класса, и *статическим методом*, *создающим и сохраняющим экземпляр при первом обращении* и *возвращающим при последующих*.
```ts
class Singleton {
  private static instance: Singleton;
  private constructor() {}
  static getInstance(): Singleton {
    if (!Singleton.instance) {
      Singleton.instance = new Singleton();
    }
    return Singleton.instance;
  }
}

const singletonA = Singleton.getInstance();
const singletonB = Singleton.getInstance();
console.log(singletonA === singletonB); // true
```

В *JavaScript* роль одиночки часто играет *модуль*. Модуль [*выполняется только один раз*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules), поэтому *все*, кто его импортирует, получают *один и тот же объект*. В *Node.js* то же самое делает `require`: он [*кэширует модуль*](https://nodejs.org/api/modules.html#caching) после первой загрузки.
```js
/* config.js */
export const config = { apiUrl: 'https://api.example.com' };

/* a.js */
import { config } from './config.js';
config.apiUrl = 'https://test.example.com';

/* b.js */
import { config } from './config.js';
console.log(config.apiUrl); // https://test.example.com (если a.js загрузился раньше)
```
Недостатки при этом *те же*, что у классического одиночки: *общее изменяемое состояние* и *скрытая зависимость*, которую *сложно подменить в тестах*.

### Внедрение зависимостей

**Внедрение зависимостей** (англ. `Dependency Injection`) — порождающий паттерн, реализующий [*принцип инверсии управления*](./Architecture-Design.md#принцип-инверсии-управления-ioc) (англ. `IoC`). 

*Внедрение зависимостей* позволяет *полностью вынести создание объектов*, от которых *зависит некоторый класс*, *за пределы этого класса*, предоставляя эти объекты *другим путём*.

*Жизненный пример*: *электроприбор и розетка*. Прибор *не вырабатывает электричество сам*: его *подают извне*, и прибор можно *включить в любую сеть* с *подходящим напряжением*.

*Внедрение зависимостей* вводит следующие 3 понятия
* **Клиент** — класс, зависимый (англ. `dependent`) от *Сервиса*.
* **Сервис** — класс, предоставляющий свой объект *Клиенту*.
* **Инжектор** — класс, внедряющий объект *Сервиса* в *Клиент*.

Возьмём пример из раздела с [*принципом инверсии управления*](./Architecture-Design.md#принцип-инверсии-управления-ioc) и улучшим его при помощи [*принципа инверсии зависимостей*](./Architecture-Design.md#dip-принцип-инверсии-зависимостей) (англ. `DIP`), заменив классы на интерфейсы, где это возможно.
```ts
interface ISalary {
  value: number;
  currency: string;
}

interface IEmployee {
  salary: ISalary;
}

const SalaryFactory = (value: number, currency: string): ISalary => new Salary(value, currency);

class Employee implements IEmployee {
  salary: ISalary;

  constructor() {
    this.salary = SalaryFactory(500, '$');
  }

  getSalary() {
    return this.salary.toString();
  }
}

class Salary implements ISalary {
  value: number;
  currency: string;

  constructor(value: number, currency: string) {
    this.value = value;
    this.currency = currency;
  }

  toString() {
    return `${this.value}${this.currency}`;
  }
}
```
Паттерн "Фабрика" *отвечает за создание объекта* класса `Salary`, но *само создание* (пусть и при помощи фабрики) *всё ещё происходит в классе* `Employee`.

В данном примере класс `Employee` — *Клиент*, `Salary` — *Сервис*.  
Рассмотрим *3 способа*, как можно написать *Инжектор*, который назовём `EmployeeInjector`:
* *Внедрение в конструктор*
* *Внедрение свойства*
* *Внедрение метода*

**Внедрение в конструктор** (англ. `Constructor Injection`): *зависимость предоставляется через параметры конструктора* (`constructor(salary:  ISalary)`).
```ts
class Employee implements IEmployee {
  salary: ISalary;

  constructor(salary: ISalary) {
    this.salary = salary;
  }
}
```
Инжектор в этом случае:
```ts
class EmployeeInjector {
  emp: IEmployee;

  constructor() {
    const salary = new Salary(500, '$');
    this.emp = new Employee(salary);
  }
}
```

**Внедрение свойства** (англ. `Property Injection`): *зависимость предоставляется через публичное свойство* (`emp.salary`).
```ts
interface IEmployee {
  salary?: ISalary;
}

class Employee implements IEmployee {
  salary?: ISalary;

  constructor() {}
}
```
Инжектор в этом случае:
```ts
class EmployeeInjector {
  emp: IEmployee;

  constructor() {
    this.emp = new Employee();
    this.emp.salary = new Salary(500, '$');
  }
}
```
Поскольку *начальное значение* `Salary` *не задано в примере*, пришлось *сделать его необязательным* (`?:`) в интерфейсе `IEmployee` и классе `Employee` во избежание *ошибок инициализации*. *Задать начальное значение* *внедрением свойства* *нельзя*. 

**Внедрение метода** (англ. `Method Injection`): *зависимость предоставляется через метод интерфейса или класса* (`setSalary(salary)`).
```ts
/* определяем метод интерфейса, принимающий salary */
interface IEmployeeMethods {
  setSalary: (salary: ISalary) => void;
}

/* реализуем метод интерфейса в классе */
class Employee implements IEmployeeMethods {
  salary?: ISalary;

  constructor() {}

  setSalary(salary: ISalary): void {}
}
```
Инжектор в этом случае:
```ts
class EmployeeInjector {
  emp: IEmployee;

  constructor() {
    this.emp = new Employee();
    const salary = new Salary(500, '$');
    /* приводим this.emp к типу интерфейса, чтобы вызвать метод */
    (<IEmployeeMethods>this.emp).setSalary(salary);
  }
}
```

*Внедрение зависимости* на примере *модулей*:  
Пусть есть *два модуля A и B*, *модуль B использует модуль A*.  
Простой случай включения модуля:
```js
/* B.js */
const A = require('A');

module.exports = {
  /* работа с модулем A */
};
```
*Внедрение зависимости*:
```js
/* B.js */

module.exports = (A) => {
  /* работа с модулем A */
};
```
```js
/* C.js */
const A = require('A');
const B = require('B');

module.exports = {
  B: B(A),
};
```

**IoC-контейнер, DI-контейнер** — *фреймворк*, реализующий *автоматическое внедрение зависимостей*. Он управляет созданием объектов и их временем жизни, а также внедряет зависимости в класс.

*IoC-контейнер* *создаёт объект какого-то класса* и *встраивает все объекты*, от которых *зависит класс*, в *конструктор*, *свойство* или *метод класса* *во время выполнения* (англ. `at run-time`) и *утилизирует* (англ. `dispose`), когда это необходимо.

*IoC-контейнер* предоставляет следующий жизненный цикл внедряемых зависимостей (англ. `DI lifecycle`):
* Регистрация (англ. `Register`)
* Разрешение (англ. `Resolve`)
* Ликвидация (англ. `Dispose`)

Пример использования *IoC-контейнера* в *NodeJS-приложении*.
```js
const awilix = require('awilix');

const container = awilix.createContainer({
  injectionMode: awilix.InjectionMode.PROXY
});

const createUserRepository = ({ db }) => ({
  getUserById(id) { return db.query(/* ... */) }
});

function Database(connectionString) { /* ... */ }

/* регистрация */
container.register({
  connectionString: awilix.asValue('/* ... */'),
  userRepository: awilix.asFunction(createUserRepository),
  db: awilix.asClass(Database),
});

/* разрешение и использование */
container.resolve('userRepository').getUserById('1');

/* ликвидация контейнера */
container.dispose();
```

### Фабричный метод

**Фабричный метод** (англ. `Factory Method`) — порождающий паттерн, *определяющий интерфейс для создания объекта* некоторого класса, при этом *решение о том, какой класс будет у объекта*, *принимается в дочерних классах*.

*Жизненный пример*: *сеть пекарен*. Команда «*испечь хлеб*» *одна на всю сеть*, а *какой хлеб получится*, решает *конкретная пекарня*: в одной — *багет*, в другой — *бородинский*.

*Связываем объекты разных классов* в *одно семейство*, характеризующее их *общие черты*.

*Голуби* и *утки* являются *летающими птицами*. Пусть их классы `Dove` и `Duck` *реализуют общий интерфейс* `IFlyingBird`. 
```ts
interface IFlyingBird { /* ... */ }
class Dove implements IFlyingBird { /* ... */ }
class Duck implements IFlyingBird { /* ... */ }
```
Описываем *абстрактный фабричный метод*, *создающий летающих птиц*, и *реализуем его в конкретных классах* (фабриках).
```ts
interface IFlyingBirdFactory {
  create(): IFlyingBird /* фабричный метод */
}

class DoveFactory implements IFlyingBirdFactory {
  create(): IFlyingBird {
    return new Dove();
  }
}

class DuckFactory implements IFlyingBirdFactory {
  create(): IFlyingBird {
    return new Duck();
  }
}

const doveFactory = new DoveFactory();
const dove = doveFactory.create();

const duckFactory = new DuckFactory();
const duck = duckFactory.create();

const flyingBirds: IFlyingBird[] = [dove, duck];
```
Использовать фабрики *очень удобно*, поскольку мы *избегаем использования оператора* `new` *напрямую*, *инкапсулируя эту логику внутри метода*.
Это позволяет также *инкапсулировать внутри метода* *некоторые общие свойства* для *какой-то подгруппы объектов*, *не дублируя при этом код* и *не создавая новый класс* для них (не считая самой фабрики).
```ts
class BlackDoveFactory implements IFlyingBirdFactory {
  create(): IFlyingBird {
    return new Dove({ color: 'black', size: 'large' });
  }
}
```

Сделать *что-то похожее* можно *при помощи функций*, но это уже *нельзя назвать фабричным методом*.
```ts
const createDove = (): Dove => new Dove();
const createWhiteDove = (): Dove => new Dove({ color: 'white', gender: 'female' });
const createBlackDove = (): Dove => new Dove({ color: 'black', gender: 'male' });

/* создаём 5 чёрных голубей */
const doves: Dove[] = [...new Array(5)].map(createBlackDove);
/* добавляем к ним одного белого */
doves.push(createWhiteDove());
```

### Абстрактная фабрика

**Абстрактная фабрика** (англ. `Abstract Factory`) — порождающий паттерн, позволяющий *создавать семейство концептуально связанных объектов*, *не привязываясь к классам этих объектов*.

*Жизненный пример*: *магазин одежды*, который *продаёт комплекты*. В *зимнем* — *шапка*, *куртка* и *ботинки*, в *летнем* — *панама*, *футболка* и *сандалии*. Вещи из *одного комплекта сочетаются* друг с другом, и покупатель *не соберёт случайно* шапку с сандалиями.

Интерфейс `IFlyingBirdFactory` из примеров выше по сути *описывает абстрактную фабрику с одним абстрактным методом*. Рассмотрим случай, когда *абстрактных методов несколько*.

Пусть у нас есть *люди*, *собаки* и *кошки*. Выделим кое-что, что *объединяет их всех* — *разделение пола на мужской и женский*.
```ts
interface ICreatureFactory {
  createMale(): Male;
  createFemale(): Female;
}

class HumanFactory implements ICreatureFactory {
  createMale(): Male {
    return new HumanMale();
  }
  
  createFemale(): Female {
    return new HumanFemale();
  }
}
```

Пусть у нас есть *набор инструментов*: *топор*, *кирка*, *лопата*. Инструменты *могут состоять из разных материалов*, поэтому *каждый из них должен быть абстрактным* (чтобы нельзя было создать сущность без материала, то есть представлен либо абстрактным классом (если содержит реализации методов), либо интерфейсом).
```ts
abstract class Axe { /* ... */ }
abstract class Pickaxe { /* ... */ }
abstract class Shovel { /* ... */ }
```
Пусть *материалами* будут *камень* и *железо*.
```ts
class StoneAxe extends Axe { /* ... */ }
class StonePickaxe extends Pickaxe { /* ... */ }
class StoneShovel extends Shovel { /* ... */ }
class IronAxe extends Axe { /* ... */ }
class IronPickaxe extends Pickaxe { /* ... */ }
class IronShovel extends Shovel { /* ... */ }
```
Создаём *абстрактную фабрику*, с помощью которой мы сможем *создавать инструменты из нужного материала*.
```ts
interface IToolFactory {
  createAxe(): Axe;
  createPickaxe(): Pickaxe;
  createShovel(): Shovel;
}
```
Создаём фабрики, которые её реализуют.
```ts
class StoneToolFactory implements IToolFactory {
  createAxe(): Axe {
    return new StoneAxe();
  }
  createPickaxe(): Pickaxe {
    return new StonePickaxe();
  }
  createShovel(): Shovel {
    return new StoneShovel();
  }
}

class IronToolFactory implements IToolFactory {
  createAxe(): Axe {
    return new IronAxe();
  }
  createPickaxe(): Pickaxe {
    return new IronPickaxe();
  }
  createShovel(): Shovel {
    return new IronShovel();
  }
}

const toolFactory = new StoneToolFactory();

const stoneTools = [
  toolFactory.createAxe(),
  toolFactory.createPickaxe(),
  toolFactory.createShovel(),
];
```
Такой подход *очень расширяемый*. Если в будущем мы захотим *добавить новый материал* для набора инструментов (например, сталь или золото), можно будет это очень просто реализовать *созданием новой фабрики*.

### Строитель

**Строитель** (англ. `Builder`) — порождающий паттерн, предоставляющий возможность *поэтапного (пошагового) создания составных объектов*.

*Жизненный пример*: *сборка компьютера на заказ*. Процессор, память и диск *выбирают по шагам*, часть шагов *можно пропустить*, а в конце получается *один готовый компьютер*.

Будем делать пиццу.  
Пусть пицца может быть *разных размеров*, *на разном тесте* и *с разными ингредиентами*.
```ts
enum Size { Small = 'Small', Medium = 'Medium', Large = 'Large' }
enum DoughType { Thin = 'Thin', Thick = 'Thick' }

enum CheeseType { Mozzarella = 'Mozzarella', Parmesan = 'Parmesan', Gouda = 'Gouda' , Cheddar = 'Cheddar' }
enum HamType { Chicken = 'Chicken', Pork = 'Pork', Turkey = 'Turkey' }
enum MushroomsType { Chanterelles = 'Chanterelles', Champignons = 'Champignons' }
enum SauceType { BBQ = 'BBQ', Ketchup = 'Ketchup', Ranch = 'Ranch', Garlic = 'Garlic' }
enum VegetableType { Tomato = 'Tomato', Pepper = 'Pepper' }

type Ingredient = CheeseType | HamType | MushroomsType | SauceType | VegetableType;

class Pizza {
  size: Size;
  dough: DoughType;
  ingredients: Ingredient[];
  constructor(size: Size, dough: DoughType) {
    this.size = size;
    this.dough = dough;
    this.ingredients = [];
  }
  addIngredient(ingredient: Ingredient): void {
    this.ingredients.push(ingredient);
  }
}
```
Описываем *абстрактного строителя* `PizzaBuilder`, который может создать любую пиццу.  
*Его наследники* будут *переопределять метод* `build`, в котором *определяется последовательность добавления ингредиентов*.
```ts
abstract class PizzaBuilder {
  private pizza: Pizza;
  constructor(size: Size, dough: DoughType) {
    this.pizza = new Pizza(size, dough);
  }
  addCheese(cheese: CheeseType): void {
    this.pizza.addIngredient(cheese);
  };
  addHam(ham: HamType): void {
    this.pizza.addIngredient(ham);
  };
  addMushrooms(mushrooms: MushroomsType): void {
    this.pizza.addIngredient(mushrooms);
  };
  addSauce(sauce: SauceType): void {
    this.pizza.addIngredient(sauce);
  };
  addVegetable(vegetable: VegetableType): void {
    this.pizza.addIngredient(vegetable);
  };
  abstract build(): void
  async cook(time: number): Promise<Pizza> {
    this.build();
    await new Promise(resolve => setTimeout(resolve, time));
    return this.pizza;
  }
}
```
Описываем *конкретного строителя* `MargheritaPizzaBuilder`, который может создать пиццу "Маргариту" (тонкое тесто, томаты, моцарелла, томатная паста).
```ts
class MargheritaPizzaBuilder extends PizzaBuilder {
  constructor(size: Size) {
    super(size, DoughType.Thin);
  }
  build(): void {
    this.addVegetable(VegetableType.Tomato);
    this.addCheese(CheeseType.Mozzarella);
    this.addSauce(SauceType.Ketchup);
  }
}
```
Описываем *директора*, который *знает, как работать со строителем*, чтобы *получить готовую пиццу* (нужно собрать пиццу и готовить её в течение 3 секунд).
```ts
class Director {
  async cookMargherita(size: Size): Promise<Pizza> {
    const margheritaBuilder = new MargheritaPizzaBuilder(size);
    const pizza = await margheritaBuilder.cook(3000);
    console.log('Margherita is ready!');
    return pizza;
  }
}

const director = new Director();
director.cookMargherita(Size.Medium).then(pizza => console.log(pizza));
```
Метод `cook` *сам вызывает* `build`, поэтому директору *достаточно вызвать* `cook`. Если вызвать `build` ещё и отдельно, *ингредиенты добавятся дважды*.

Для полноты картины рассмотрим *ещё один небольшой пример*: *построение ландшафта*. Здесь `add`-методы будут реализованы *не в родительском абстрактном строителе*, а *в конкретном дочернем*.
```ts
class Landscape { /* ... */ }

abstract class LandscapeBuilder {
  private landscape: Landscape;
  constructor() {
    this.landscape = new Landscape();
  }
  abstract addTree(): void;
  abstract addBush(): void;
  abstract addRiver(): void;
  render(): Landscape {
    return this.landscape;
  }
}

class ForestLandscapeBuilder extends LandscapeBuilder {
  addTree(): void { /* ... */ };
  addBush(): void { /* ... */ };
  addRiver(): void { /* ... */ };
}

class ForestDirector {
  buildForest() {
    const builder = new ForestLandscapeBuilder();
    builder.addBush();
    builder.addTree();
    builder.addTree();
    return builder.render(); 
  }
}

const director = new ForestDirector();
director.buildForest();
```

В *JavaScript* строитель *чаще всего* встречается в виде *цепочки вызовов* (англ. `fluent interface`): *каждый метод настраивает очередную часть* и *возвращает сам строитель*, а *последний вызов собирает результат*. Так часто устроены *построители SQL-запросов*.
```ts
class QueryBuilder {
  private fields: string[] = ['*'];
  private table = '';
  private conditions: string[] = [];

  select(...fields: string[]): this {
    this.fields = fields;
    return this;
  }

  from(table: string): this {
    this.table = table;
    return this;
  }

  where(condition: string): this {
    this.conditions.push(condition);
    return this;
  }

  build(): string {
    if (!this.table) {
      throw new Error('Не указана таблица');
    }
    const where = this.conditions.length ? ` WHERE ${this.conditions.join(' AND ')}` : '';
    return `SELECT ${this.fields.join(', ')} FROM ${this.table}${where}`;
  }
}

const query = new QueryBuilder()
  .select('name', 'email')
  .from('users')
  .where('age >= 18')
  .where('active = true')
  .build();

console.log(query); // SELECT name, email FROM users WHERE age >= 18 AND active = true
console.log(new QueryBuilder().from('users').build()); // SELECT * FROM users
```
Без строителя пришлось бы *передавать в конструктор все части сразу*, в том числе *необязательные*.

### Прототип

**Прототип** (англ. `Prototype`) — порождающий паттерн, позволяющий *копировать объект любой сложности* *без привязки к его конкретному классу*.

*Жизненный пример*: *ксерокс*. Чтобы получить *копию документа*, *не нужно набирать его заново*: достаточно *скопировать готовый*.

```ts
interface Prototype<T> {
  clone(): T
}

class Article implements Prototype<Article> {
  title: string;
  date: Date;
  constructor(title: string) {
    this.title = title;
    this.date = new Date();
  }
  clone(): this {
    /* новый объект с тем же прототипом, что у оригинала, и с копией его полей */
    return Object.assign(Object.create(Object.getPrototypeOf(this)), this);
  }
}

const article = new Article('Prototype Pattern');
const copy = article.clone();
console.log(article === copy); // false
console.log(copy instanceof Article); // true
console.log(copy.date === article.date); // true
```
Если копировать в *пустой объект* (`Object.assign({}, this)`), получится *обычный объект без методов класса*: `copy instanceof Article` вернёт `false`, а `copy.clone` будет `undefined`. `Object.create` *сохраняет прототип*, поэтому копия *остаётся экземпляром* `Article`.

Копирование здесь *неглубокое*: `copy.date` и `article.date` — *один и тот же объект* `Date`, и его изменение *затронет обе статьи*.

Есть [много способов клонирования объектов](https://github.com/Max-Starling/Notes/blob/master/JavaScript.md#клонирование-объектов) реализовать функцию `clone()`. *Каждый из них* имеет *свои преимущества и недостатки* по сравнению с остальными.  

## Структурные

**Структурные паттерны** отвечают за то, *как из классов и объектов собираются более крупные структуры* и *как при этом сохранить гибкость*.

- [Адаптер](#адаптер) (англ. `Adapter`)
- [Декоратор](#декоратор) (англ. `Decorator`)
- [Заместитель](#заместитель) (англ. `Proxy`)
- [Фасад](#фасад) (англ. `Facade`)
- [Компоновщик](#компоновщик) (англ. `Composite`)
- [Мост](#мост) (англ. `Bridge`)
- [Приспособленец](#приспособленец) (англ. `Flyweight`)

### Адаптер

**Адаптер** (англ. `Adapter`) — структурный паттерн, позволяющий *объектам с несовместимыми интерфейсами работать вместе*. Адаптер *оборачивает* один объект и *переводит обращения* к нему в формат, *понятный другому*.

*Жизненный пример*: *переходник для розетки*. Вилка и розетка *остаются прежними*, а между ними появляется *небольшая деталь*, которая их *согласует*.

Пусть приложение *отправляет уведомления* через интерфейс `INotifier`.
```ts
interface INotifier {
  send(to: string, text: string): void;
}

class EmailNotifier implements INotifier {
  send(to: string, text: string): void {
    console.log(`email для ${to}: ${text}`);
  }
}
```
Понадобилось *отправлять SMS*. Подключаем *стороннюю библиотеку*, но её класс *устроен иначе*: у метода *другое название*, а параметры передаются *одним объектом*. *Изменить библиотеку* мы не можем.
```ts
/* класс из сторонней библиотеки */
class SmsClient {
  sendSms(message: { phone: string; body: string }): void {
    console.log(`sms на ${message.phone}: ${message.body}`);
  }
}
```
Пишем *адаптер*: он *реализует интерфейс*, который ждёт приложение, а *внутри вызывает библиотеку*.
```ts
class SmsNotifier implements INotifier {
  private client: SmsClient;

  constructor(client: SmsClient) {
    this.client = client;
  }

  send(to: string, text: string): void {
    this.client.sendSms({ phone: to, body: text });
  }
}

/* код приложения знает только про INotifier */
const notify = (notifier: INotifier, to: string) => notifier.send(to, 'Заказ доставлен');

notify(new EmailNotifier(), 'max@example.com'); /* email для max@example.com: Заказ доставлен */
notify(new SmsNotifier(new SmsClient()), '+123456789'); /* sms на +123456789: Заказ доставлен */
```
Если библиотеку придётся *заменить*, *переписать* нужно будет *только адаптер*.

Адаптер можно сделать и *через наследование*: `class SmsNotifier extends SmsClient implements INotifier`. Такой вариант называют **адаптером класса** (англ. `class adapter`), а вариант с обёрткой — **адаптером объекта** (англ. `object adapter`); оба описаны ещё в [*книге GoF*](https://www.informit.com/articles/article.aspx?p=1398600). *Обёртка гибче*: в неё можно передать *любой объект нужного типа*, в том числе *заглушку в тестах*.

*Порты и адаптеры* из [*Шестиугольной архитектуры*](./Architectural-Patterns.md#порт-и-адаптер) — *та же идея* на *уровне архитектуры*: порт — интерфейс, который ждёт приложение, адаптер — обёртка над *конкретной технологией*.

### Декоратор

**Декоратор** (англ. `Decorator`) — структурный паттерн, позволяющий *динамически добавлять объекту новые обязанности*, *оборачивая* его в объекты *с тем же интерфейсом*. Декоратор — *гибкая альтернатива наследованию*.

*Жизненный пример*: *телефон в чехле*. Чехол *не меняет телефон* и *не мешает им пользоваться*, но *добавляет защиту*. Поверх можно наклеить *защитное стекло* — *ещё один слой*.

Вернёмся к *пицце* из примера со [*Строителем*](#строитель). Пусть у каждой пиццы есть *описание* и *цена*.
```ts
interface IPizza {
  getDescription(): string;
  getPrice(): number;
}

class Margherita implements IPizza {
  getDescription(): string {
    return 'Маргарита';
  }

  getPrice(): number {
    return 10;
  }
}
```
Клиенты хотят *добавки*: сыр, грибы. Если под *каждое сочетание* заводить *подкласс* (`MargheritaWithCheese`, `MargheritaWithMushrooms`, `MargheritaWithCheeseAndMushrooms`), классов станет *слишком много*: каждая новая добавка *удваивает число сочетаний*.

*Декоратор* — обёртка, которая *реализует тот же интерфейс* `IPizza`, *хранит внутри* оборачиваемую пиццу и *дополняет её поведение*.
```ts
abstract class PizzaDecorator implements IPizza {
  protected pizza: IPizza;

  constructor(pizza: IPizza) {
    this.pizza = pizza;
  }

  abstract getDescription(): string;
  abstract getPrice(): number;
}

class WithCheese extends PizzaDecorator {
  getDescription(): string {
    return `${this.pizza.getDescription()} + сыр`;
  }

  getPrice(): number {
    return this.pizza.getPrice() + 2;
  }
}

class WithMushrooms extends PizzaDecorator {
  getDescription(): string {
    return `${this.pizza.getDescription()} + грибы`;
  }

  getPrice(): number {
    return this.pizza.getPrice() + 3;
  }
}

const pizza: IPizza = new WithMushrooms(new WithCheese(new Margherita()));
console.log(pizza.getDescription()); // Маргарита + сыр + грибы
console.log(pizza.getPrice()); // 15

const doubleCheese: IPizza = new WithCheese(new WithCheese(new Margherita()));
console.log(doubleCheese.getDescription()); // Маргарита + сыр + сыр
```
Обёртки можно *вкладывать друг в друга* в *любом порядке и количестве*, а код, который работает с `IPizza`, *не замечает разницы*.

В *JavaScript* декоратором часто служит [*функция высшего порядка*](./FunctionalProgramming.md#функции-высшего-порядка): она *принимает функцию* и *возвращает новую* — с *тем же вызовом*, но *дополненным поведением*.
```ts
const withLogging = <A extends unknown[], R>(fn: (...args: A) => R) =>
  (...args: A): R => {
    console.log(`вызов ${fn.name}(${args.join(', ')})`);
    return fn(...args);
  };

const sum = (a: number, b: number): number => a + b;
const loggedSum = withLogging(sum);

console.log(loggedSum(2, 3));
/* вызов sum(2, 3)
5 */
```
По той же схеме в *React* устроены **компоненты высшего порядка** (англ. `Higher-Order Components`, `HOC`): функция *принимает компонент* и *возвращает новый компонент* с дополнительным поведением. [*Документация React*](https://legacy.reactjs.org/docs/higher-order-components.html) отмечает, что *в современном коде* HOC используются *редко*.

*Не путать* с синтаксисом `@decorator` в *TypeScript*: это *возможность языка*, которая помогает оборачивать классы и методы, а паттерн — *идея*, которую можно реализовать и *без неё*.

### Заместитель

**Заместитель** (англ. `Proxy`) — структурный паттерн, *подставляющий вместо настоящего объекта* объект-заменитель *с тем же интерфейсом*, чтобы *управлять доступом* к оригиналу: *отложить его создание*, *закэшировать ответы*, *проверить права*, *записать вызовы* в журнал.

*Жизненный пример*: *секретарь руководителя*. Звонок адресован руководителю, но сначала его *принимает секретарь*: на простой вопрос *ответит сам*, лишний звонок *отсеет*, а важный — *переведёт*.

Пусть *сервис погоды* ходит в *медленное внешнее API*.
```ts
interface IWeatherService {
  getTemperature(city: string): Promise<number>;
}

class WeatherService implements IWeatherService {
  async getTemperature(city: string): Promise<number> {
    console.log(`запрос к API: ${city}`);
    await new Promise(resolve => setTimeout(resolve, 100)); /* имитация сети */
    return 20;
  }
}
```
*Кэширующий заместитель* реализует *тот же интерфейс* и обращается к настоящему сервису, *только если ответа ещё нет в кэше*.
```ts
class CachedWeatherService implements IWeatherService {
  private service: IWeatherService;
  private cache = new Map<string, number>();

  constructor(service: IWeatherService) {
    this.service = service;
  }

  async getTemperature(city: string): Promise<number> {
    const cached = this.cache.get(city);
    if (cached !== undefined) {
      return cached;
    }
    const temperature = await this.service.getTemperature(city);
    this.cache.set(city, temperature);
    return temperature;
  }
}

const weather: IWeatherService = new CachedWeatherService(new WeatherService());
await weather.getTemperature('Minsk'); /* запрос к API: Minsk */
await weather.getTemperature('Minsk'); /* ничего не выводится: ответ взят из кэша */
```
Код, который использует `IWeatherService`, *не знает*, работает он с *сервисом* или с *заместителем*.

*Заместители* бывают *разные*:
* **Кэширующий** *запоминает ответы*, как в примере выше.
* **Виртуальный** *создаёт тяжёлый объект только при первом обращении* к нему (ленивая инициализация).
* **Защищающий** *проверяет права* перед вызовом.
* **Удалённый** *выглядит как локальный объект*, а на деле *отправляет запросы по сети*. Так устроен [*удалённый вызов процедур*](./GraphQL-REST.md#rpc): в статье 1984 года программа делает *обычный локальный вызов* процедуры в *клиентской заглушке* (англ. `user-stub`), а уже она *упаковывает аргументы* и *отправляет их* на другую машину.
* **Логирующий** *записывает вызовы* в журнал.

В *JavaScript* заместитель *встроен в язык*: объект `Proxy` *перехватывает* чтение, запись и другие операции над объектом. Подробно — в [*заметке про JavaScript*](./JavaScript.md#proxy).

Заместитель *похож на Декоратор*: оба *оборачивают объект с тем же интерфейсом*. Разница — *в цели*. Декоратор *добавляет обязанности*, и обёртки обычно *собирает клиент*. Заместитель *управляет доступом* к объекту и часто *сам решает*, когда создать или вызвать оригинал.

### Фасад

**Фасад** (англ. `Facade`) — структурный паттерн, *предоставляющий простой интерфейс* к *сложной подсистеме*: набору классов, библиотеке или API.

*Жизненный пример*: *заказ пиццы по телефону*. Клиент называет *адрес* и *пиццу*, а кухня, касса и курьеры *работают за спиной оператора*: знать о них клиенту *не нужно*.

Пусть *оформление заказа* требует *трёх подсистем*: склада, оплаты и доставки.
```ts
class Warehouse {
  reserve(productId: string): void {
    console.log(`товар ${productId} зарезервирован`);
  }
}

class Payments {
  charge(userId: string, amount: number): void {
    console.log(`с ${userId} списано ${amount}$`);
  }
}

class Delivery {
  schedule(userId: string, productId: string): void {
    console.log(`доставка ${productId} для ${userId} назначена`);
  }
}
```
Если *каждый*, кто оформляет заказ (сайт, мобильное приложение, бот), будет *сам вызывать все три подсистемы* в нужном порядке, логика *продублируется*, а любое изменение порядка придётся *вносить во всех местах*. Фасад *собирает эту последовательность* в *одном методе*.
```ts
class OrderFacade {
  private warehouse = new Warehouse();
  private payments = new Payments();
  private delivery = new Delivery();

  placeOrder(userId: string, productId: string, amount: number): void {
    this.warehouse.reserve(productId);
    this.payments.charge(userId, amount);
    this.delivery.schedule(userId, productId);
  }
}

new OrderFacade().placeOrder('max', 'pizza-42', 15);
/* товар pizza-42 зарезервирован
с max списано 15$
доставка pizza-42 для max назначена */
```
Фасад *не запрещает работать с подсистемой напрямую*: кому нужна *тонкая настройка*, обращается к её классам, остальным *хватает одного метода*.

Фасад и Адаптер *оба оборачивают чужой код*, но Адаптер *подгоняет существующий интерфейс* под ожидаемый, а Фасад *придумывает новый*, более простой, для *целой подсистемы*.

*Опасность Фасада* — превратиться в **божественный объект** (англ. `God Object`), через который *проходит всё* и который *знает обо всём*. Разросшийся фасад *делят на несколько* — по задачам.

### Компоновщик

**Компоновщик** (англ. `Composite`) — структурный паттерн, позволяющий *собирать объекты в древовидную структуру* и *работать с ней так же*, как с *отдельным объектом*.

*Жизненный пример*: *файловая система*. В папке лежат *файлы* и *другие папки*, но *размер* можно спросить *одинаково* и у файла, и у папки: папка *сама сложит размеры* своего содержимого.
```ts
interface IFileSystemItem {
  name: string;
  getSize(): number;
}

/* лист дерева */
class FileItem implements IFileSystemItem {
  name: string;
  private size: number;

  constructor(name: string, size: number) {
    this.name = name;
    this.size = size;
  }

  getSize(): number {
    return this.size;
  }
}

/* контейнер: хранит других участников дерева */
class Folder implements IFileSystemItem {
  name: string;
  private children: IFileSystemItem[] = [];

  constructor(name: string) {
    this.name = name;
  }

  add(item: IFileSystemItem): this {
    this.children.push(item);
    return this;
  }

  getSize(): number {
    return this.children.reduce((sum, child) => sum + child.getSize(), 0);
  }
}

const project = new Folder('project')
  .add(new FileItem('README.md', 2))
  .add(new Folder('src')
    .add(new FileItem('index.ts', 10))
    .add(new FileItem('utils.ts', 5)));

console.log(project.getSize()); // 17
```
Клиенту *не нужно проверять*, файл перед ним или папка: у обоих *один интерфейс* `IFileSystemItem`, а *рекурсию прячет* метод папки.

Компоновщик встречается *везде, где есть деревья*: *DOM* (у `<body>` и у любого `<div>` одни и те же методы, хотя внутри одного — *тысячи узлов*), *дерево компонентов React*, *вложенные меню*, *структура компании*.

### Мост

**Мост** (англ. `Bridge`) — структурный паттерн, *отделяющий абстракцию от её реализации* так, чтобы их можно было *изменять независимо* друг от друга. Вместо *одной иерархии классов*, в которой *перемножаются все варианты*, появляются *две*, связанные *ссылкой* — «*мостом*».

Пусть есть *два вида сообщений* (напоминание и срочное оповещение) и *два канала доставки* (email и SMS). Если делать *по классу на сочетание* (`EmailReminder`, `SmsReminder`, `EmailAlert`, `SmsAlert`), каждый новый канал добавит *по классу на каждый вид сообщения*, а каждый новый вид — *по классу на каждый канал*: классов будет *видов × каналов*.

Мост *разделяет эти два измерения*. *Каналы* — это *реализация*.
```ts
interface IChannel {
  deliver(to: string, text: string): void;
}

class EmailChannel implements IChannel {
  deliver(to: string, text: string): void {
    console.log(`email для ${to}: ${text}`);
  }
}

class SmsChannel implements IChannel {
  deliver(to: string, text: string): void {
    console.log(`sms для ${to}: ${text}`);
  }
}
```
*Виды сообщений* — это *абстракция*: сообщение *хранит ссылку на канал* и *ничего не знает* о том, *как именно* он доставляет.
```ts
abstract class Message {
  protected channel: IChannel;

  constructor(channel: IChannel) {
    this.channel = channel;
  }

  abstract send(to: string): void;
}

class Reminder extends Message {
  send(to: string): void {
    this.channel.deliver(to, 'Напоминаем о встрече завтра');
  }
}

class Alert extends Message {
  send(to: string): void {
    this.channel.deliver(to, 'СРОЧНО: сервер недоступен');
  }
}

new Reminder(new EmailChannel()).send('max'); /* email для max: Напоминаем о встрече завтра */
new Alert(new SmsChannel()).send('max'); /* sms для max: СРОЧНО: сервер недоступен */
```
Теперь *новый канал* (например, push-уведомления) — это *один класс*, *новый вид сообщения* — тоже *один*. Классов становится *видов + каналов*, а не *видов × каналов*.

Мост — применение [*композиционного принципа повторного использования*](./Architecture-Design.md#crp-композиционный-принцип-повторного-использования): сообщение *не наследуется от канала*, а *содержит его*.

Мост и Адаптер *устроены похоже*, но Адаптер обычно появляется, *когда классы уже написаны и не стыкуются*, а Мост *закладывают заранее*, чтобы две иерархии *развивались независимо*.

### Приспособленец

**Приспособленец**, **Легковес** (англ. `Flyweight`) — структурный паттерн, позволяющий *уместить в памяти огромное количество мелких объектов*: одинаковую часть состояния они хранят *не каждый у себя*, а в *общих разделяемых объектах*.

Состояние объекта *делят на две части*:
* **Внутреннее состояние** (англ. `intrinsic state`) *одинаково у многих объектов* и *не зависит от контекста*. Его *выносят* в разделяемый объект-приспособленец.
* **Внешнее состояние** (англ. `extrinsic state`) *уникально для каждого объекта* (например, координаты). Оно *хранится отдельно* или *передаётся в методы*.

Вернёмся к *лесу* из примера со [*Строителем*](#строитель). Пусть в лесу *миллион деревьев трёх видов*. Название, цвет и текстура у всех деревьев одного вида *одинаковые*, а координаты у каждого *свои*.
```ts
/* приспособленец: то, что одинаково у всех деревьев одного вида */
class TreeType {
  name: string;
  color: string;
  texture: string;

  constructor(name: string, color: string, texture: string) {
    this.name = name;
    this.color = color;
    this.texture = texture; /* представим, что это тяжёлая картинка */
  }
}

/* фабрика приспособленцев: отдаёт уже созданный вид, если он есть */
class TreeTypeFactory {
  private static types = new Map<string, TreeType>();

  static get(name: string, color: string, texture: string): TreeType {
    const key = `${name}|${color}|${texture}`;
    let type = TreeTypeFactory.types.get(key);
    if (!type) {
      type = new TreeType(name, color, texture);
      TreeTypeFactory.types.set(key, type);
    }
    return type;
  }

  static count(): number {
    return TreeTypeFactory.types.size;
  }
}

/* дерево хранит только своё: координаты и ссылку на вид */
class Tree {
  x: number;
  y: number;
  type: TreeType;

  constructor(x: number, y: number, type: TreeType) {
    this.x = x;
    this.y = y;
    this.type = type;
  }
}

const kinds: [string, string, string][] = [
  ['дуб', 'зелёный', 'oak.png'],
  ['берёза', 'светло-зелёный', 'birch.png'],
  ['ель', 'тёмно-зелёный', 'spruce.png'],
];

const forest: Tree[] = [];
for (let i = 0; i < 1_000_000; i++) {
  const [name, color, texture] = kinds[i % kinds.length];
  const type = TreeTypeFactory.get(name, color, texture);
  forest.push(new Tree(Math.random() * 1000, Math.random() * 1000, type));
}

console.log(forest.length); // 1000000
console.log(TreeTypeFactory.count()); // 3
```
Деревьев — *миллион*, а объектов с текстурами — *три*. Без приспособленца *каждое дерево* держало бы *свою копию* названия, цвета и текстуры.

Приспособленец оправдан, *только когда объектов действительно много* и *память — настоящая проблема*: иначе он *лишь усложняет код*. Кстати, в *черновике 2005 года* авторы GoF предлагали *вынести Приспособленца вместе с Интерпретатором* в *отдельную группу*: эти два паттерна *слишком не похожи* на остальные ([*интервью 2009 года*](https://www.informit.com/articles/article.aspx?p=1404056)).

## Поведенческие

**Поведенческие паттерны** отвечают за *алгоритмы* и *распределение обязанностей между объектами*: *кто что делает* и *как объекты общаются* друг с другом.

- [Наблюдатель](#наблюдатель) (англ. `Observer`)
- [Стратегия](#стратегия) (англ. `Strategy`)
- [Состояние](#состояние) (англ. `State`)
- [Шаблонный метод](#шаблонный-метод) (англ. `Template Method`)
- [Команда](#команда) (англ. `Command`)
- [Хранитель](#хранитель) (англ. `Memento`)
- [Итератор](#итератор) (англ. `Iterator`)
- [Цепочка обязанностей](#цепочка-обязанностей) (англ. `Chain of Responsibility`)
- [Посредник](#посредник) (англ. `Mediator`)
- [Посетитель](#посетитель) (англ. `Visitor`)
- [Интерпретатор](#интерпретатор) (англ. `Interpreter`)

### Наблюдатель

**Наблюдатель** (англ. `Observer`) — поведенческий паттерн, *создающий механизм подписки*: объект-*издатель* (англ. `subject`) *хранит список подписчиков* (англ. `observers`) и *оповещает их всех*, когда в нём *что-то происходит*.

*Жизненный пример*: *подписка на журнал*. Не нужно каждый день *ходить в киоск* и *спрашивать*, вышел ли новый номер: редакция *сама пришлёт* его всем подписчикам.

Вспомним задачу из [*определений*](./Architecture-Design.md#паттерн-проектирования): *показать уведомление*, когда *пользователь появился в сети*. Издатель *хранит подписчиков* и *вызывает их* при событии, а подписка *возвращает функцию отписки*.
```ts
type Listener<T> = (data: T) => void;

class Subject<T> {
  private listeners: Listener<T>[] = [];

  subscribe(listener: Listener<T>): () => void {
    this.listeners.push(listener);
    return () => {
      this.listeners = this.listeners.filter(l => l !== listener);
    };
  }

  notify(data: T): void {
    this.listeners.forEach(listener => listener(data));
  }
}

const userOnline = new Subject<string>();

const unsubscribeToast = userOnline.subscribe(name => console.log(`уведомление: ${name} в сети`));
userOnline.subscribe(name => console.log(`список друзей: ${name} наверху`));

userOnline.notify('Max');
/* уведомление: Max в сети
список друзей: Max наверху */

unsubscribeToast();
userOnline.notify('Anna'); /* список друзей: Anna наверху */
```
Издатель *ничего не знает о подписчиках*, кроме того, что их *можно вызвать*: *новую реакцию* на событие можно добавить, *не трогая издателя*.

Наблюдатель *встроен в платформы*: в *браузере* это `addEventListener`, в *Node.js* — класс `EventEmitter` (о нём — в [*заметке про NodeJS*](./NodeJS.md#событийно-ориентированное-программирование)). `EventEmitter` вызывает слушателей *синхронно* и *в порядке подписки* ([*документация Node.js*](https://nodejs.org/api/events.html)).

На Наблюдателе построена *связь Model и View* в [*MVC*](./Architectural-Patterns.md#mvc-1979): модель *оповещает* представления *об изменениях*.

Подписку нужно *снимать*, когда подписчик *больше не нужен* (например, *компонент удалён со страницы*). Иначе издатель *продолжает держать ссылку* на подписчика, и сборщик мусора *не может освободить память* — это *утечка*. Node.js даже *выводит предупреждение* о возможной утечке, если на одно событие подписано *больше 10 слушателей*. Это *не жёсткий предел*: слушатели добавятся, но появится предупреждение ([*документация Node.js*](https://nodejs.org/api/events.html)).

Рядом часто звучит «*издатель-подписчик*» (англ. `publish-subscribe`, `pub/sub`). Обычно так называют *вариант с посредником*: издатели и подписчики *не знают друг о друге* и общаются *через канал* — шину событий или брокер сообщений. В Наблюдателе же подписчик подписывается *прямо на объект-издатель*. Про *события между частями системы* — в [*заметке про архитектурные стили*](./Architectural-Styles.md#событийная-архитектура).

### Стратегия

**Стратегия** (англ. `Strategy`) — поведенческий паттерн, *определяющий семейство взаимозаменяемых алгоритмов* и *выносящий каждый* из них *в отдельный объект*. Код, который пользуется алгоритмом (*контекст*), *знает только общий интерфейс*, поэтому алгоритм можно *подменить даже во время выполнения*.

*Жизненный пример*: *оплата в магазине*. Покупка *одна и та же*, а заплатить можно *картой*, *наличными* или *бонусами*: кассир просто *принимает выбранный способ*.

Пусть *стоимость доставки* зависит *от способа*.
```ts
interface IDeliveryStrategy {
  calculate(weightKg: number): number;
}

class CourierDelivery implements IDeliveryStrategy {
  calculate(weightKg: number): number {
    return 5 + weightKg * 2;
  }
}

class PostDelivery implements IDeliveryStrategy {
  calculate(weightKg: number): number {
    return 3 + weightKg;
  }
}

class Pickup implements IDeliveryStrategy {
  calculate(): number {
    return 0;
  }
}

class Checkout {
  private delivery: IDeliveryStrategy;

  constructor(delivery: IDeliveryStrategy) {
    this.delivery = delivery;
  }

  setDelivery(delivery: IDeliveryStrategy): void {
    this.delivery = delivery;
  }

  total(price: number, weightKg: number): number {
    return price + this.delivery.calculate(weightKg);
  }
}

const checkout = new Checkout(new CourierDelivery());
console.log(checkout.total(100, 3)); // 111

checkout.setDelivery(new Pickup());
console.log(checkout.total(100, 3)); // 100
```
*Без Стратегии* метод `total` превратился бы в *цепочку* `if-else` по способу доставки, которая *растёт с каждым новым способом*, — нарушение [*принципа открытости-закрытости*](./Architecture-Design.md#ocp-принцип-открытости-закрытости). *Со Стратегией* новый способ — это *новый класс*, а `Checkout` *не меняется*.

В *JavaScript* стратегией часто служит *обычная функция*. Например, `Array.prototype.sort` принимает *стратегию сравнения* — [*функцию-компаратор*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort).
```ts
const pizzas = [
  { name: 'Пепперони', price: 12 },
  { name: 'Маргарита', price: 10 },
  { name: 'Гавайская', price: 11 },
];

const byPrice = (a: { price: number }, b: { price: number }) => a.price - b.price;
const byName = (a: { name: string }, b: { name: string }) => a.name.localeCompare(b.name);

console.log(pizzas.sort(byPrice).map(pizza => pizza.name)); // [ 'Маргарита', 'Гавайская', 'Пепперони' ]
console.log(pizzas.sort(byName).map(pizza => pizza.name)); // [ 'Гавайская', 'Маргарита', 'Пепперони' ]
```

### Состояние

**Состояние** (англ. `State`) — поведенческий паттерн, позволяющий объекту *менять поведение* в зависимости от *своего внутреннего состояния*. Со стороны кажется, будто у объекта *поменялся класс*.

*Жизненный пример*: *торговый автомат*. Кнопка «выдать» *ничего не делает*, пока *не брошена монета*, и *выдаёт товар*, когда монета *брошена*.

Пусть *заказ* проходит через состояния «*новый*» → «*оплачен*» → «*отправлен*». *Наивное решение* — `switch` по полю `status` *в каждом методе*: каждое новое состояние придётся *добавлять во все* `switch`. Паттерн Состояние *выносит поведение каждого состояния в отдельный класс*.
```ts
interface IOrderState {
  readonly name: string;
  pay(order: Order): void;
  ship(order: Order): void;
}

class Order {
  state: IOrderState = new NewOrder();

  pay(): void {
    this.state.pay(this);
  }

  ship(): void {
    this.state.ship(this);
  }
}

class NewOrder implements IOrderState {
  readonly name = 'новый';

  pay(order: Order): void {
    order.state = new PaidOrder();
  }

  ship(): void {
    console.log('Нельзя отправить неоплаченный заказ');
  }
}

class PaidOrder implements IOrderState {
  readonly name = 'оплачен';

  pay(): void {
    console.log('Заказ уже оплачен');
  }

  ship(order: Order): void {
    order.state = new ShippedOrder();
  }
}

class ShippedOrder implements IOrderState {
  readonly name = 'отправлен';

  pay(): void {
    console.log('Заказ уже оплачен');
  }

  ship(): void {
    console.log('Заказ уже отправлен');
  }
}

const order = new Order();
order.ship(); /* Нельзя отправить неоплаченный заказ */
order.pay();
order.ship();
console.log(order.state.name); // отправлен
```
Каждое состояние *само знает*, *что делать* и *в какое состояние перейти*. Добавить состояние «*отменён*» — значит *написать ещё один класс* и *поправить переходы*, которые в него ведут.

По сути это *объектная реализация* **конечного автомата** (англ. `finite-state machine`).

Состояние и Стратегия *устроены похоже*: контекст *делегирует работу вложенному объекту*. Разница в том, *кто его меняет*. Стратегию обычно *выбирает клиент снаружи*, и стратегии *не знают друг о друге*. Состояния же *переключают друг друга сами*.

### Шаблонный метод

**Шаблонный метод** (англ. `Template Method`) — поведенческий паттерн, *задающий скелет алгоритма* в методе *базового класса* и позволяющий *подклассам переопределять отдельные шаги*, *не меняя структуру* алгоритма в целом.

Мы *уже встречали* его в примере со [*Строителем*](#строитель): метод `cook` абстрактного `PizzaBuilder` *задаёт порядок шагов* — собрать пиццу методом `build`, затем подождать, — а сам `build` *переопределяют наследники*.

Пусть нужно *выгружать отчёт в разных форматах*. Порядок *одинаковый*: заголовок, затем строки. Отличается *только оформление*.
```ts
abstract class ReportExporter {
  /* шаблонный метод: порядок шагов зафиксирован */
  export(rows: string[][]): string {
    const lines = rows.map(row => this.formatRow(row));
    const header = this.header();
    return (header ? [header, ...lines] : lines).join('\n');
  }

  /* обязательный шаг: каждый подкласс реализует по-своему */
  protected abstract formatRow(row: string[]): string;

  /* необязательный шаг (хук): по умолчанию заголовка нет */
  protected header(): string {
    return '';
  }
}

class CsvExporter extends ReportExporter {
  protected header(): string {
    return 'name,price';
  }

  protected formatRow(row: string[]): string {
    return row.join(',');
  }
}

class TextExporter extends ReportExporter {
  protected formatRow(row: string[]): string {
    return row.join(' — ');
  }
}

const rows = [['Маргарита', '10'], ['Пепперони', '12']];

console.log(new CsvExporter().export(rows));
/* name,price
Маргарита,10
Пепперони,12 */

console.log(new TextExporter().export(rows));
/* Маргарита — 10
Пепперони — 12 */
```
**Хук** (англ. `hook`) — *необязательный шаг* с *поведением по умолчанию*: подкласс *может его переопределить*, а может и нет (как `TextExporter` с заголовком).

Шаблонный метод — пример [*инверсии управления*](./Architecture-Design.md#принцип-инверсии-управления-ioc): *не подкласс вызывает общий код*, а *базовый класс вызывает методы подкласса*. Так же устроены *фреймворки*: мы *пишем методы*, а *вызывает их фреймворк*.

Шаблонный метод и Стратегия *оба меняют часть алгоритма*. Шаблонный метод делает это *через наследование*: вариант выбирается, *когда пишется подкласс*. Стратегия — *через композицию*: алгоритм можно *подменить во время выполнения*.

### Команда

**Команда** (англ. `Command`) — поведенческий паттерн, *превращающий запрос* (действие) *в самостоятельный объект*. Такой объект можно *передать как параметр*, *положить в очередь*, *записать в журнал* и *отменить*.

*Жизненный пример*: *запланированный платёж* в банковском приложении. Платёж *оформляется заранее* и хранится *отдельной записью*: его можно *выполнить позже*, *повторять каждый месяц* или *отменить*.

Пусть есть *текстовый редактор с отменой действий*.
```ts
class Editor {
  text = '';
}

interface ICommand {
  execute(): void;
  undo(): void;
}

class AppendCommand implements ICommand {
  private editor: Editor;
  private addition: string;

  constructor(editor: Editor, addition: string) {
    this.editor = editor;
    this.addition = addition;
  }

  execute(): void {
    this.editor.text += this.addition;
  }

  undo(): void {
    const { text } = this.editor;
    this.editor.text = text.slice(0, text.length - this.addition.length);
  }
}

class CommandHistory {
  private done: ICommand[] = [];

  run(command: ICommand): void {
    command.execute();
    this.done.push(command);
  }

  undo(): void {
    this.done.pop()?.undo();
  }
}

const editor = new Editor();
const commands = new CommandHistory();

commands.run(new AppendCommand(editor, 'Привет'));
commands.run(new AppendCommand(editor, ', мир'));
console.log(editor.text); // Привет, мир

commands.undo();
console.log(editor.text); // Привет
```
*Отправитель* (кнопка, горячая клавиша, пункт меню) *не знает*, что именно произойдёт: он *просто запускает команду*. *Получатель* (`Editor`) *не знает*, кто его вызвал. *Историю*, *очередь* и *повтор* даёт то, что *действие стало объектом*.

Если *отмена не нужна*, команда в *JavaScript* *вырождается в обычную функцию* — колбэк, который передают кнопке. Поэтому Норвиг и относил Команду к паттернам, которые *упрощают функции первого класса*.

*Не путать* с командой из [*CQRS*](./Architectural-Patterns.md#cqrs): там это *операция, меняющая состояние* (в противоположность запросу), а здесь — *объект, упаковывающий вызов*.

### Хранитель

**Хранитель**, **Снимок** (англ. `Memento`) — поведенческий паттерн, позволяющий *сохранять и восстанавливать прошлые состояния объекта*, *не раскрывая подробностей его устройства*.

*Жизненный пример*: *сохранение в игре*. Перед сложным боем игрок *сохраняется*, а если бой проигран, *загружает сохранение* и пробует снова. Самому игроку *не нужно знать*, что именно *записано в файл*.

В паттерне *три роли*:
* **Создатель** (англ. `originator`) — объект, *чьё состояние сохраняют*. Только он умеет *делать снимок* и *восстанавливаться* из него.
* **Снимок** (англ. `memento`) — *неизменяемый объект* с сохранённым состоянием.
* **Опекун** (англ. `caretaker`) — тот, кто *хранит снимки*, *не заглядывая внутрь*.

```ts
class Snapshot {
  readonly level: number;
  readonly health: number;

  constructor(level: number, health: number) {
    this.level = level;
    this.health = health;
  }
}

class Hero {
  private level = 1;
  private health = 100;

  fight(): void {
    this.level += 1;
    this.health -= 70;
  }

  save(): Snapshot {
    return new Snapshot(this.level, this.health);
  }

  restore(snapshot: Snapshot): void {
    this.level = snapshot.level;
    this.health = snapshot.health;
  }

  toString(): string {
    return `уровень ${this.level}, здоровье ${this.health}`;
  }
}

const hero = new Hero();
const saves: Snapshot[] = []; /* опекун */

saves.push(hero.save());
hero.fight();
console.log(`${hero}`); // уровень 2, здоровье 30

hero.restore(saves[saves.length - 1]);
console.log(`${hero}`); // уровень 1, здоровье 100
```
Поля героя *остаются приватными*: наружу уходит *только снимок*, который *нельзя изменить* (`readonly`).

Хранитель и Команда *оба помогают сделать отмену*, но *по-разному*. Команда *отменяет действие*, выполняя *обратное*, а Хранитель *возвращает сохранённое состояние целиком*. Первое *экономит память*, второе *проще*, когда обратное действие *придумать трудно*.

### Итератор

**Итератор** (англ. `Iterator`) — поведенческий паттерн, позволяющий *последовательно обходить элементы коллекции*, *не раскрывая её внутреннего устройства*: массив это, дерево или что-то ещё.

В *JavaScript* Итератор *встроен в язык*. Объект *итерируемый*, если у него есть метод `[Symbol.iterator]()`, возвращающий *итератор* — объект с методом `next()`, который отдаёт `{ value, done }`. С итерируемыми объектами работают `for...of`, *spread* `[...x]`, `yield*` и *деструктуризация массива* ([*MDN*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)). Как сделать объект итерируемым *вручную* — в [*заметке про JavaScript*](./JavaScript.md#итерируемые-объекты).

Удобнее всего писать итератор *генератором*. Пусть есть *дерево*, и нужно *обойти все его значения*. Снаружи *не видно*, что это дерево: для `for...of` и *spread* оно выглядит *как обычная последовательность*.
```ts
class TreeNode {
  value: number;
  children: TreeNode[];

  constructor(value: number, children: TreeNode[] = []) {
    this.value = value;
    this.children = children;
  }

  /* обход в глубину */
  *[Symbol.iterator](): Generator<number> {
    yield this.value;
    for (const child of this.children) {
      yield* child;
    }
  }
}

const tree = new TreeNode(1, [
  new TreeNode(2, [new TreeNode(3)]),
  new TreeNode(4),
]);

console.log([...tree]); // [ 1, 2, 3, 4 ]

for (const value of tree) {
  if (value > 2) break; /* обход можно прервать: оставшиеся узлы не посещаются */
  console.log(value);
}
/* 1
2 */
```
Коллекция может *поменять устройство* (например, хранить узлы в массиве, а не в дереве), и код, который её обходит, *этого не заметит*.

### Цепочка обязанностей

**Цепочка обязанностей** (англ. `Chain of Responsibility`) — поведенческий паттерн, позволяющий *передавать запрос последовательно по цепочке обработчиков*. Каждый обработчик *решает сам*: *обработать запрос* и остановиться или *передать его следующему*.

*Жизненный пример*: *согласование отпуска*. Заявление сначала смотрит *руководитель команды*, длинный отпуск он передаёт *руководителю отдела*, а совсем длинный — *генеральному директору*. Каждый может *одобрить сам* или *передать выше*.
```ts
abstract class Approver {
  private next?: Approver;

  setNext(approver: Approver): Approver {
    this.next = approver;
    return approver; /* чтобы собирать цепочку в одну строку */
  }

  approve(days: number): string {
    if (this.next) {
      return this.next.approve(days);
    }
    return `Отпуск на ${days} дн. никто не одобрил`;
  }
}

class TeamLead extends Approver {
  approve(days: number): string {
    return days <= 3 ? `Руководитель команды одобрил ${days} дн.` : super.approve(days);
  }
}

class HeadOfDepartment extends Approver {
  approve(days: number): string {
    return days <= 14 ? `Руководитель отдела одобрил ${days} дн.` : super.approve(days);
  }
}

class Ceo extends Approver {
  approve(days: number): string {
    return days <= 28 ? `Генеральный директор одобрил ${days} дн.` : super.approve(days);
  }
}

const approvals = new TeamLead();
approvals.setNext(new HeadOfDepartment()).setNext(new Ceo());

console.log(approvals.approve(2)); // Руководитель команды одобрил 2 дн.
console.log(approvals.approve(10)); // Руководитель отдела одобрил 10 дн.
console.log(approvals.approve(60)); // Отпуск на 60 дн. никто не одобрил
```
Отправитель *знает только первое звено* и *не знает*, кто в итоге обработает запрос. Звенья можно *переставлять*, *добавлять* и *убирать*, не трогая остальных.

На цепочке построены *промежуточные обработчики* (англ. `middleware`) в *Express*: каждый получает *запрос*, *ответ* и функцию `next`. Обработчик *либо завершает цикл «запрос — ответ»*, *либо вызывает* `next()`, чтобы *передать управление дальше* ([*документация Express*](https://expressjs.com/en/guide/using-middleware.html)).
```js
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`); /* логирование */
  next();
});

app.use((req, res, next) => {
  if (!req.headers.authorization) {
    return res.status(401).send('Unauthorized'); /* цепочка обрывается */
  }
  next();
});

app.get('/profile', (req, res) => res.send('Профиль'));
```
Похоже работает и *всплытие событий в DOM*: событие *поднимается от элемента к его предкам*, пока какой-нибудь обработчик *не вызовет* [`event.stopPropagation()`](https://developer.mozilla.org/en-US/docs/Web/API/Event/stopPropagation). Разница в том, что при всплытии срабатывают *все обработчики* на пути, а не только первый подходящий.

### Посредник

**Посредник** (англ. `Mediator`) — поведенческий паттерн, *убирающий прямые связи между объектами*: вместо того чтобы обращаться друг к другу, они *общаются через объект-посредник*, который знает, *кому и что передать*.

*Жизненный пример*: *модератор на собрании*. Участники *не перебивают друг друга*, а *поднимают руку*, и модератор *даёт слово*.

Вспомним пример из [*MVP*](./Architectural-Patterns.md#зависимые-views): *поля ввода* и *кнопка*, которая *недоступна, пока поля не заполнены*. Если поля будут *сами включать кнопку*, каждый компонент *станет знать о других*, и *переиспользовать их по отдельности* не выйдет. Посредник — *форма* — *собирает логику взаимодействия в одном месте*.
```ts
interface IMediator {
  notify(sender: Component, event: string): void;
}

class Component {
  protected mediator?: IMediator;

  setMediator(mediator: IMediator): void {
    this.mediator = mediator;
  }
}

class Input extends Component {
  value = '';

  type(value: string): void {
    this.value = value;
    this.mediator?.notify(this, 'change');
  }
}

class Button extends Component {
  disabled = true;

  click(): void {
    if (!this.disabled) {
      this.mediator?.notify(this, 'click');
    }
  }
}

class SignUpForm implements IMediator {
  email = new Input();
  password = new Input();
  submit = new Button();

  constructor() {
    [this.email, this.password, this.submit].forEach(component => component.setMediator(this));
  }

  notify(sender: Component, event: string): void {
    if (event === 'change') {
      this.submit.disabled = !this.email.value || !this.password.value;
    }
    if (sender === this.submit && event === 'click') {
      console.log(`Регистрация: ${this.email.value}`);
    }
  }
}

const form = new SignUpForm();
form.email.type('max@example.com');
console.log(form.submit.disabled); // true

form.password.type('secret');
console.log(form.submit.disabled); // false

form.submit.click(); /* Регистрация: max@example.com */
```
Поля и кнопка *ничего не знают друг о друге* — только *о посреднике*. Их можно *переиспользовать в другой форме* с *другими правилами*.

Посредник и Наблюдатель *оба уменьшают связанность*, но Наблюдатель *рассылает оповещения всем подписчикам*, а Посредник *собирает логику взаимодействия в одном месте*. Посредника *часто реализуют через Наблюдателя*: компоненты *публикуют события*, а посредник на них *подписан*.

*Опасность* — та же, что у [*Фасада*](#фасад): посредник, который *знает обо всём*, *разрастается в божественный объект*.

### Посетитель

**Посетитель** (англ. `Visitor`) — поведенческий паттерн, позволяющий *добавлять новые операции над объектами*, *не изменяя их классы*: операция *выносится в отдельный объект-посетитель*, а объекты лишь «*принимают*» его.

Возьмём *файлы и папки* из примера с [*Компоновщиком*](#компоновщик). Нужно *посчитать размер*, *найти файлы по расширению*, *выгрузить дерево в JSON* — и *не хочется* каждый раз *дописывать методы* в `FileItem` и `Folder`. Добавим в них *единственный метод* `accept`, а *операции вынесем в посетителей*.
```ts
interface IVisitor<R> {
  visitFile(file: FileItem): R;
  visitFolder(folder: Folder): R;
}

interface IFileSystemItem {
  name: string;
  accept<R>(visitor: IVisitor<R>): R;
}

class FileItem implements IFileSystemItem {
  name: string;
  size: number;

  constructor(name: string, size: number) {
    this.name = name;
    this.size = size;
  }

  accept<R>(visitor: IVisitor<R>): R {
    return visitor.visitFile(this);
  }
}

class Folder implements IFileSystemItem {
  name: string;
  children: IFileSystemItem[];

  constructor(name: string, children: IFileSystemItem[] = []) {
    this.name = name;
    this.children = children;
  }

  accept<R>(visitor: IVisitor<R>): R {
    return visitor.visitFolder(this);
  }
}

/* операция 1: размер */
class SizeVisitor implements IVisitor<number> {
  visitFile(file: FileItem): number {
    return file.size;
  }

  visitFolder(folder: Folder): number {
    return folder.children.reduce((sum, child) => sum + child.accept(this), 0);
  }
}

/* операция 2: поиск по расширению */
class FindVisitor implements IVisitor<string[]> {
  private extension: string;

  constructor(extension: string) {
    this.extension = extension;
  }

  visitFile(file: FileItem): string[] {
    return file.name.endsWith(this.extension) ? [file.name] : [];
  }

  visitFolder(folder: Folder): string[] {
    return folder.children.flatMap(child => child.accept(this));
  }
}

const project = new Folder('project', [
  new FileItem('README.md', 2),
  new Folder('src', [new FileItem('index.ts', 10), new FileItem('utils.ts', 5)]),
]);

console.log(project.accept(new SizeVisitor())); // 17
console.log(project.accept(new FindVisitor('.ts'))); // [ 'index.ts', 'utils.ts' ]
```
Метод `accept` вызывает у посетителя *метод, соответствующий своему классу*: файл — `visitFile`, папка — `visitFolder`. Так выбор кода *зависит сразу от двух типов* — посетителя и элемента. Это называют **двойной диспетчеризацией** (англ. `double dispatch`).

*Цена паттерна*: *новую операцию* добавить *легко* — это *новый посетитель*, а вот *новый тип элемента* — *трудно*: придётся *дописать метод во все существующие посетители*. Поэтому Посетитель хорош, когда *типы элементов меняются редко*, а *операции — часто*.

Посетители *обходят синтаксические деревья* в *компиляторах* и *линтерах*. Например, *правило ESLint* возвращает объект с методами, которые ESLint вызывает, чтобы «*посетить*» узлы при обходе *абстрактного синтаксического дерева* ([*документация ESLint*](https://eslint.org/docs/latest/extend/custom-rules)).
```js
module.exports = {
  create(context) {
    return {
      /* вызывается для каждого узла типа Identifier */
      Identifier(node) {
        if (node.name === 'foo') {
          context.report({ node, message: 'Не называйте переменные foo' });
        }
      },
    };
  },
};
```

### Интерпретатор

**Интерпретатор** (англ. `Interpreter`) — поведенческий паттерн, *описывающий грамматику простого языка классами* (по классу на правило) и *вычисляющий выражения* этого языка, *обходя дерево* из объектов этих классов.

Пусть нужно *считать формулы цены*: `price * count + delivery`. Каждое правило грамматики — *число*, *переменная*, *сложение*, *умножение* — становится *классом с методом* `interpret`.
```ts
type Context = Record<string, number>;

interface IExpression {
  interpret(context: Context): number;
}

class NumberLiteral implements IExpression {
  private value: number;

  constructor(value: number) {
    this.value = value;
  }

  interpret(): number {
    return this.value;
  }
}

class Variable implements IExpression {
  private name: string;

  constructor(name: string) {
    this.name = name;
  }

  interpret(context: Context): number {
    return context[this.name] ?? 0;
  }
}

class Plus implements IExpression {
  private left: IExpression;
  private right: IExpression;

  constructor(left: IExpression, right: IExpression) {
    this.left = left;
    this.right = right;
  }

  interpret(context: Context): number {
    return this.left.interpret(context) + this.right.interpret(context);
  }
}

class Times implements IExpression {
  private left: IExpression;
  private right: IExpression;

  constructor(left: IExpression, right: IExpression) {
    this.left = left;
    this.right = right;
  }

  interpret(context: Context): number {
    return this.left.interpret(context) * this.right.interpret(context);
  }
}

/* price * count + delivery */
const total = new Plus(
  new Times(new Variable('price'), new Variable('count')),
  new Variable('delivery'),
);

console.log(total.interpret({ price: 10, count: 3, delivery: 5 })); // 35
console.log(new Plus(new NumberLiteral(2), new NumberLiteral(2)).interpret({})); // 4
```
Превратить строку `price * count + delivery` в *дерево объектов* — *отдельная задача синтаксического анализатора* (парсера). Интерпретатор отвечает за то, *что делать с уже готовым деревом*.

*Дерево выражений* — это [*Компоновщик*](#компоновщик): `Plus` и `Times` *содержат другие выражения*. Если операций над деревом *много* (вычислить, напечатать, упростить), их выносят в [*посетителей*](#посетитель).

Паттерн подходит для *простых языков*: формулы, правила фильтрации, маленькие языки запросов. Для *сложных грамматик* классов становится *слишком много*, и берут *генераторы парсеров* или *готовые движки*.

## Похожие паттерны

Некоторые паттерны *устроены почти одинаково*, и на *собеседованиях* часто просят *объяснить разницу*.

Паттерны | Что общего | Чем отличаются
:--: | :--: | :--:
*Фабричный метод* и *Абстрактная фабрика* | *создают объекты*, *скрывая конкретный класс* | *метод создаёт один продукт*, *фабрика* — *семейство связанных продуктов*
*Адаптер* и *Фасад* | *оборачивают чужой код* | *Адаптер подгоняет существующий интерфейс* под ожидаемый, *Фасад придумывает новый*, *более простой*
*Адаптер* и *Декоратор* | *обёртки* | *Адаптер меняет интерфейс*, *Декоратор сохраняет его* и *добавляет поведение*
*Декоратор* и *Заместитель* | *обёртка с тем же интерфейсом* | *Декоратор добавляет обязанности*, *Заместитель управляет доступом* к объекту
*Адаптер* и *Мост* | *связывают абстракцию с реализацией* | *Адаптер стыкует уже написанное*, *Мост закладывают заранее*
*Стратегия* и *Состояние* | *контекст делегирует работу вложенному объекту* | *стратегию выбирает клиент*, *состояния переключают друг друга сами*
*Стратегия* и *Шаблонный метод* | *меняют часть алгоритма* | *Стратегия* — *композицией*, *во время выполнения*; *Шаблонный метод* — *наследованием*
*Команда* и *Хранитель* | *помогают сделать отмену* | *Команда выполняет обратное действие*, *Хранитель восстанавливает сохранённое состояние*
*Наблюдатель* и *Посредник* | *уменьшают связанность* | *Наблюдатель рассылает оповещения подписчикам*, *Посредник собирает логику взаимодействия в одном месте*
*Итератор* и *Посетитель* | *проходят по структуре* | *Итератор отдаёт элементы по одному*, *Посетитель выполняет над ними операцию*, *выбирая код по типу элемента*
