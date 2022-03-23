
## Обращение к элементу
Обращение к элементу при помощи `jQuery` работает аналогино методу `document.querySelector`:
```js
document.querySelector('#element-id') // обращение к элементу по атрибуту id
document.querySelector('.element-class') // обращение к элементу по атрибуту class 
```
Только для подобного поиска элемента используется метод `$`.
```js
$('#element-id')
$('.element-class')
```


## Сокрытие элемента
*Убрать элемент* со страницы:
```js
$('.element-class').hide();
/* или */
$('.element-class').css('display', 'none');
```
*Вернуть элемент* на страницу:
```js
$('.element-class').show();
/* или */
$('.element-class').css('display', 'block');
```
