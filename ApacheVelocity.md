
## Установка значения переменной

```js
#set($size = 10)
```
```js
#set($title = "Notes")
```

## Использование переменной
```js
{
  "size": $size,
  "title": "$title"
}
```

Нельзя использовать выражение в качестве значения как в примере ниже. Нужно выносить выражение в переменную и использовать её. Иначе в значение попадёт само выражение, а не его результат.
```js
{
  "size": $size + 1,
}
/* результат 10 + 1, а не 11 */
```

## Инкремент
```js
#set($counter = 0)
#set($counter = $counter + 1)
```

## Условный оператор
```js
#if($a > $b)          
    #set($max = $a)
#else
    #set($max = $b)
#end
```
```js
#if($counter == 0)
  #set($str = "zero")
#elseif($counter == 1)
  #set($str = "one")
#else
  #set($str = "another number")
#end
```

## Цикл
```js
#foreach($entry in $array)
  #set($prop = $entry.get("property"))
#end
```

## Массив

### Объявление
```js
#set($array = [42, "a string", $counter])
```
### Обращение к элементу
```js
$array.get(1)
$array[1]
```
### Проверка на пустоту
```js
#if ($myMap.isEmpty()) #end
#if ($myMap.size() == 0) #end
```
### Добавление элементов
```js
$array.add(17)
```
