
- [Работа со строками](#работа-со-строками)
- [Регулярные выражения](#регулярные-выражения)
- [Семиотика, синтаксис и семантика](#семиотика-синтаксис-и-семантика)
 
# Работа со строками
- [Конкатенация](#конкатенация)

## Конкатенация

# Регулярные выражения
- [О регулярных выражениях](#о-регулярных-выражениях)
- [Классы символов](#классы-символов)

## О регулярных выражениях

**Регулярное выражение** (англ. `regular expression`, `regex`, `regexp`) - это **поисковый шаблон** (англ. `search pattern`), который *позволяет* по *заданным* в нём *правилам* *найти подстроку* (некоторую *последовательность символов*) в некоторой *строке*. 

Таким образом, *основной целью использования регулярного выражения* является *проверка на соответствие* (англ. `match`) некоторой *строки заданному шаблону*. 

Для *написания поискового шаблона* используется специальный *формальный язык*, который *поддерживается* во многих *системах* и *языках программирования*.

### Регулярное выражение позволяет задавать в шаблоне
* *конкретные символы* алфавита,
* *класс символов* (цифры, буквы, знаки пунктуации),
* *промежуток символов*, которые должны быть *включены* в поиск или должны быть *исключены* из него,
* *количество символов заданного вида*,
* *логические условия* и многое другое.

Всё это мы *рассмотрим далее* на *примерах*.

### Когда регулярные выражения могут быть полезны
* при *поиске*: для *включения* удовлетворяющих шаблону *строк* в некоторую *выборку*
* при *валидации* строки: проверка сложность пароля, проверка правильности введённого номера телефона или электронной почты
* при *необходимости обрабатывать строку по-разному* в *зависимости* от того, что она *содержит*.

### Простейшее регулярное выражение
Подобно тому, как *строки* обычно *заключаются* в *одинарные*, *двойные* или *косые ковычки*:
```javascript
'hello'
"hi"
`welcome`
```
*регулярные выражения размещают между двумя косыми чертами* `/` (англ. `slash`):
```regex
/hey/
```
Это *позволяет интерпретатору (компилятору) отличить регулярное выражение* от *других выражений*. 

*Самим простым поисковым шаблоном* является *конкретная последовательность символов*. 

Например, выясним, *какие* из *указанных строк содержат подстроку* `"ay"`. *Регулярное выражение* примет вид `/ay/`.
```js
'Ray Palmer'.match(/ay/); // ['ay', index: 1] (начинается с 1-ого символа)
'Dorian Gray'.match(/ay/); // ['ay', index: 9] (начинается с 9-ого символа)
'Max Starling'.match(/ay/); // null (соответствий не найдено)
```

## Перечисление допустимых символов

### Перечисление символов

Регулярное выражение позволяет *перечислить допустимые символы*. Для этого используются **квадратные скобки** `[]` (англ. `square brackets`. *Читается* как *"любой символ из символов ..."*.

Например,
* регулярному выражению `[abc]` удовлетворяет каждый из символов `a`, `b`, `c` и никакой другой символ,
* регулярному выражению `[13579]` удовлетворяют только символы `1`, `3`, `5`, `7`, `9`
```js
'May'.match(/[abc]/) // ['a', index: 1]
'#ff00ff'.match(/[0123456789]/) // ['0', index: 2]
```

### Исключение перечисленных символов

Для *исключения* из *регулярного выражения символов* используется символ **карет** `^` (англ. `caret`) *внутри квадратных скобок*. *Читается* как *"любой символ кроме символов из интервала"*.

### Включение символа из интервала символов

Для *включения* в *регулярное выражение* *символов* из *некоторого интервала символов* используется символ `-` (англ. hyphen) внутри *квадратных скобок*. *Читается* как *"любой символ из интервала ..."*.

Например,
* *регулярное выражение* `/[0-9]/` *удовлетворяет любому* из *символов* `0, 1, 2, 3, 4, 5, 6, 7, 8, 9` (то есть *любой цифре*),
* *регулярное выражение* `[A-Z]` - *любой большой букве латинского алфавита*,
* *регулярное выражение* `[a-z]` - *любой маленькой букве латинсткого алфавита*,
* а *выражение* `[А-я]` - *любой букве* (*большой* или *маленькой*) русского алфавита*
```js
'Hello'.match(/[A-Z]/) // ['H', index: 0]
'Hi'.match(/[a-z]/) // ['i', index: 1]
'Hey'.match(/[0-9]/) // null
'Greetings user2000'.match(/[0-9]/) // ['2', index: 14]
```

### Исключение символа из интервала символов

Для *исключения* из *регулярного выражения символов* из *некоторого интервала* так же используется символ **карет** `^` (англ. `caret`).

Например,
```js
'Greetings user2000'.match(/[^0-9]/) // ['G', index: 0]

'15,000 dollars'.match(/[0-9]/) // ['1', index: 0]
'15,000 dollars'.match(/[^0-9]/) // [',', index: 2]

'GitHub'.match(/[^A-z]/) // null
```

 ### Комбинация перечислений символов

Например, *регулярное выражение* `[A-Za-z0-9]` удовлетворяет *любой букве латинского алфавита или цифре*, а *регулярное выражение* `[^A-Za-z0-9]` удовлетворяет *любому символу*, который *не является буквой латинского алфавита и числом*.
```js
"Hey! How is it going?".match(/[A-Za-z0-9.]/) // ['H', index: 0]
"Hey! How is it going?".match(/[^A-Za-z0-9]/) // ['!', index: 3]
```


## Классы символов

**Классы символов** (англ. `character classes`) чем-то *напоминают типы данных*: они ограничивают область допустимых значений (в данном случае, область допустимых символов).

# Семиотика, синтаксис и семантика

Говоря простыми словами, **язык программирования** - это средство коммуникации между программистом и компьютером. С помощью него программист может "рассказать" компьютеру, что нужно сделать (исполнить, выполнить) последнему.

В любой системе, где используются знаки, выделяют 

 Рассматривая любую знаковую систему (в том числе и язык программирования), обычно выделяют синтаксис -- правила построения сообщений в этой системе, семантику -- правила истолкования сообщений тем, кому они адресованы, а также прагматику, сопоставляющую сообщения желаниям того, от кого они исходят.
