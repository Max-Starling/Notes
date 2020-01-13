- [Объекты и функции первого класса](#объекты-и-функции-первого-класса)
- [Функции высшего порядка](#функции-высшего-порядка)
- [Композиция](#композиция)
- [Мемоизация](#мемоизация)
- [Каррирование](#каррирование)

## Объекты и функции первого класса

*Объект* называется **объектом первого класса** (First-class Object/Entity/Citizen), если он 
1. может быть передан в функцию как аргумент,  
2. может быть возвращен из функции как результат, 
3. может быть присвоен переменной, 
4. может быть сохранён в структуру данных.

Остальные объекты считаются **объектами второго класса** (Second-class Objects).

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

**Функция высшего порядка** (Higher-order Function) —  *функция*, *принимающая другую функцию* в *качестве аргумента* или *возвращающая функцию* в *качестве результата* своего выполнения.

*JavaScript* является *событийно-ориентированным* (Event-driven) языком. Многие *действия выполняются* по *наступлению определённых событий* или выполняются *строго после других действий*. *Функции высшего порядка*, принимающие в *качестве параметра другие функции*, помогают добиться такого поведения. *Функции-параметры* называют **функциями обратного вызова**.
```js
const callback = () => console.log('done');

document.addEventListener('onClick', callback);

setTimeout(callback, 1000);

const p = new Promise(res => res());
p.then(callback);

const calculate = (cb) => {
  const result = 1 + 2;
  cb();
  return result;
};
calculate(callback);
```

Пример *функции высшего порядка*, реализующей *замыкание*. Она замыкает переменную `count` и возвращает другую функцию, изменяющую замкнутую переменную.
```js
/* счётчик */
const createCounter = () => {
  let count = 0;
  return () => count += 1;
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2

const anotherCounter = createCounter();
console.log(anotherCounter()); // 1
```
Пример *функции высшего порядка*, реализующей *[каррирование](#каррирование)*. Она возвращает другую функцию, использующую свой параметр `y` и параметр функции высшего порядка `x`, доступный из замыкания.
```js
/* конкатенация двух строк */
const concat = x => y => `${x} ${y}`;

console.log(concat('Simple')('notes')); // "Simple notes"
```
Пример *функции высшего подярка*, принимающей и возвращающей React-компонент (компоненты в React являются функциями), которая называется **компонентом высшего порядка** (Higher-order Component, HOC).
```jsx
const user = { name: 'Max' };

const withUser = Component => props => (
  <Component {...props} user={user} />
);

const Article = withUser(props => <div>{props.user.name}</div>);
```
```jsx
const createConditionalComponent => condition => Component => props => (
  condition ? <Component {...props} /> : null;
);

const Text = () => (<span>Notes</span>);

const AlwayRenderingText = createConditionalComponent(true)(Text);
const NeverRenderingText = createConditionalComponent(false)(Text);
```

## Композиция

**Композиция функций** (Function Composition) — *применение одной функции* к *результату другой*. 

Функция `h` — *композиция* функций `g` и `f`, если `h(x) = g(f(x))`. Обозначение: `h(x) = (g ∘ f)(x)`.

```js
const increment = val => val + 1;
const decrement = val => val - 1;

const foo = decrement(increment(0)); // композиция
console.log(foo); // 0
const bar = increment(increment(0)); // композиция
console.log(bar); // 2
```

При помощи композиции можно составлять из простых функций выражения любой сложности.
```js
const divide = (value, divider) => value / divider;
const pow = (value, power) => value ** power;
divide(pow(3, pow(2, 2)), 3); // 27 = (3 ^ (2 * 2)) / 3
```
Если в *композиции* участвует слишком *много функций*, то её код становится *трудно читаемым*. Для таких случаев можно использовать *функцию* `compose`, которая принимает несколько функции и составляет из них композицию справа налево (начинает с последней и заканчивает первой).
```js
const compose = (...fns) => initialValue => fns.reduceRight((value, fn) => fn(value), initialValue);
```
```js
console.log(compose(
  increment,
  increment,
  decrement,
  increment,
  increment,
)(4)); // 7

/* массив из 10 функций increment */
const fns = Array(10).fill(increment); // [ƒ, ƒ, ƒ, ƒ, ƒ, ƒ, ƒ, ƒ, ƒ, ƒ]
console.log(compose(...fns)(0)); // 10
```
Если промежуточная функция в композиции имеет больше одного параметра, её можно вызвать в теле функции-обёртки с нужным количеством параметров.
```js
divide(pow(3, pow(2, 2)), 3); // 27 = (3 ^ (2 * 2)) / 3
/* преобразуется в */
compose(
  value => divide(value, 3),
  value => pow(3, value),
  () => pow(2, 2),
)();
```
Поскольку функция не может вернуть больше одного значения, промежуточные функции композиции могут принять только один аргумент. Симулировать поведение передачи нескольких аргументов можно, если передавать их массивом или объектом.
```js
compose(
  value => divide(value, 3),
  value => pow(3, value),
  ([value, power]) => pow(value, power),
)([2, 2]);
```
Стоит ещё раз отметить, что функция `compose` начинается с последней переданной функции, а не с первой.

### Pipe
Функция `pipe` работает аналогично функции `compose`, но составляет композицию из своих функций-аргументов слева направо.
```js
const pipe = (...fns) => initialValue => fns.reduce((value, fn) => fn(value), initialValue);
```
Её использование выглядит более естественным.
```js
pipe(
  ([value, power]) => pow(value, power),
  value => pow(3, value),
  value => divide(value, 3),
)([2, 2]);
```

### Несколько аргументов
Можно переписать `compose` и `pipe` таким образом, чтобы первая функция принимала несколько аргументов.
```js
const compose = (...fns) => fns.reduceRight((f, g) => (...args) => g(f(...args)));
const pipe = (...fns) => fns.reduce((f, g) => (...args) => g(f(...args)));
/* или */
const compose = (...fns) => fns.reduce((f, g) => (...args) => f(g(...args)));
const pipe = (...fns) => fns.reduceRight((f, g) => (...args) => f(g(...args)));
```
```js
pipe(
  (value, power) => pow(value, power),
  value => pow(3, value),
  value => divide(value, 3),
)(2, 2);
```
Промежуточные функции по-прежнему не смогут принять больше одного параметра.

Описание работы функции `compose` по шагам. Метод `reduce` при отсутствии начального значения кладёт первый элемент массива первым параметром, второй - вторым параметром.
```js
const compose = (...fns) => fns.reduce((f, g) => (...args) => f(g(...args)));
/* далее псевдокод */
compose(a, b, c, d)(1, 3)
/* I */
(a, b) => args => a(b(args)) := k
/* II */
(k, c) => args => k(c(args)) := m
/* III */
(m, d) => args => m(d(args)) := n
/* IV */
n := args => a(b(c(d(args)));
/* V */
n(1, 3) := a(b(c(d(1, 3)))
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

## Thunk

https://en.wikipedia.org/wiki/Thunk
