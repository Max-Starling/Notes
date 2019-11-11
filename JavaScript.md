- [Основы JavaScript](#основы-javascript)
  - [Переменные и их типы](#переменные-и-их-типы)
  - [Замыкание и область видимости](#замыкание-и-область-видимости)
  - [Всплытие](#всплытие)
- [Операторы](#операторы)
  - [Оператор typeof](#оператор-typeof)
  - [Оператор void](#оператор-void)
  - [Operator запятая](#оператор-запятая)
- [Объекты](#объекты)
  - [Перечисление свойств объекта](#перечисление-свойств-объекта)
  - [Является ли объектом](#является-ли-объектом)
  - [Клонирование объектов](#клонирование-объектов)
  - [Сравнение объектов](#сравнение-объектов)
- [Функции](#функции)
- [Встроенные объекты](#встроенные-объекты)
  - [Error и его наследники](#error-и-его-наследники)
  - [Promise](#promise)
- [Спецификация](#спецификация)
  - [Функции под капотом JavaScript](#функции-под-капотом-javascript)

# Основы JavaScript
- [Переменные и их типы](#переменные-и-их-типы)
- [Замыкание и область видимости](#замыкание-и-область-видимости)
- [Всплытие](#всплытие)

## Переменные и их типы

**Переменная** состоит из *имени* и *выделенной* под это имя *области памяти*.

**Имя переменной** может содержать *буквы, цифры, $, _*.  
*Регистр* важен (`ALL` и `all` - разные переменные).  

*Константы* принято называть в *UPPERCASE*: `ANY_NAME`.  

### 5 примитивных типов и объект
* **number** `1`, `2.17`, `NaN`, `Infinity`
* **string** `'str'`, `"str"`
* **boolean** `true`, `false`
* **null** `null`
* **undefined** `undefined`
* **object** `{}`

Значение **null** *не является* «*ссылкой на нулевой адрес/объект*» или чем-то подобным.  
Значение **null** *специальное* и имеет смысл «*ничего*» или «*значение неизвестно*».  
Значение **undefined** означает «*переменная не присвоена*».  

### Способы объявить переменную

**Блок** — всё, что лежит внутри фигурных скобок `{}`.  

Например, *конструкции* `if-else`, `while`, `switch`, `try-catch`, *циклы* `for`, *функции содержат блоки*. Тем не менее *можно использовать блоки* и *без этих конструкций*.

**Переменная let** имеет *блочную область видимости*, то есть её *нельзя использовать за пределами блока*, в котором она *объявлена*.
```js
{
  let foo = 1;
  foo = 7;
  console.log(foo); // 7;
}
console.log(foo); // ReferenceError: foo is not defined
```
```js
for (let i = 0; i < 10; i++) {
  /* ... */
}
console.log(i); // ReferenceError: i is not defined
```
*Переменную let* *нельзя использовать* до её *инициализации*.
```js
console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
let foo = 'notes';
```
*Переменную let* *нельзя объявить дважды* с одним и *тем же именем*.
```js
let foo = 1;
let foo = 7; // SyntaxError: Identifier 'foo' has already been declared
```

**Переменная const** имеет *те же свойства*, что и *переменная let*, но вдобавок *её значение нельзя переопределить*.
```js
const foo = 1;
foo = 7; // TypeError: Assignment to constant variable.
```

**Переменная var** является *предком переменных let* и *const* и *не обладает их ограничениями*.

*Переменная var* *не имеет блочной области видимости*.
```js
if (true) {
  var foo = 1;
}
console.log(foo);
```
```js
for (var i = 0; i < 10; i++) {
  /* ... */
}
console.log(i); // 10
```
*Переменную var* *можно использовать до* её *инициализации*. Такое поведение называется *всплытием*. *Значение до инициализации*: `undefined`.
```js
console.log(foo); // undefined
var = 'notes';
console.log(foo); // 'notes'
```
*Переменную var* *можно объявить дважды* с *тем же именем*.
```js
var foo = 1;
console.log(foo); // 1
var foo = 7;
console.log(foo); // 7
```

Глобальная переменная

## Замыкание и область видимости

**Область видимости** (Scope) определяет видимость (visibility), доступность (accessibility) переменных. 

В JavaScript есть *два типа области видимости*: **глобальная** (Global Scope) и **локальная** (Local Scope).

*Каждая функция создаёт новую локальную область видимости*.  
*Переменные*, *определённые внутри функции*, *недоступны извне*.
```js
const fn = () => {
  var foo = 1;
}
console.log(foo); // ReferenceError: foo is not defined
fn();
console.log(foo); // ReferenceError: foo is not defined
```

<!-- Если переменная не объявлена в одной функции, то она принадлежит глобальной области видимости.  
В браузере за глобальную область видимости отвечает объект `Window`. -->

**Замыкание** (Closure) — функция вместе с ссылками на её окружающее состояние - лексическое окружение (Lexical Environment).   

Замыкание дает доступ к области видимости внешней функции из внутренней функции.   

В JavaScript замыкания создаются автоматически при создании функции.

## Всплытие

В JavaScript переменная может быть использована перед тем, как она была определена (declared) в коде.

**Всплытие** (hoising) — поведение JavaScript, помещающее объявления (declarations) вверх текущей области видимости (current scope).

```js
foo = 3;
console.log(foo); // выведет 3
var foo;
```

Всплывают (hoist) сами объявления (declarations), но не присвоенные им значения (initializations).  
Это связано с тем, что переменная создаётся в области видимости на первом этапе интерпретации, а инициализируется значением на втором.

```js
console.log(foo); // выведет undefined
var foo = 3;
```

Всплывают переменные `var`, а `let` и `const` не всплывают, поскольку возникает `ReferenceError`.

```js
console.log(bar); // ReferenceError: Cannot access 'bar' before initialization
let bar = 3;
```

# Операторы
- [Оператор typeof](#оператор-typeof)
- [Оператор void](#оператор-void)
- [Operator запятая](#оператор-запятая)

## Оператор typeof

**Оператор typeof** возвращает *тип аргумента*.  
*Результатом* действия *оператора* является *строка*.  

В JavaScript *массивы* и *функции* так же являются *объектами*.  
Но оператор *typeof* имеет *тип function* для *удобства*.  
То, что *null* считается *объектом*, является *врождённой ошибкой* языка.  
 ```javascript
typeof undefined // "undefined"
typeof 0 // "number"
typeof true // "boolean"
typeof "foo" // "string"
typeof {} // "object"
typeof [] // "object"
typeof null // "object" (врождённая ошибка языка)
typeof function(){} // "function"
```

## Оператор void

**Оператор void** — *унарный* оператор, *выполнящий принимаемое выражение* и *возвращающий undefined*.  
Его можно использовать *со скобками и без*:
```javascript
void 3 // undefined
void(3) // undefined
void(3 == '3') // undefined
void 3 == '3';   // undefined == '3' --> false
```
*Преобразование Function Declaration* в *Function Expression* для *самовызывающихся* функций (IIFE):
```js
(function() { /* ... */ })()
// эквивалентно
void function(){ /* ... */ }()
// дважды SyntaxError (название функции и круглые скобки), если
function(){ /* ... */ }()
```
*Избегание явного* использования *undefined*, а также *краткий способ* его записать (иногда можно встретить в *минифицированном* коде):
```js
if (field === void 0)
```
Иногда нужно просто *выполнить функцию*, *ничего не возвращая*, но *стрелочная* функция в своей *краткой форме всегда возвращает результат выражения*, что может иногда приводить к *неожиданным последствиям*.  
Можно себя *обезопасить*:
```js
const onClick = () => void this.setState({ isClicked: true });
```
Здесь стоит обратить внимание, что код ниже выдаст ошибку: приоритет `=>` ниже, чем у `void`.
```js
const onClick = void () => this.setState({ isClicked: true }); // SyntaxError: Malformed arrow function parameter list
```
Приоритет оператор

## Оператор запятая

**Оператор запятая** (comma operator) *выполняет каждый из его операндов слева направо* и *возвращает значение последнего*. *Операнды* могут быть представлены *выражениями*.

*Оператор запятая* имеет *самый низкий приоритет среди операторов*, что может стать *причиной ошибок* при неправильном использовании.
```js
let foo = 2, 3; // SyntaxError: Unexpected number
```
Ошибка выше связана с тем, что *оператор присваивания* `=` *выполняется раньше*, чем *оператор запятая*, поскольку имеет *более высокий приоритет*. Впереди стоит `let`, применяющийся *ко всем операндам оператора запятая*: `let foo = 2` и `let 3` (*название переменной не может быть числом*). 

*Избежать ошибки* можно при помощи *оператора группировки* `( )`, имеющего *самый высокий приоритет среди операторов*.
```js
let bar = (2, 3);
console.log(bar); // 3 (последний операнд)
```
К слову, пример ниже отработает без ошибок. В первом операнде `foo = 2` происходит присвоение значения глобальной переменной, во втором просто возвращается `3`.
```js
foo = 2, 3;
console.log(foo); // 2
```

Не так часто удаётся *применить оператор запятая*, но иногда он может быть *полезен*.  
Например, можно *временно добавить* в стрелочную функцию *логирование*, если нужно что-то быстро посмотреть.
```js
const getDataType = data => typeof data;
// заменяем на
const getDataType = data => (console.log(data), typeof data);

getDataType('notes') // можно увидеть значение 'notes' в консоли
```
Другой пример: *выполнить операцию* над чем-то и сразу *вернуть* её *результат*.
```js
const array = ['n', 'o', 't', 'e'];
console.log(array.push('s')) // 5 (вернулась длина массива после добавления элемента)

// хотим вернуть новый массив:
const array = ['n', 'o', 't', 'e'];
const push = (arr, val) => (arr.push(val), arr);
console.log(push(array, 's')); // ['n', 'o', 't', 'e', 's']
```
Здесь стоит ещё раз отметить *важность оператора группировки*.
```js
const push = (arr, val) => arr.push(val), arr; // SyntaxError: Missing initializer in const declaration
```
Код выше воспринимается интерпретатором как `const push = /* ... */` и `const arr` (*константы обязаны иметь какое-то значение при создании*). С `let` ошибки бы не было.

# Объекты
- [Перечисление свойств объекта](#перечисление-свойств-объекта)
- [Является ли объектом](#является-ли-объектом)
- [Клонирование объектов](#клонирование-объектов)
  - [Плохие способы клонирования](#плохие-способы-клонирования)
  - [Неглубокое клонирование](#неглубокое-клонирование)
  - [Глубокое клонирование](#глубокое-клонирование)
- [Сравнение объектов](#сравнение-объектов)
  - [Операторы сравнения](#операторы-сравнения)
  - [Неглубокое сравнение](#неглубокое-сравнение)
  - [Глубокое сравнение](#глубокое-сравнение)

## Перечисление свойств объекта

Цикл `for...in` *перебирает все несимвольные* (non-Symbol) *перечисляемые свойства* (enumerable properties) объекта, включая свойства из цепочки прототипов (prototype chain).

Метод `Object.keys(obj)` *возвращает массив* из *собственных* (own) *перечисляемых свойств* объекта `obj` в том же *порядке*, в котором они обходились бы *циклом* `for..in`. Поскольку свойства собственные, цепочка прототипов не включается в перечисление.

Метод `Object.getOwnPropertyNames(o)` *возвращает массив* названий всех собственных свойств объекта `obj`.

## Является ли объектом
```js
const isObject = value => typeof value === 'object' && !Array.isArray(value) && value !== null;
```
Такая реализация обусловлена следующим поведением [оператора typeof](#оператор-typeof):
```js
typeof({}) === 'object' // true
typeof([]) === 'object' // true
typeof(() => {}) === 'function' // true
typeof(null) === 'object' // true
```

## Клонирование объектов
В JavaScript *объекты* и *массивы* (тоже являющиеся объектами) *передаются по ссылке* (by reference). 

Существует множество способов *клонировать объект* (object clone), среди которых есть плохие и хорошие.

### Плохие способы клонирования

**Клонирование через оператор присваивания** `=` означает *запись ссылки на объект* в *новую переменную*. Если *изменить* эту *переменную* (не заменить полностью, а изменить поля), то *изменится* и *оригинальный* объект.
```js
const obj = { a: 7 };
const copy = obj; // передача ссылки
console.log(foo === copy); // true
copy.a = 3;
console.log(foo.a) // 3;
```
Следует *избегать поведения*, при котором *изменение копии влияет на оригинальный объект*.

**Клонирование через Object.create()** не имеет смысла, поскольку `Object.create(proto)` создаёт новый объект, используя существующий объект `proto` в качество прототипа для нового.
```js
const obj = { a: 7 };
const copy = Object.create(obj);
console.log(copy); // {}
console.log(copy.a); // 7 (не найдено в самом объекте, но найдено в прототипе)
console.log(copy.__proto__); // { a: 7 }
obj.hasOwnProperty('a'); // true
copy.hasOwnProperty('a'); // false
```

**Клонирование в цикле for..in** означает копирование не только собственных (own) свойств объекта, но и свойств прототипа. Само свойство, отвечающее за прототип, не копируется.
```js
const cloneObject = (obj) => {
  const copy = {};
  for (let key in obj) {
    copy[key] = obj[key];
  }
  return copy;
};

const prototype = { prop: 'prototype property' };
const obj = { field: 'value' };
obj.__proto__ = prototype; // так делать не желательно, но для примера можно
console.log(obj); // { field: 'value' }
console.log(obj.prop); // 'prototype property'

const copy = cloneObject(obj);
console.log(copy); // { field: "value", prop: "prototype property" }
```

**Клонирование через eval** является самым худшим вариантом. 
```js
const cloneObject = obj => eval(uneval(obj));

const obj = { /* ... */ };
const copy = cloneObject(obj);
```

Во-первых, использование `eval` - это плохо.
> Eval is not evil. Using eval poorly is. 

Во-вторых, требуется поддержки функции `uneval`, имеющаяся только у Firefox, но даже в нём это может не сработать из-за политики безопастности контента (Content Security Policy): `EvalError: call to eval() blocked by CSP`.

### Неглубокое клонирование

**Неглубокое клонирование** (shallow clone) подразумевает копирование неглубоких свойств (shallow properties) оригинального объекта в новый объект. Если свойство само является объектом (`prop: {}`), то оно передаётся по ссылке (оригинальное и скопированное свойство ссылаются на один объект). 

*Неглубокое свойство*  `obj.prop`, *глубокое свойство*: `obj.prop.nestedProp`. Глубокие свойства (deep properties) объекта клонируются автоматически, поскольку содержатся в объектах, являющихся неглубокими свойствами.

Если вложенные объекты отсутствуют, неглубокое клонирование является оптимальным.

**Клонирование через Object.assign()**.
```js
const cloneObject = obj => Object.assign({}, obj);

const obj = {
  field: {
    nestedField: 'notes',
  },
};
const copy = cloneObject(obj);
console.log(obj === copy); // false
copy.field.nestedField = 'changed';
console.log(obj.field.nestedField); // 'changed' (изменение клона повлияло на оригинал)
console.log(obj.field === copy.field); // true (ссылаются на один объект)
```

**Клонирование через Spread-оператор** `...` работает аналогично `Object.assign()`.
```js
const cloneObject = obj => ({ ...obj });
```

**Клонирование при помощи Object.keys()** подразумевает перебор и копирование собственных свйоств оригинального объекта.
```js
const cloneObject = (obj) => {
  const copy = {};
  Object.keys(obj).forEach((key) => {
    copy[key] = obj[key];
  });
  return copy;
};
```
В случае, если `Object.assign` и `...` не поддерживаются, можно написать полифилл с использованием `Object.keys`.

Аналогичного `Object.keys` поведения можно добиться от *клонирования в цикле for..in*, добавив в нём дополнительную проверку на принадлежность свойства.
```js
for (let key in obj) {
 if (obj.hasOwnProperty(key)) { /* ... */ }
}
```

Готовым решением неглубокого копирования является функция `_.clone(value)` из библиотеки `lodash`.

### Глубокое клонирование
**Глубокое клонирование** (deep clone) подразумевает копирование свойств на всех уровнях, то есть на каждом уровне вложенности вместо передаче по ссылке создаётся новый объект с теми же свойствами.

**Клонирование через JSON-сериализацию** очень популярно благодаря простоте, скорости работы (JSON-сериализация реализована и оптимизирована браузером) и возможности глубокого клонирования. 
```js
const cloneObject = obj => JSON.parse(JSON.stringify(obj));

const obj = {
  field: {
    nestedField: 'notes',
  },
};
const copy = cloneObject(obj);
console.log(obj === copy); // false
copy.field.nestedField = 'changed';
console.log(obj.field.nestedField); // 'notes' (изменение клона не повлияло на оригинал)
console.log(obj.field === copy.field); // false
```
Недостаток: утрата некоторых данных (data loss), а точнее тех данных, которые не поддерживаются в JSON.
```js
const cloneObject = obj => JSON.parse(JSON.stringify(obj));

const copy = cloneObject({
  a: () => {}, // поле опускается
  b: Infinity, // значение заменяется на null
  c: NaN, // значение заменяется на null
  d: new Date(), // превратится в строку
  e: undefined, // поле опускается
  а: Symbol(''), // заменяется на null
});
console.log(copy); // { b: null, c: null, d: "XXXX-XX-XXTXX:XX:XX.XXXZ" }
```
Более того, некоторые данные вообще не могут быть преобразованы в JSON. Например, циклическая ссылка (circular reference) или `BigInt` вызовут исключение, которое нужно будет где-то обработать.
```js
JSON.parse({ a: () => {} }); // SyntaxError: Unexpected token o in JSON at position 1
```

**Клонирование через V8-сериализацию** в Node.js (экспериментальная функциональность).
```js
const v8 = require('v8');
const clone = obj => v8.deserialize(v8.serialize(obj));
```

Пример глубокого клонирования конкретного объекта без всяких функций.
```js
const user = {
  email: 'user@email.com',
  settings: { theme: 'dark' },
  comments: ['Hi!', 'Agree'].
};

const clone = {
  ...user,
  settings: { ...user.settings },
  comments: [...user.comments],
};
```
Такое поведение можно было бы реализовать рекурсивной функцией `cloneObject`. Например,
```js
const isObject = value => typeof value === 'object' && !Array.isArray(value) && value !== null;

const cloneObject = (obj) => {
  let copy = {};
  for (let prop in obj) {
    if (obj.hasOwnProperty(prop)) {
      const value = obj[prop];
      /* если значение является объектом, рекурсивно копируем его свойства */
      copy[prop] = isObject(value) ? cloneObject(value) : value;
    }
  }
  return copy;
}

const foo = { g: { h: 'h' } };
condt bar = cloneObject(foo); // { g: { h: 'h' } }
console.log(foo === bar); // false
console.log(foo.g === bar.g); // false
```
Эта функция не может обработать все случаи. Например, отдельно следует описывать работу с массивами и функциями, а также с циклическими ссылками, выбрасывающими исключения `«too much recursion»` и подобные.
```js
const copy = {};
copy.proto = copy; // циклическая ссылка
console.log(copy); // { proto: {...} }
console.log(copy.proto); // { proto: {...} }
console.log(copy.proto.proto.proto); // { proto: {...} }
```

Таким образом, для глубокого клонирования лучше всего использовать готовые решения. Такими являются  `_.cloneDeep(obj)` в библиотеке `lodash`, `jQuery.extend(true, {}, obj)`, `angular.clone(obj)` и другие.

## Сравнение объектов

### Операторы сравнения

В JavaScript есть два оператора сравнения: нестрогий (abstract) `==` и строгий (strict) `===`.

При сравнении объектов `A` и `B` оба оператора вернут `true` лишь в том случае, если ссылки `A` и `B` будут указывать на один и тот же объект.
```js
const a = {};
const b = {};
const c = a;
console.log(a == b, a === b); // false false
console.log(a == c, a === c); // true true
```

### Неглубокое сравнение

**Неглубокое сравнение** (shallow comparison) объектов A и B подразумевает проверку на строгое равенство (`===`) *только неглубоких свойств* (shallow properties) объектов (*проверка не рекурсивна*). Если все неглубокие свойства совпадают, то объекты считаются эквивалентными (shallow equal). Если `A === B`, то A и B по определению считаются эквивалентными, поскольку ссылаются на один объект.

*Неглубокое свойство*  `obj.prop`, *глубокое свойство*: `obj.prop.nestedProp`.

Примеры *неглубокого сравнения*.
* `{ a: 1 }` и `{ a: 1 }` считаются эквивалентными, поскольку их неглубокие свойства `a` совпадают (`1 === 1`).
* `{ a: {}}` и `{ a: {}}` считаются не эквивалентными, поскольку их неглубокие свойства `a` представленны объектами с разными ссылками (`{} !== {}`).

Реализация *неглубокого сравнения* для *любых значений*.
```js
const isObject = value => typeof value === 'object' && value !== null;

const compareObjects = (A, B) => {
  const keysA = Object.keys(A);
  const keysB = Object.keys(B);
 
  /* Если количество свойств не совпадает, то объекты не эквивалентны. */
  if (keysA.length !== keysB.length) {
    return false;
  }
 
  /* Рассматриваются свойства объекта A в объекте B. Если объект B не имеет хотя бы одно 
  собственное (own) свойство или значения свойств не строго равны, то объекты не эквивалентны. */
  return !keysA.some(key => !B.hasOwnProperty(key) || A[key] !== B[key]);
};

const shallowEqual = (A, B) => {
  /* Если значения A и B проходят строгое равенство, то они эквивалентны. */
  if (A === B) {
    return true;
  }
 
  /* Если оба значения равны NaN, то они эквивалентны. */
  if ([A, B].every(Number.isNaN)) {
    return true;
  }
  
  /* Eсли A и/или B не являются объектами, то они не эквивалентны,  
  поскольку не прошли проверки выше. */
  if (![A, B].every(isObject)) {
    return false;
  }
  
  /* Остался случай, когда A и B — объекты */
  return compareObjects(A, B);
};

const a = { field: 1 };
const b = { field: 2 };
const c = { field: { field: 1 } };
const d = { field: { field: 1 } };

console.log(shallowEqual(1, 1)); // true
console.log(shallowEqual(1, 2)); // false
console.log(shallowEqual(null, null)); // true
console.log(shallowEqual(NaN, NaN)); // true
console.log(shallowEqual([], [])); // true
console.log(shallowEqual([1], [2])); // false
console.log(shallowEqual({}, {})); // true
console.log(shallowEqual({}, a)); // false
console.log(shallowEqual(a, b)); // false
console.log(shallowEqual(a, c)); // false
console.log(shallowEqual(c, d)); // false
```

Применение неглубокого сравнения в React, чтобы сделать `PureComponent`.
```js
import shallowCompare from 'react-addons-shallow-compare'; 

class Foo extends Component {
  shouldComponentUpdate(nextProps, nextState) {
    return shallowCompare(this, nextProps, nextState);
  }
  render() { /* ... */ }
}
```
Неглубокого сравнение применяется в Redux: `shallowEqual(oldState, newState)`, чтобы выяснить, изменился ли *State*. 
Именно поэтому очень важно *не мутировать State*: измененяются и новый, и старый State одновременно — неглубокое сравнение не видит различий между ними.

### Глубокое сравнение

**Глубокое сравнение** (deep comparison) объектов A и B подразумевает рекурсивный обход и сравнение всех свойств (в том числе и глубоких) объектов A и B.

Одним из способов провести глубокое сравнения является JSON-сериализация (сравниваются получившиеся строки). Это достаточно быстрый способ.
```js
const deepEqual = (A, B) => JSON.stringify(A) === JSON.stringify(B);

const c = { field: { field: 1 } };
const d = { field: { field: 1 } };
const e = { field: { field: 2 } };
console.log(deepEqual(c, d)); // true
console.log(deepEqual(c, e)); // false
```
Его большим недостатком является то, что порядок свойств в сравниваемых объектах имеет значение.
```js
const a = { f1: '1', f2: '2' };
const b = { f2: '2', f1: '1' };
deepEqual(a, b); // false
```

Других встроенных решений не существует.  
Можно переписать функцию `shallowEqual`, сделав её рекурсивной, или подключить готовые функции из сторонних библиотек.
В Node.js есть встроенная функция `assert.deepEqual()`, которая также представлена в виде отдельного модуля `deep-equal`.

Глубокое сравнение работает медленнее, чем неглубокое.  
Не стоит его использовать, если нет в этом необходимости.

# Функции

**Стрелочная функция** (Arrow Function Expression) является *функциональным выражением*, которое, помимо *укороченного синтаксиса*, обладает *рядом свойств* по сравнению с *функциональным выражением*, объявленным через `function` (Function Expression).
```js
const inc = val => val += 1;
const sum = (a, b) => a + b;
const mul = (a, b) => {
  return a * b;
};
```

*Стрелочная функция* не имеет своих `this` и `arguments` — их значения *ищутся снаружи* (из *внешнего лексического окружения*).
```js
const Foo = () => {
  console.log(this); 
  console.log(arguments); 
};
Foo();
// Window {...}
// ReferenceError: arguments is not defined
```
```js
function Bar () {
  console.log(this);
  const Foo = () => {
    console.log(this);
    console.log(arguments);
  };
  Foo();
}
Bar();
// Window {...}
// Window {...}
// Arguments [...]
new Bar();
// Foo {...}
// Foo {...}
// Arguments [...]
```
*Отсутствие* своего `this` влечёт за собой другую особенность: *стрелочная функция* *не может* быть использована как *функция-конструктор*, то есть *не может* быть вызвана с *конструкцией* `new`.
```js
const Article = () => {};
const article = new Article(); // TypeError: Article is not a constructor
```

Ещё одной интересной *особенностью стрелочных функций* является то, что *оператор* `=>` *имеет очень низкий приоритет*, что делает *невозможным использование стрелочных функций* в качестве *операндов других операторов*.

Например, следующий пример *вызовет ошибку*, поскольку у `void` приоритет выше, чем у `=>`, и он *обрабатывается раньше*.
```js
const fn = void () => console.log('notes'); // SyntaxError: Malformed arrow function parameter list
```

# Встроенные объекты
- [Error и его наследники](#error-и-его-наследники)
  - [ReferenceError](#referenceerror)
  - [SyntaxError](#syntaxerror)
  - [TypeError](#typeerror)
  - [RangeError](#rangeerror)
  - [EvalError](#evalerror)
  - [URIError](#urierror)
  - [InternalError](#internalerror)
- [Promise](#promise)
  - [Порядок в Promise.all()](#порядок-в-promiseall)

## Error и его наследники

### ReferenceError
**ReferenceError** — ошибка при обращении к *несуществующей переменной*.
```js
foo.field; // ReferenceError: foo is not defined
```
```js
console.log(foo) // ReferenceError: Cannot access 'foo' before initialization
const foo = {};
```

### SyntaxError
**SyntaxError** - ошибка при попытке интерпретировать *синтаксически неправильный* код.
```js
const foo; // SyntaxError: Missing initializer in const declaration
```
```js
function(){ /* ... */ }() // SyntaxError: Function statements require a function name
```
```js
function foo(){ /* ... */ }() // SyntaxError: Unexpected token )
```
```js
JSON.parse('{ "field":"value", }'); // SyntaxError: Unexpected token } in JSON at position 19
```
### TypeError
**TypeError** - ошибка при наличии *значения несовместимого* (неожидаемого) *типа*.
```js
const foo = {};
foo.method(); // TypeError: foo.method is not a function
```
```js
const foo = 1;
foo = 7; // TypeError: Assignment to constant variable
```
### RangeError
**RangeError** — ошибка в случае нахождения *значения за пределами допустимого диапазона*.
```js
const foo = new Array(-1); // RangeError: Invalid array length
```
```js
const foo = 3;
foo.toFixed(101); // RangeError: toFixed() digits argument must be between 0 and 100
```
```js
function foo() { foo() }
foo(); // RangeError: Maximum call stack size exceeded (везде, кроме Firefox)
```
### EvalError
**EvalError** — ошибка в *глобальной функции eval()*. В *текущей* спецификации *не используется* и остаётся лишь для *совместимости*.

Ошибка ниже связана с проведением браузерами *политики безопастности контента* (Content Security Policy), которая помогает *избежать* многих *потенциальных XSS* (cross-site scripting) *атак*.  
Ранее её тип был *EvalError*, сейчас он просто *опускается*:
```js
window.setInterval("alert('notes')", 25); // Refused to evaluate a string as JavaScript because 'unsafe-eval' is not an allowed source of script in the following Content Security Policy directive: "script-src github.githubassets.com".
```
### URIError
**URIError** - ошибка при передаче *недопустимых параметров* в *encodeURI()* или *decodeURI()*.
```js
encodeURI('\uD900'); // URIError: malformed URI sequence (Firefox)
encodeURI('\uD900'); // URIError: The URI to be encoded contains an invalid character (Edge)
encodeURI('\uD900'); // URIError: URI malformed (Chrome and others)
```
### InternalError
**InternalError** - *внутренняя* ошибка в *движке JavaScript*. (только *Firefox*)
```js
function foo() { foo() }
foo(); // InternalError: too much recursion
```

*Все* рассмотренные *типы ошибок* можно *сгенерировать* так же, как и *Error*, *наследниками* которого они являются:
```js
throw new Error(/* ... */);
```

## Promise

**Promise** (промис) – *специальный объект*, *содержащий своё состояние*.  
*Изначально состояние* `pending` (ожидание).  
Затем либо `resolved`/`fulfilled` (выполнено успешно), либо `rejected` (выполнено с ошибкой).

```javascript
/* создание Promise */
const executor = (resolve, reject) => { /* ... */ };
const promise = new Promise(executor); 
```
Функция `executor(resolve, reject)` *вызывается автоматически*. В ней можно выполнять *любые асинхронные операции*. По их завершении следует вызвать либо `resolve(value)`, либо `reject(reason)`.

После вызова `resolve` или `reject` *промис меняет* своё *состояние*, которое становится *конечным* (больше его изменить нельзя). 

*Отреагировать* на *изменение состояния промиса* можно при помощи `then` и `catch`.
```js
const onResolved = value => { /* ... */ };
const onRejected = reason => { /* ... */ };

// функция onFulfilled сработает при успешном выполнении
promise.then(onResolved);
// функция onRejected – при выполнении с ошибкой
promise.then(onResolved, onRejected);
promise.catch(onRejected);
```

Пример с *setTimeout*, где *промис успешно выполнится* не менее, чем через *3 секунды*.
```js
const executor = resolve => void setTimeout(resolve, 3000);
const promise = new Promise(executor);
promise.then(() => console.log('resolved!'));
// через ~3 секунды выведется 'resolved!'
```
Пример с *передачей значения* в `resolve`.
```js
const executor = resolve => void resolve('resolved!');
const promise = new Promise(executor);
promise.then(console.log);
// через ~3 секунды выведется 'resolved!'
```
Пример с *передачей причины* в `reject`.
```js
const executor = (resolve, reject) => void reject('rejected!');
const promise = new Promise(executor);
promise.catch(console.log);
// через ~3 секунды выведется 'rejected!'
```

### Промисификация
**Промисификация** – *создание обёртки*, *возвращающей Promise*, вокруг *асинхронной* функциональности.

Обычно *промисифицируют асинхронные функции*, построенные на *функциях обратного вызова* (callbacks).
```js
const callback = (err, result) => err ? reject(err) : resolve(result);

/* Принимается функция fn и возвращается функция-обёртка, возвращающая Promise. */
const promisify = fn => (...args) => new Promise((resolve, reject) => {    
  fn(...args, callback);
});
```

<!-- fn.call(this, [...args, callback]); // вызывается fn с обновлёнными аргументами -->

### Цепочка промисов

Если нужно выполнять асинхронные операции в определённой последовательности, можно каждую из них обернуть в промис и создать **цепочку промисов** (Promise chain). Для *создания* такой *цепочки* необходимо в `.then()` или `.catch()` *вернуть промис*.

### Порядок в Promise.all()

**Итерируемый объект** (iterable) — *любой* объект, *элементы* которого можно *перебрать* при помощи *цикла for..of*.  
По-умолчанию такими *являются встроенные типы Array*, *Set*, *String* и *Map*, в то время как *Object не является*.

Функция `Promise.all(iterable)` *принимает итерируемый объект* (обычно массив), содержащий промисы (элементы, не являющиеся промисами, помещаются в `Promise.resolve()`), *дожидается выполнения каждого из промисов* и *возвращает массив*, состоящий из их *значений*.

Несмотря на то, что *промисы выполняются асинхронно*, *порядок в результирующем массиве значений совпадает* с *порядком промисов* в *ачальном итерируемом объекте* благодаря *внутреннему свойству [[Index]]*:
```js
const slow = new Promise(resolve => setTimeout(resolve, 250, 'slow'));
const instant = 'instant'; // тип не Promise , поэтому преобразуется в Promise.resolve('instant')
const quick = new Promise(resolve => setTimeout(resolve, 50, 'quick'));

const onResolved = responses => responses.map(response => console.log(response));
Promise.all([slow, instant, quick]).then(onResolved);

// или то же самое с помощью async/await
try {
  const responses = await Promise.all([slow, instant, quick]);
  responses.map(response => console.log(response)); // 'slow', 'instant', 'quick'
} catch (e) {
  /* ... */
}
```

Поскольку тип *String* является *итерируемым*, его тоже *можно передать* в *Promise.all()*:
```js
Promise.all('notes').then(res => console.log(res)); // ['n', 'o', 't', 'e', 's']
// что эквивалентно
Promise.all(['n', 'o', 't', 'e', 's']).then(res => console.log(res));
```

# Спецификация

## Функции под капотом JavaScript

**Функциональный объект** (function object) — *объект*, поддерживающий *внутренний метод [[Call]]*.

**Фукция-конструктор** (constructor function), или просто **конструктор** (constructor), — *функциональный объект*, поддерживающий *внутренний метод [[Construct]]*.

### Метод [[Call]]

**Метод [[Call]] (thisArgument, argumentsList)** *выполняет код*, связанный с *его* функциональным объектом.  

*Вызывается* при помощи *выражения вызова функции*: 
```js
object()
```

*Аргументы*: значение *this* и *список аргументов*, переданных *функции выражением вызова*.  

Объекты, которые *реализуют* внутренний метод *[[Call]]*, называются **вызываемыми** (callable).

### Метод [[Construct]]

**Метод [[Construct]] (argumentsList, newTarget)** *cоздаёт и возвращает объекты*.

*Вызывается* при помощи *операторов* **new** и **super**.

*Аргументы*: *список аргументов оператора* и *объект*, к которому изначально был *применён оператор new*.  


### Инстанциирование функционального объекта 

**Инстанцирование** (instantiation) — *создание экземпляра класса* (instance).  
Слово *инстанционарование* применяется *к классу*, *создание* (creation) - *к объекту*.  

Несмотря на то, что *функции* в JavaScript являются *объектами*, в то же время они могут быть и *классами*, поэтому к ним и *применяется* слово *инстанционарование*.

*Функциональные объекты инстанционируются* при помощи:
```js
InstantiateFunctionObject(scope)
```

### Function Declaration
```js
function BindingIdentifier ( FormalParameters ) { FunctionBody }
```
#### I этап интерпретации - инстанционирование
1) Положить в переменную *strick* *true*, если к коду функции *применён strict мод*, *false иначе*.  
2) Положить в переменную *name строку BindingIdentifier* или строку *"default"*, если *значение не задано*.
3) Положить в переменную *F результат* выполнения `FunctionCreate(Normal, FormalParameters, FunctionBody, scope, strict)`.  
4) *Создать конструктор* с помощью `MakeConstructor(F)`.  
5) *Установить имя* функции с помощью `SetFunctionName(F, name)`.  
6) *Вернуть F*.

#### II этап интерпретации - оценка (Evaluation)
1) Вернуть `NormalCompletion(empty)`.

### Function Expression
```js
function ( FormalParameters ) { FunctionBody }
```

#### I этап интерпретации
Отсутствует.

#### II этап интерпретации - оценка (Evaluation)
1) Положить в переменную *strick* *true*, если к коду функции *применён strict мод*, *false иначе*.  
2) Положить в переменную *scope* *LexicalEnvironment* из *контекста выполнения*.
3) Положить в переменную *closure результат выполнения* `FunctionCreate(Normal, FormalParameters, FunctionBody, scope, strict)`.  
4) *Создать конструктор* с помощью  `MakeConstructor(F)`.  
6) *Вернуть closure*.
