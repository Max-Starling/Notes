- [Объекты и функции первого класса](#объекты-и-функции-первого-класса)
- [Функции высшего порядка](#функции-высшего-порядка)
- [Композиция](#композиция)
- [Мемоизация](#мемоизация)
- [Каррирование](#каррирование)

# Thunk

https://en.wikipedia.org/wiki/Thunk

## Объекты и функции первого класса

*Объект* называется **объектом первого класса** (First-class Object/Entity/Citizen), если он 
1. может быть передан в функцию как аргумент,  
2. может быть возвращен из функции как результат, 
3. может быть присвоен переменной, 
4. может быть сохранён в структуру данных.

Остальные объекты считаются **объектами второго класса** (Seconds-Class-Objects).

<!-- 5) может быть создан во время выполнения программы,  -->

Функции, являющиеся объектами первого класса, называют **функциями первого класса** (First-class Function).

Во *многих языках программирования* понятие *функции первого класса ассоциируется* с понятием **анонимной функции** (anonymous functions).

В *JavaScript все функции* являются *функциями первого класса*.
```js
// 1., 2. и 3.
const createLogger = log => (...args) => log(...args);

// 3.
const log = createLogger(console.log);
log('qq'); // "qq"

// 4.
const speaker = {
  hello: () => log('hello'),
}
```

## Функции высшего порядка

**Функция высшего порядка** (High-order Function) —  *функция*, *принимающая другую функцию* в *качестве аргумента* или *возвращающая функцию* в *качестве результата* своего выполнения.

Пример *функции высшего порядка*, реализующей *замыкание* (она замыкает переменную `count`).
```js
/* счётчик */
const createCounter = () => {
  let count = 0;
  return () => count += 1; // возвращается функция
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2

const anotherCounter = createCounter();
console.log(anotherCounter()); // 1
```
Пример *функции высшего порядка*, реализующей *[каррирование](#каррирование)*.
```js
/* конкатенация двух строк */
const concat = x => y => `${x} ${y}`; // возвращается функция

console.log(concat('Simple')('notes')); // "Simple notes"
```
Пример *функции высшего подярка*, принимающей и возвращающей компонент в React (компоненты в React являются функциями), которая называется **компонентом высшего порядка** (High-order Component, HOC).
```jsx
const Header = props => (<h1>{props.children}</h1>);

const Title = props => (<p>{props.children}</p>);

const createWelcomeTextComponent = TextComponent => props => (
  <TextComponent {...props}>Welcome</TextComponent>
); // принимает и возвращает компонент

const WelcomeHeader = createWelcomeTextComponent(Header);
const WelcomeTitle = createWelcomeTextComponent(Title);

const render = () => (
  <header>
    <WelcomeHeader />
  </header>
  <main>
    <article>
      <WelcomeTitle />
    </article>
  </main>
);
```
## Функции обратного вызова

## Композиция

**Композиция функций** (Function Composition) — *применение одной функции* к *результату другой*. 

Функция `h` — *композиция* функций `g` и `f`, если `h(x) = g(f(x))`. Обозначение: `h = g ∘ f`.

```js
const increment = val => val + 1;
const decrement = val => val - 1;

const incrementDecrement = val => decrement(increment(val)); // композиция
const incrementTwice = val => increment(increment(val)); // композиция

const foo = incrementDecrement(0);
console.log(foo); // 0
const bar = incrementTwice(0);
console.log(bar); // 2
```

Композицию можно составлять из любого количества функций.
```js
console.log(incrementTwice(increment(decrement(incrementDecrement(increment(0)))))); // 3
```
Если в *композиции* участвует слишком *много функций*, то её код становится *трудно читаемым* (смотреть пример выше). Для таких случаев можно использовать *функцию* `compose`, которая принимает функции в качестве параметров через запятую и составляет из них композицию, последовательно вызывая их справа налево.
```js
const compose = (...functions) => args => functions.reduceRight((arg, fn) => fn(arg), args);
```
```js
console.log(compose(
  incrementTwice,
  increment,
  decrement,
  incrementDecrement,
  increment,
)(0)); // 3

/* массив из 10 функций increment */
const fns = Array(10).fill(increment); // [ƒ, ƒ, ƒ, ƒ, ƒ, ƒ, ƒ, ƒ, ƒ, ƒ]
console.log(compose(...fns)(0)); // 10
```

## Pipe
```js
const pipe = (...functions) => args => functions.reduce((arg, fn) => fn(arg), args);
```

## Мемоизация

**Мемоизация** (memoization, запоминание) — сохранение результатов выполнения функций для предотвращения повторных вычислений.

Мемоизация подразумевает проверку перед каждым вызовом мемоизируемой функции: если она ранее вызывалась с теми же параметрами, как сейчас, — вернуть результат из памяти, иначе — вычислить и записать в память.

<!--
Очевидно, что мемоизация предполагает работу с чистыми функциями, которые всегда при тех же параметрах вернут тот же результат.
```js
/* функция ниже не подойдёт для мемоизации */
const rand = (min, max) => min + Math.random() * (max - min);
```
-->

Напишем простую функцию инкремента.
```js
const inc = val => val + 1;
console.log(inc(5)); // 6
```
Мемоизируем её.
```js
let memory = {};

const incMemo = (val) => {
  if (memory[val] === void 0) {
    memory[val] = inc(val);
  } else {
    console.log('took from memory!');
  }
  return memory[val];
};

console.log(incMemo(5)); // 6
console.log(memory); // { 5: 6 }
console.log(incMemo(5)); // 'took from memory!', 6
```
Обобщим функцию мемоизации, чтобы её можно было переиспользовать для любой функции одного аргумента.
```js
const memo = fn => (val) => {
  if (memory[val] === void 0) {
    memory[val] = fn(val);
  } else {
    console.log('took from memory!');
  }
  return memory[val];
};
```
```js
const dec = memo(val => val - 1);

console.log(dec(6)); // 5
console.log(dec(6)); // 'took from memory!', 5
```
Перепишем функцию `memo` таким образом, чтобы она могла принимать несколько параметров.
```js
let memory = {};

const memo = fn => (...args) => {
  const key = args.toString();
  if (memory[key] === void 0) {
    memory[key] = fn(...args);
  } else {
    console.log('took from memory!');
  }
  return memory[key];
};
```
```js
const mulFn = (a, b) => a * b;
const mul = memo(mulFn);

console.log(mul(3, 7)); // 21
console.log(memory); // { "3, 7": 21 }
console.log(mul(3, 7)); // 'took from memory!', 21
```
В примерах выше используется общее хранилище для всех мемоизируемых функций. Перепишем функцию `memo` таким образом, чтобы у каждой функции `fn` было своё хранилище.

Для этого используем метод `toString()`, возвращающий строковое представление функции.
```js
console.log(mulFn); // "(a, b) => a * b"
```
Будем использовать это представление в качестве ключа в хранилище `memory`. Этому ключу будет соответствовать хранилище для конкретной функции `fn`.
```js
let memory = {};

const memo = fn => (...args) => {
  const fnKey = fn.toString();
  memory[fnKey] = memory[fnKey] || {};
  const argsKey = args.toString();
  if (memory[fnKey][argsKey] === void 0) {
    memory[fnKey][argsKey] = fn(...args);
  } else {
    console.log('took from memory!');
  }
  return memory[fnKey][argsKey];
};
```
```js
const divideFn = (a, b) => a / b;
const divide = memo(divideFn);

console.log(divide(21, 7)); // 3
console.log(memory); // { "(a, b) => a * b": { "3,7": 21 } }
console.log(memory[divideFn.toString()]); // { "3,7": 21 }
console.log(divide(21, 7)); // 'took from memory!', 3
```
Сейчас хранилище `memory` очищается только вручную: результаты вычислений остаются даже тогда, когда функция больше не используется.
```js
let sumFn = (...args) => args.reduce((acc, val) => acc + val, 0);
const sumKey = sumFn.toString();

const sum = memo(sum);
console.log(sum(1, 2, 3)); // 6
console.log(sum(1, 2, 3)); // 'took from memory!', 6

sumFn = null; // удаляем функцию
console.log(memory[sumKey]); // { "1,2,3": 6 } (данные о sum остались)
```
Если бы мы использовали в качестве `fnKey` не строковое представление функции, а саму функцию, то результат был бы тем же: любое значение (в том числе непримитивное), используемое как ключ, приводится к стровокому значению. Будем использовать `WeakMap`, принимающий объект в качестве ключа и очищающий ячейку хранилища, когда этот объект удаляется (как вручную, так и сборщиком мусора).
```js
let memory = new WeakMap();

const memo = fn => (...args) => {
  if (!memory.has(fn)) {
    memory.set(fn, {});
  }
  const argsKey = args.toString();
  if (memory.get(fn)[argsKey] === void 0) {
     memory.get(fn)[argsKey] = fn(...args);
  } else {
    console.log('took from memory!');
  }
  return memory.get(fn)[argsKey];
};
```
```js
let sumFn = (...args) => args.reduce((acc, val) => acc + val, 0);

const sum = memo(sumFn);

console.log(sum(1, 2, 3)); // 6
console.log(sum(1, 2, 3)); // 'took from memory!', 6
console.log(memory.get(sumFn)); // { "1,2,3": 6 }
sumFn = null;
console.log(memory.get(sumFn)); // undefined
```
Рассмотрим параметры функции `fn`. Что, если ими будут являться объекты, массивы, функции?
```js
let args = [{ foo: 1 }, { bar: 2 }];
console.log(args.toString()); // "[object Object],[object Object]"

args = [[1], [2, 3]];
console.log(args.toString()); // "1,2,3"
args = [1, 2, 3];
console.log(args.toString()); // "1,2,3"

args = [() => 1, () => 2];
console.log(args.toString()); // "() => 1,() => 2"
```
Чтобы сделать функцию `memo` более расширяемой, следует добавить возможность генерации ключей.
```js
let memory = new WeakMap();

const defaultKeyGenerator = args => args.toString();

const memo = (fn, keyGerenator) => (...args) => {
  if (!memory.has(fn)) {
    memory.set(fn, {});
  }
  const argsKey = keyGerenator ? keyGerenator(args) : defaultKeyGenerator(args);
  if (memory.get(fn)[argsKey] === void 0) {
     memory.get(fn)[argsKey] = fn(...args);
  } else {
    console.log('took from memory!');
  }
  return memory.get(fn)[argsKey];
};
```
Тогда для объектов можно использовать генератор, использующий их JSON-представления.
```js
import equal from 'deep-equal';

const deepEqual = memo(
  equal,
  args => args.map(item => JSON.stringify(item)).join('__'), // key generator
);

const foo = { a: { b: 1 } };
const bar = { a: { b: 1 } };

console.log(deepEqual(foo, bar)); // true
console.log(deepEqual(foo, bar)); // took from memory!, true
console.log(memory.get(equal)); // { '{"a":{"b":1}}__{"a":{"b":1}}': true }
```
Для массивов можно заменить в генерации `toString()` на `join(separator)`, если есть необходимость, чтобы наборы параметров `[1], [2, 3]` и `[1, 2], [3]` считались различными при подсчётах.
```js
const sumFn = (...args) => args.reduce((acc, val) => acc + val, 0);
const sum = memo(sumFn);

const sumArraysFn = (...args) => args.reduce((acc, val) => acc + (Array.isArray(val) ? sum(...val) : val), 0);
const sumArrays = memo(sumArraysFn, args => args.join('__'));

console.log(sumArrays([1,2,3], [2,3], [1,2,3])) // took from memory!, 17 (подсчёт [1, 2, 3] берётся из памяти)
console.log(memory.get(sumFn)); // { "1,2,3": 6, "2,3": 5 }
console.log(sumArrays([1,2,3], [2,3], [1,2,3])); // took from memory!, 17
console.log(memory.get(sumArraysFn)); // { "1,2,3__2,3__1,2,3": 17 }
```

## Каррирование
**Каррирование**, **карринг** (currying) — преобразование функции несокольких аргументов в цепочку функций одного аргумента.

```js
const sum = (x, y) => x + y;
sum(1, 3); // 4

const curriedSum = x => y => x + y;
curriedSum(1)(3); // 4
```
Каждый вызов преобразованной функции возвращает новую функцию, в которой один параметр фиксируется.
```js
const tmp = curriedSum(1); //  y => 1 + y
tmp(3); // 4
```
Функцию преобразования можно написать следующим образом.
```js
const curry = (fn) => { 
 const curried = (...args) => { 
   if (args.length === fn.length) { 
     return fn(...args); 
   } else { 
     return nextArg => curried(...args, nextArg); 
   } 
 }; 
 return curried; 
}
```
Или можно записать ещё короче.
```js
const curry = fn => curried = (...args) => 
  args.length === fn.length
   ? fn(...args)
   : nextArg => curried(...args, nextArg);
```
