# Klavio

## Переменные
Обращение к переменной `foo`:
```hbs
{{ foo }}
```
Если использовать переменную в тексте:
```hbs
<span> Hello, {{ name }}! </span>
```

### Передача значения по умолчанию
```hbs
<span> Hello, {{ name|default:'Sir' }}! </span>
```

## Циклы
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

## Операторы if..else
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
