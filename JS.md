# Основы JavaScript

## Типы ошибок в JavaScript

**ReferenceError** — ошибка при обращении к *несуществующей переменной*.
```js
obj.field; // ReferenceError: obj is not defined
```
```js
console.log(obj2); // ReferenceError: Cannot access 'obj3' before initialization
const obj2 = {};
```

**SyntaxError** - ошибка при попытке интерпретировать *синтаксически неправильный* код.
```js
const a; // SyntaxError: Missing initializer in const declaration
```
```js
function(){ /* ... */ }() // SyntaxError: Function statements require a function name
```
```js
function a(){ /* ... */ }() // SyntaxError: Unexpected token )
```
**TypeError** - ошибка при наличии *значения несовместимого* (неожидаемого) *типа*.
```js
const obj = {};
obj.method(); // TypeError: obj.method is not a function
```

**RangeError** — ошибка в случае нахождения *значения за пределами допустимого диапазона*.
```js
const arr = new Array(-1); // RangeError: Invalid array length
```
```js
(function f() { f() })() // RangeError: Maximum call stack size exceeded  (везде, кроме Firefox)
```

**EvalError** — ошибка в *глобальной функции eval()*.

**URIError** - ошибка при передаче *недопустимых параметров* в *encodeURI()* или *decodeURI()*.

**InternalError** - *внутренняя* ошибка в *движке JavaScript*. (только *Firefox*)
```js
(function f() { f() })() // InternalError: too much recursion
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

**Итерируемый объект** (iterable) - *любой* объект, элементы которого можно перебрать при помощи *цикла for..of*.  
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
