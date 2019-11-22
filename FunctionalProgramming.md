https://en.wikipedia.org/wiki/Thunk


## Композиция

**Композиция** функций (function composition) — *применение одной функции* к *результату другой*. 

Функция `h` — композиция функций `g` и `f`, если `h(x) = g ∘ f = g(f(x))`.

```js
const increment = val => val + 1;
const decrement = val => val - 1;

const incrementDecrement = val => decrement(increment(val)); // композиция
const incrementTwice = val => increment(increment(val)); // композиция

let foo = incrementDecrement(0);
console.log(foo); // 0
let bar = incrementTwice(0);
console.log(bar); // 2
```
