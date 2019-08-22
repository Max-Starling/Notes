# Основы JavaScript

## Типы ошибок в JavaScript

**ReferenceError** — ошибка при обращении к *несуществующей переменной*.
```js
foo.field; // ReferenceError: foo is not defined
```
```js
console.log(foo) // ReferenceError: Cannot access 'foo' before initialization
const foo = {};
```

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
**TypeError** - ошибка при наличии *значения несовместимого* (неожидаемого) *типа*.
```js
const foo = {};
foo.method(); // TypeError: foo.method is not a function
```

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

**EvalError** — ошибка в *глобальной функции eval()*. В *текущей* спецификации *не используется* и остаётся лишь для *совместимости*.

Ошибка ниже связана с проведением браузерами *политики безопастности контента* (Content Security Policy), которая помогает *избежать* многих *потенциальных XSS* (cross-site scripting) *атак*.  
Ранее её тип был *EvalError*, сейчас он просто *опускается*:
```js
window.setInterval("alert('notes')", 25); // Refused to evaluate a string as JavaScript because 'unsafe-eval' is not an allowed source of script in the following Content Security Policy directive: "script-src github.githubassets.com".
```
**URIError** - ошибка при передаче *недопустимых параметров* в *encodeURI()* или *decodeURI()*.
```js
encodeURI('\uD900'); // URIError: malformed URI sequence (Firefox)
encodeURI('\uD900'); // URIError: The URI to be encoded contains an invalid character (Edge)
encodeURI('\uD900'); // URIError: URI malformed (Chrome and others)
```
**InternalError** - *внутренняя* ошибка в *движке JavaScript*. (только *Firefox*)
```js
function foo() { foo() }
foo(); // InternalError: too much recursion
```

*Все* рассмотренные *типы ошибок* можно *сгенерировать* так же, как и *Error*, *наследниками* которого они являются:
```js
throw new Error(/* ... */);
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
const onClick = void () => this.setState({ isClicked: true });
```

## Оператор запятая

**Оператор запятая** (comma) *выполняет каждый из его операндов слева направо* и *возвращает значение последнего*.

Не так часто удаётся его применить, но иногда может быть полезен.  
Например, можно временно добавить в стрелочную функцию логирование, если нужно что-то быстро посмотреть:
```js
const getDataType = data => typeof data;
// заменяем на
const getDataType = data => (console.log(data), typeof data);

getDataType('notes') // можно увидеть значение 'notes' в консоли
```
Другой пример: выполнить операцию над чем-то и вернуть результат:
```js
const array = ['n', 'o', 't', 'e'];
console.log(array.push('s')) // 5 (вернулась длина массива после добавления элемента)

// хотим вернуть новый массив:
const array = ['n', 'o', 't', 'e'];
const push = (arr, val) => (arr.push(val), arr);
console.log(push(array, 's')); // ['n', 'o', 't', 'e', 's']
```

Круглые скобки (оператор группировки) обязательны, поскольку оператор запятая имеет самый низкий приоритет среди всех операторов, в случае их отсутствия скорее всего будет ошибка.

## Порядок в Promise.all()

**Итерируемый объект** (iterable) — *любой* объект, элементы которого можно перебрать при помощи *цикла for..of*.  
По-умолчанию такими *являются встроенные типы Array*, *Set*, *String* и *Map*, в то время как *Object не является*.

Функция Promise.all(iterable) принимает итерируемый объект (обычно массив), содержащий промиссы, дожидается выполнения каждого и промиссом возвращает массив из их значений.

Несмотря на то, что промиссы выполняются асинхронно, порядок в результирующем массиве значений совпадает с порядком промиссов в начальном итерируемом объекте благодаря *внутреннему свойству [[Index]]*:
```js
const slow = new Promise(resolve => setTimeout(resolve, 250, 'slow'));
const instant = 'instant'; // тип не Promise , поэтому преобразуется в Promise.resolve('instant')
const quick = new Promise(resolve => setTimeout(resolve, 50, 'quick'));

Promise.all([slow, instant, quick]).then(responses => responses.map(response => console.log(response)));

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

## Функции под капотом JavaScript (из спецификации)

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
Отсуствует.

#### II этап интерпретации - оценка (Evaluation)
1) Положить в переменную *strick* *true*, если к коду функции *применён strict мод*, *false иначе*.  
2) Положить в переменную *scope* *LexicalEnvironment* из *контекста выполнения*.
3) Положить в переменную *closure результат выполнения* `FunctionCreate(Normal, FormalParameters, FunctionBody, scope, strict)`.  
4) *Создать конструктор* с помощью  `MakeConstructor(F)`.  
6) *Вернуть closure*.

## Является ли объектом
```js
const isObject = value => typeof value === 'object' && !Array.isArray(value) && value !== null;
```
В JavaScript *массивы* и *функции* так же являются *объектами*.  
Но оператор *typeof* имеет *тип function*' для *удобства*.  
То, что *null* считается *объектом*, является *врождённой ошибкой* языка.  
```js
typeof({}) === 'object' // true
typeof([]) === 'object' // true
typeof(() => {}) === 'function' // true
typeof(null) === 'object' // true
```
