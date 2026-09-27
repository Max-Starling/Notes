# Паттерны проектирования

- [Порождающие](#порождающие)
- [Структурные](#структурные)
- [Поведенческие](#поведенческие)

## Порождающие

**Порождающие паттерны** отвечают за удобное и безопасное создание новых объектов или групп объектов.

- [Одиночка (Singleton)](#одиночка)
- [Внедрение зависимостей (Dependency Injection)](#внедрение-зависимостей)
- [Фабричный метод (Factory Method)](#фабричный-метод)
- [Абстрактная фабрика (Abstract Factory)](#абстрактная-фабрика)
- [Строитель (Builder)](#строитель)
- [Прототип (Prototype)](#прототип)
### Одиночка

**Одиночка** (Singleton) — порождающий паттерн, гарантирующий существование только одного объекта определённого класса. Одиночка позволяет обратиться к этому объекту из любого места в приложении.

Часто считается антипаттерном, поскольку нарушает модульность кода (похож на глобальную переменную).

Для реализации одиночки пишется класс с приватным конструктором; статическим полем, хранящим экземпляр (instance) класса, и статическим методом, создающим и сохраняющим экземпляр при первом обращении и возвращающим при последующих.
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

### Внедрение зависимостей

**Внедрение зависимостей** (Dependency Injection) — порождающий паттерн, реализующий *принцип инверсии управления* (IoC). 

*Внедрение зависимостей* позволяет полностью вынести создание объектов, от которых зависит некоторый класс, за пределы этого класса, предоставляя эти объекты другим путём.

*Внедрение зависимостей* вводит следующие 3 понятия
* **Клиент** — класс, зависимый (dependent) от *Сервиса*.
* **Сервис** — класс, предоставляющий свой объект *Клиенту*.
* **Инжектор** — класс, внедряющий объект *Сервиса* в *Клиент*.

Возьмём пример из раздела с *принципом инверсии управления* и улучшим его при помощи *принципа инверсии зависимостей* (DIP), заменив классы на интерфейсы, где это возможно.
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
Паттерн "Фабрика" отвечает за создание объекта класса `Salary`, но само создание (пусть и при помощи фабрики) всё ещё происходит в классе `Employee`.

В данном примере класс `Employee` — *Клиент*, `Salary` — *Сервис*.  
Рассмотрим 3 способа, как можно написать *Инжектор*, который назовём `EmployeeInjector`:
* *Внедрение в конструктор*
* *Внедрение свойства*
* *Внедрение метода*

**Внедрение в конструктор** (Constructor Injection): зависимость предоставляется через параметры конструктора (`constructor(salary:  ISalary)`).
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

**Внедрение свойства** (Property Injection): зависимость предоставляется через публичное свойство (`emp.salary`).
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
Поскольку начальное значение `Salary` не задано в примере, пришлось сделать его необязательным (`?:`) в интерфейсе `IEmployee` и классе `Employee` во избежание ошибок инициализации. Задать начальное значение *внедрением свойства* нельзя. 

**Внедрение метода** (Method Injection): зависимость предоставляется через метод интерфейса или класса (`setSalary(salary)`).
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
Пусть есть два модуля A и B, модуль B использует модуль A.  
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
  B: B(A);
};
```

**IoC-контейнер, DI-контейнер** — *фреймворк*, реализующий *автоматическое внедрение зависимостей*. Он управляет созданием объектов и их временем жизни, а также внедряет зависимости в класс.

*IoC-контейнер* создаёт объект какого-то класса и встраивает все объекты, от которых зависит класс, в конструктор, свойство или метод класса во время выполнения (at run-time) и утилизирует (dispose), когда это необходимо.

*IoC-контейнер* предоставляет следующий жизненный цикл внедряемых зависимостей (DI lifecycle):
* Регистрация (Register)
* Разрешение (Resolve)
* Ликвидация (Dispose)

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

**Фабричный метод** (Factory Method) — порождающий паттерн, определяющий интерфейс для создания объекта некоторого класса, при этом решение о том, какой класс будет у объекта, принимается в дочерних классах.

Связываем объекты разных классов в одно семейство, характеризующее их общие черты.

Голуби и утки являются летающими птицами. Пусть их классы `Dove` и `Duck` реализуют общий интерфейс `IFlyingBird`. 
```ts
interface IFlyingBird { /* ... */ }
class Dove implements IFlyingBird { /* ... */ }
class Duck implements IFlyingBird { /* ... */ }
```
Описываем абстрактный фабричный метод, создающий летающих птиц, и реализуем его в конкретных классах (фабриках).
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
const dove = DoveFactory.create();

const duckFactory = new DuckFactory();
const duck = DuckFactory.create();

const flyingBirds: IFlyingBird[] = [dove, duck];
```
Использовать фабрики очень удобно, поскольку мы избегаем использования оператора `new` напрямую, инкапсулируя эту логику внутри метода.
Это позволяет также инкапсулировать внутри метода некоторые общие свойства для какой-то подгруппы объектов, не дублируя при этом код и не создавая новый класс для них (не считая самой фабрики).
```ts
class BlackDoveFactory implements IFlyingBirdFactory {
  create(): IFlyingBird {
    return new Dove({ color: 'black', size: 'large' });
  }
}
```

Сделать что-то похожее можно при помощи функций, но это уже нельзя назвать фабричным методом.
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

**Абстрактная фабрика** (Abstract Factory) — порождающий паттерн, позволяющий создавать семейство концептуально связанных объектов, не привязываясь к классам этих объектов.

Интерфейс `IFlyingBirdFactory` из примеров выше по сути описывает абстрактную фабрику с одним абстрактным методом. Рассмотрим случай, когда абстрактных методов несколько.

Пусть у нас есть люди, собаки и кошки. Выделим кое-что, что объединяет их всех — разделение пола на мужской и женский.
```ts
interface ICreatureFactory {
  createMale(): Male {}
  createFemale(): Female {}
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

Пусть у нас есть набор инструментов: топор, кирка, лопата. Инструменты могут состоять из разных материалов, поэтому каждый из них должен быть абстрактным (чтобы нельзя было создать сущность без материала, то есть представлен либо абстрактным классом (если содержит реализации методов), либо интерфейсом).
```ts
abstract class Axe { /* ... */ }
abstract class Pickaxe { /* ... */ }
abstract class Shovel { /* ... */ }
```
Пусть материалами будут камень и железо.
```ts
class StoneAxe extends Axe { /* ... */ }
class StonePickaxe extends Pickaxe { /* ... */ }
class StoneShovel extends Shovel { /* ... */ }
class IronAxe extends Axe { /* ... */ }
class IronPickaxe extends Pickaxe { /* ... */ }
class IronShovel extends Shovel { /* ... */ }
```
Создаём абстрактную фабрику, с помощью которой мы сможем создавать инструменты из нужного материала.
```ts
interface IToolFactory {
  createAxe(): Axe {}
  createPickaxe(): Pickaxe {}
  createShovel(): Shovel {}
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
Такой подход очень расширяемый. Если в будущем мы захотим добавить новый материал для набора инструментов (например, сталь или золото), можно будет это очень просто реализовать созданием новой фабрики.

### Строитель

**Строитель** (Builder) — порождающий паттерн, предоставляющий возможность поэтапного (пошагового) создания составных объектов.

Будем делать пиццу.  
Пусть пицца может быть разных размеров, на разном тесте и с разными ингредиентами.
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
Его наследники будут переопределять метод `build`, в котором определяется последовательность добавления ингредиентов.
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
Описываем *директора*, который знает, как работать со строителем, чтобы получить готовую пиццу (нужно собрать пиццу и готовить её в течение 3 секунд).
```ts
class Director {
  async cookMargherita(size: Size): Promise<Pizza> {
    const margheritaBuilder = new MargheritaPizzaBuilder(size);
    margheritaBuilder.build();
    const pizza = await margheritaBuilder.cook(3000);
    console.log('Margherita is ready!');
    return pizza;
  }
}

const director = new Director();
director.cookMargherita(Size.Medium).then(pizza => console.log(pizza));
```

Для полноты картины рассмотрим ещё один небольшой пример: построение ландшафта. Здесь `add`-методы будут реализованы не в родительском абстрактном строителе, а в конкретном дочернем.
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

### Прототип

**Прототип** (Prototype) — порождающий паттерн, позволяющий копировать объект любой сложности без привязки к его конкретному классу.

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
    return Object.assign({}, this);
  }
}

const article = new Article('Prototype Pattern');
const copy = article.clone();
console.log(article === copy); // false
```
Есть [много способов клонирования объектов](https://github.com/Max-Starling/Notes/blob/master/JavaScript.md#клонирование-объектов) реализовать функцию `clone()`. Каждый из них имеет свои преимущества и недостатки по сравнению с остальными.  

## Структурные

## Поведенческие
