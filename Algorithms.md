## Сортировки

### Сортировка пузырьком (bubble sort)

*Cравниваются соседние элементы* и *меняются местами*, если *следующий* элемент *меньше предыдущего*.  
Требуется *несколько проходов по данным*.

```js
const bubbleSort = (array) => {
  const length = array.length;
  let tmp = 0;
  for (let i = 0; i < length; i += 1){
    for (let j = length - 1; j >= i + 1; j -= 1) {
      if (array[j] < array[j-1]) {
        tmp = array[j];
        array[j] = array[j-1];
        array[j-1] = tmp;
      }
    }
  }
}
```

https://function-x.ru/cpp_algoritmy_sortirovki.html  
https://habr.com/ru/post/204600/  
https://en.wikipedia.org/wiki/Timsort  
