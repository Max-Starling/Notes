- [Мемоизация](#мемоизация)
- [Каррирование](#каррирование)

# Thunk

https://en.wikipedia.org/wiki/Thunk

## Функции высшего порядка

## Композиция

**Композиция функций** (function composition) — *применение одной функции* к *результату другой*. 

Функция `h` — *композиция* функций `g` и `f`, если `h(x) = g ∘ f = g(f(x))`.

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

Композицию можно делать и из большего числа функций.
```js
console.log(incrementTwice(increment(decrement(incrementDecrement(increment(0)))))); // 3
```
Если в *композиции* участвуют *много функций*, а их *названия длинные*, то такую композицию *трудно прочитать*. Можно написать *функцию* `compose`, которая *составляет композицию* из *любого числа функций*.
```js
const compose = (...functions) => args => functions.reduceRight((arg, fn) => fn(arg), args);

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
