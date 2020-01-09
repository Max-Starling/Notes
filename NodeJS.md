- [Асинхронное программирование](#асинхронное-программирование)
  - [Функции обратного вызова](#функции-обратного-вызова)
  
# Асинхронное программирование

## Функции обратного вызова

**Функция обратного вызова** (Callback) — функция, которая передаётся параметром в другую функцию. 

Примером *функции обратного вызова* является `compareFunction` в `[].sort(compareFunction)`, использующаяся в качестве алгоритма сравнения двух элементов массива.
```js
const compareFunction = (a, b) => a - b;
[1, 3, 2].sort(compareFunction); // [1, 2, 3]
```

## Promise

*Параметры Promise executor* `resolve` и `reject` являются *функциями обратного вызова*.
```js
const promise = new Promise((resolve, reject) => {
  setTimeout(() => resolve('resolved'), 3000);
});

promise.then(result => console.log(result)); // resolved (через ~3 секунды)
```

## Работа с файлами

### Удаление файла

```js
const fs = require('fs')

const removeFile = (filePath) => new Promise((resolve, reject) => {
  fs.unlink(path, err => err ? reject(err) : resolve());
});
```
