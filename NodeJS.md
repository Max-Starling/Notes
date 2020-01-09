- [Асинхронное программирование](#асинхронное-программирование)
  - [Функции обратного вызова](#функции-обратного-вызова)
  
# Асинхронное программирование

## Функции обратного вызова

**Функция обратного вызова** (Callback, cb) — функция, которая передаётся параметром в другую функцию. 

Примером *функции обратного вызова* является `compareFunction` в `[].sort(compareFunction)`, использующаяся в качестве алгоритма сравнения двух элементов массива.
```js
const compareFunction = (a, b) => a - b;
[1, 3, 2].sort(compareFunction); // [1, 2, 3]
```

Чаще всего *функции обратного вызова* используются как какой-то *набор инструкций*, который нужно *выполнить после совершения* какого-то *действия* или *наступления события*.

*Параметры Promise executor* `resolve` и `reject` являются *функциями обратного вызова*.
```js
const promise = new Promise((resolve, reject) => {
  setTimeout(() => resolve('resolved'), 3000);
});

promise.then(result => console.log(result)); // resolved (через ~3 секунды)
```
Функция `setTimeout(callback, delay)` принимает *функцию обратного вызова*, которая вызовется не ранее, чем пройдёт `delay` миллисекунд.
```js
const `setTimeout`
```

Код ниже отработает синхронно, последовательно.
```js
console.log('before');
console.log('after');
// выведет "before", "after"
```
Следующие два примера отработают асинхронно, непоследовательно.
```js
Promise.resolve('before').then(console.log);
console.log('after');
// выведет "after", "before"
```
```js
setTimeout(() => console.log('before'), 0);
Promise.resolve('after').then(console.log);
// выведет "after", "before"
```
Чтобы симулировать синхронное поведение (выполнить инструкции с логированием в правильном порядке), можно использовать
```js
const before = () => console.log('before');
const after = () => console.log('after');

const do = (after) => {
  before()
  after();
};
```

## Promise


## Работа с файлами

### Удаление файла

```js
const fs = require('fs')

const removeFile = (filePath) => new Promise((resolve, reject) => {
  fs.unlink(path, err => err ? reject(err) : resolve());
});
```
