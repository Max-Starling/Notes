
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

## Цикл
```pascal
#foreach($entry in $array)
  #set($prop = $entry.get("property"))
#end
```
