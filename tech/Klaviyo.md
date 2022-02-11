# Klavio

Klavio использует Django (Python) в качестве *шаблонизатора* (англ. `template engine`).

## Переменные
Обращение к переменной `foo`:
```hbs
{{ foo }}
```
Если использовать переменную в тексте:
```hbs
<span> Hello, {{ name }}! </span>
```

### Использование встроенных методов
Например, для применения метода с названием `method` с переменной `foo` используется следующий синтаксис:
```hbs
{{ foo|method }}
```
Если метод может принимать параметры, то вызов метода `method` с параметром `param` имеет вид:
```hbs
{{ foo|method:param }}
```
Если нужно применить к переменной `bar` последовательно два метода `method1` и `method2`, то
```hbs
{{ foo|method1:param1|method2:param2 }}
```

### Переменные со знаком `$`
Если переменная из контекста имеет знак `$` в своём названии (например, `$value`), то к ней нельзя обратиться как к `{{ $value }}`, поскольку это приведёт к синтаксической ошибке.
Такие переменные нужно находить с помощью метода `lookup`, передавая ему в качестве параметра название переменной строкой:
```hbs
{{ event|lookup:'$value' }}
```

### Передача переменной значения по умолчанию
```hbs
<span> Hello, {{ name|default:'Sir' }}! </span>
```

## Циклы
В Django существует только один вид циклов: `for..in`. Циклы `foreach`, `while`, `for`, `do..while` отсутствуют.
### Цикл `for`
```django
<ul>
{% for unit in unit_list %}
    <li>{{ unit.name }} {{ unit.price }}</li>
{% empty %}
    <li>Sorry, no units in this list.</li>
{% endfor %}
</ul>
```
## Условные операторы
В Django существует только один вид условных операторов: `if..else`. Тернарный оператор `a ? b : c` отсуствует.

### Операторы if..else
```django
{% if unit_list and unit_list|length > 1 %}
    Number of units: {{ unit_list|length }}
{% elif unit_list|length == 1 %}
    There is only one unit left.
{% else %}
    There are no units.
{% endif %}
```


## Методы массивов
* `length` возвращает длину массива с названием `array` (также подходит для строк)
```hbs
{{ array|length }}
```
* `length_is` возвращает логическое значение `true/false`, если длина массива `array` совпадает с указанной (например, `3`)
```hbs
{{ array|length_is:"3" }}
```

