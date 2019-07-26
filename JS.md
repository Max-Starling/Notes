# Основы JavaScript

##
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
// что эквивалентно:
Promise.all(['n', 'o', 't', 'e', 's']).then(res => console.log(res));
```
