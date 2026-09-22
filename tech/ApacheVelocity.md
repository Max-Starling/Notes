
## Установка значения переменной

```velocity
#set($size = 10)
```
```velocity
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
```velocity
#set($counter = 0)
#set($counter = $counter + 1)
```

## Условный оператор

## if..else
```velocity
#if($a > $b)          
    #set($max = $a)
#else
    #set($max = $b)
#end
```


## elseif
```velocity
#if($counter == 0)
  #set($str = "zero")
#elseif($counter == 1)
  #set($str = "one")
#else
  #set($str = "another number")
#end
```

## Краткая запись
```velocity
#if($a > $b)it's true!#{else}it's not!#end
```
## Цикл
```velocity
#foreach($entry in $array)
  #set($prop = $entry.get("property"))
#end
```

## Массив

### Объявление
```velocity
#set($array = [42, "a string", $counter])
```
### Обращение к элементу
```js
$array.get(1)
$array[1]
```
### Проверка на пустоту
```velocity
#if ($myMap.isEmpty()) #end
#if ($myMap.size() == 0) #end
```
### Добавление элементов
```js
$array.add(17)
```
