- [Асинхронное программирование](#асинхронное-программирование)
  - [Функции обратного вызова](#функции-обратного-вызова)
  - [Error-first callback](#error-first-callback)
  
# Асинхронное программирование

## Функции обратного вызова

**Функция обратного вызова** (Callback, cb) — *функция*, *передающаяся аргументом* в *другую функцию*. 

*Функции обратного вызова* могут быть использованы как *алгоритмы* для *решения задач*, имеющих *несколько способов решения*. Одной из таких задач является сортировка. 

В JavaScript метод `Array.prototype.sort(compareFunction)` принимает функцию обратного вызова `compareFunction`, использующуюся как алгоритм сравнения двух элементов массива.
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
Функция `setTimeout(callback, delay)` принимает *функцию обратного вызова*, которая вызовется *не ранее*, чем *через* `delay` *миллисекунд*.
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
Чтобы симулировать синхронное поведение (выполнить инструкции с логированием в правильном порядке), можно написать следующее.
```js
const after = () => console.log('after');

Promise.resolve('before')
  .then(console.log)
  .then(after);
// выведет "before", "after"

setTimeout((after) => {
  console.log('before');
  after();
}, 0, after);
// выведет "before", "after"
```

## Error-first callback

Большинство асинхронных методов в *NodeJS* следуют *идиоматическому* (свойственному языку) *паттерну* **Error-first callback**.

*Идея паттерна* заключается в следующем
* *Функция обратного вызова* *передаётся* в *аргументы метода*, производящего некоторую *асинхронную операцию*.
* Если *операция завершается ошибкой*, *первым аргументом функции обратного вызова* станет *экземпляр* `Error`. 
* Если *операция завершается успешно*, *первым аргументом* станет `null`, а *результат операции* (данные) будет передан *вторым аргументом*.

*Обработка ошибок* и *данных* выглядит следующим образом
```js
const fs = require('fs');

const readFileCallback = (err, data) => {
  if (err) {
    console.error('Read file error occured: ', err);
    /* обработать ошибку здесь */
  } else {
    console.log(data);
    /* обработать данные здесь */
  }
};

/* асинхронное чтение файла */
fs.readFile('/* ... */', readFileCallback);
```

Попытка выбросить ошибку `err` из функции обратного вызова является распространённой ошибкой, поскольку к моменту вызова `readFileCallback` код вокруг `fs.readFile` завершит своё выполнение. Ошибка не попадёт в `try..catch` и, скорее всего, приведёт к краху всего приложения.
```js
const readFileCallback = (err, data) => {
  if (err) {
    throw err; // так лучше не делать
  } else {
    console.log(data);
  }
};

try {
  fs.readFile('/* ... */', readFileCallback);
} catch (err) {
  /* ошибка сюда не попадёт */
  console.log(err);
}
```
Аналогичная ситуация с данными `data`. Их возврат при помощи `return` ничего не даст. Функция `fs.readFile` просто вызовет операцию чтения, но код не будет ждать её выполнения и пойдёт дальше. Поэтому возвращённое значение будет `undefined`.
```js
const readFileCallback = (err, data) => {
  if (err) {
    console.error('Read file error occured: ', err);
  } else {
    console.log(data);
    return data;
  }
};

const data = fs.readFile('/* ... */', readFileCallback);
console.log(data); // undefined
```

## Промиссификация

Есть *другой способ*: *промиссифицировать асинхронную функцию*. Например, промиссифицируем функции чтения и удаления файла.
```js
const fs = require('fs');

const readFile = filePath => new Promise((resolve, reject) => {
  fs.readFile(filePath, (err, data) => err ? reject(err) : resolve(data));
});

const removeFile = filePath => new Promise((resolve, reject) => {
  fs.unlink(filePath, err => err ? reject(err) : resolve());
});
```
```js
(async () => {
  try {
    const data = await readFile('input.txt');
    await removeFile('input.txt');
  } catch (e) {
    console.log(e);
  }
})();
```
Начиная с Node v11.0.0 можно использовать встроенное решение.
```js
const fs = require('fs').promises;

(async () => {
  try {
    const data = await fs.readFile('input.txt');
    await fs.unlink('input.txt');
  } catch (e) {
    console.log(e);
  }
})();
```
