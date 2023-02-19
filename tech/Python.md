
# Types

## String

### Concat string
Binary `+` operator is used for *string concatenation*.
```py
hi = "Hi"
there = "There"
result_str = hi + " " + there
print(result_str)
```
### Convert to string

```py
n = 17
s = str(num) 
print(s) // "17"
```

## Массив `array`


## Кортеж `tuple`

## Словарь `dictionary`

## JSON
### Parse JSON to Dictionary
```py
import json

repo_json =  '{ "user":"Max-Starling", repo: "Notes" }'

# Parse repo_json
repo_dict = json.loads(repo_json)

# Access a dictionary field
print(repo_dict["user"])
```
### Convert to JSON
```py
import json

repo_dict = {
  "user":"Max-Starling"
  repo: "Notes"
}

# Convert to JSON
repo_json = json.dumps(x)

# Print JSON
print(repo_json)
```
### Intends in JSON
You can set number of indents in JSON
```py
json.dumps(x, indent=4)
```
### JSON Types mapping table

| Python Type  | JSON   |
| ------- | ------ |
| dict    | Object |
| list, typle	  | Array  |
| str	    | String |
| int, float |	Number |
| True	| true |
| False	| false |
| None	| null |

# Типизация (`Typing`)

Python не требует от нас явного указания типов, но их указание помогает избежать множества ошибок, а ещё делает ваш код более понятным для других разработчиков.

Задать тип можно
* переменной после двоеточия `:`:
```py
BASE_URL: str = 'http://localhost:8080'
```
* аргументу функции после `:` и возвращаемому значению функции после `->`.
```py
def getFullUrl(baseUrl: str, endpoint: str) -> str:
    return f"{baseUrl}{endpoint}";
```
* параметру класса после `:` (причём в классах тип обязателен для каждого поля)
```py
class User:
    email: str
    age: int

testUser: User = {
    'email': 'test@email',
    'age': 0
}
```
Примитивные типы данных
* `str` - строковый тип
* `int` - целочисленный тип
* `float` - тип числа с правающей точкой
* `bool` - логический тип
* `complex` - тип комплексного числа, то есть числа с действительной и мнимой частью, содержащей мнимую единицу `j`

Сложные типы данных (например, состоящие из нескольких значений, возможно, разных типов данных) испортируются из библиотеки `typing`:
* `List[x]` - список, массив
* `Set[x]` - множество
* `Tuple` - кортеж
* `Dict[x, y]` - словарь, объект, ассоциативный массив ("ключ-значение")
* `Union` - объединение, перечисление допустимых типов.
```py
Union[int, str] # тип `int` или `str`
Union[int] == int # объединение одного типа эквивалентно этому типу
Union[int, str, int] == Union[int, str] # повторяющиеся типы исключаются из объединения
Union[Union[int, str], float] == Union[int, str, float]
Union[int, str] == Union[str, int] # порядок задания типом в объединении не важен
```
* `Option` означает, что значение задавать не обязательно. Если значение не задать, ошибки не будет.
```py
Optional[T] # эквивалентно `Union[T, None]`
```
```py
from typing import Optional

class DatabricksConnectionStringConfig:
    hostname: str
    token: str
    port: Optional[int] = 443
```

# Функции

## Лямбда-функции

TODO

Лямбда-выражение в программировании — специальный синтаксис для определения функциональных объектов, заимствованный из λ-исчисления.

Лямбда-выражения принимают две формы. Форма, которая наиболее прямо заменяет анонимный метод, представляет собой блок кода, заключенный в фигурные скобки. Это — прямая замена анонимных методов. Лямбда-выражения, с другой стороны, предоставляют ещё более сокращенный способ объявлять анонимный метод и не требуют ни кода в фигурных скобках, ни оператора return. Оба типа лямбда-выражений могут быть преобразованы в делегаты.

Во всех лямбда-выражениях используется лямбда-оператор =>, который читается как «переходит в» (в языках Java, F# и PascalABC.NET используется оператор ->). Левая часть лямбда-оператора определяет параметры ввода (если таковые имеются), а правая часть содержит выражение или блок оператора. Лямбда-выражение x => x * 5 читается как «функция x, которая переходит в x, умноженное на 5»
```py
lambda <args>: <statement>
```

Лямбда-фунция возведения в степень.
```py
pow = lambda x, power: x ** power
print(pow(2, 5))
```
что эквивалентно
```py
def pow(x, power):
  return x ** power
print(pow(2, 5))
```



# `*args` and `**kwargs`

Оператор `*args` (`asterisk operator`) используется для *"распаковки" перечисляемых объектов* (англ. `iterable`), например, таких как *массив* и *список аргументов функции*.

То есть, `*[1, 2, 3]` это ничто иное, как `1, 2, 3`.

Оператор `**kwargs` (`double asterisk operator`) используется для *"распаковки" объектов*.

То есть, `**{ name: "Max", sex: "male" }` это ничто иное, как `name="Max", sex="male"`.

Не путайте унарный оператор `**kwargs`, который применим только к объектам, с бинарным оператором возведения числа в степень `number ** power`.

Не путайте унарный оператор `*args`, который применим только к перечисляемым объектам, с бинарным оператором умножения чисел `a * b`.

Операторы `*args` и `**kwargs` являются аналогами оператора `...` (`spread operator`) в JavaScript.

## Массивы и `*args`

При применении унарного оператора `*` к *массиву* вы передаёте в выражение все значения массива через запятую. Например, это может быть полезным при построении нового массива путём расширения предыдущего.

На примере ниже происходит слияние двух массивов.
```py
arr = [3, 4]
another_arr = [1, 2]
merged = [*another_arr, *arr]
print(merged) # [1, 2, 3, 4]
```
```javascript
// JavaScript
const foo = [3, 4];
const bar = [1, 2];
const merged = [...bar, ...foo];
console.log(merged); // [1, 2, 3, 4]
```
Ещё немного слияний массивов для закрепления.
```py
foo = [1, 2, 3]
bar = [*foo, 4]
print(bar) # [1, 2, 3, 4]

baz = [0, *bar, 5]
print(baz) # [0, 1, 2, 3, 4, 5]
```
```javascript
// JavaScript
const foo = [1, 2, 3];
const bar = [...foo, 4];
console.log(bar); // [1, 2, 3, 4]

const baz = [0, ...bar, 5]
console.log(baz); // [0, 1, 2, 3, 4, 5]
```
## Параметры функции и `*args`
Аналогично примерам выше, можно "передать значения массива через запятую" в качестве параметров функции. 
```python
def sum(a, b):
    return a + b
numbers = [3, 7]
print(sum(*numbers)) # 10
```
```js
function sum(a, b) {
    return a + b;
}

const numbers = [3, 7];
console.log(sum(...numbers)); // 10
// что эквивалентно
console.log(sum(3, 7)); // 10
```
## Аргументы функции и `*args`
Применим `*args` к аргументами функции, вы получите функцию, которая может обрабатывать любое количество аргументов вплоть до бесконечности. Собранные аргументы помещаются в кортеж `tuple`.
```python
def sum(*numbers):
    result = 0
    for num in numbers:
        result = result + num
    print(result)

sum(3, 7) # 10
sum(3, 7, 2) # 12
sum(3, 7, 2, 5) # 17
```
```js
// JavaScript
function sum(...numbers) {
    let result = 0;
    for (const num of numbers) {
      result += num;
    }
    console.log(result);
}

sum(3, 7); // 10
sum(3, 7, 2); // 12
sum(3, 7, 2, 5); // 17
```
## Объекты и **kwargs

Оператор `**kwargs` (keyword args) может быть использован для "распаковки" объектов. 

Например, для слияния двух и более объектов. 
```py
user_personal_info = { "first_name": "Max", "last_name": "Starling" }
user_job_info = { "job": "engineer", "experience": "6 years" }
user_info = { **user_personal_info, **user_job_info }
print(user_info) # { "first_name": "Max", "last_name": "Starling", "job": "engineer", "experience": "6 years" }
```
```js
// JavaScript
const userPersonalInfo = { firstName: "Max", lastName: "Starling" };
const userJobInfo = { job: "engineer", experience: "6 years" };
const userInfo = { ...userPersonalInfo, ...userJobInfo };
console.log(userInfo); // { firstName: "Max", last_name: "Starling", job: "engineer", experience: "6 years" }
```
или для создания копии объекта с возможностью расширения его свойств
```py
file_info = { "file_name": "test.json", "size": "1376kb" }
print({ **file_info, "created_at": "15/12/2022" }) # { "file_name": "test.json", "size": "1376kb", "created_at": "15/12/2022" }
```
```js
// JavaScript
const fileInfo = { fileName: "test.json", size: "1376kb" }
console.log({ ...fileInfo, createdAt: "15/12/2022" }) // { fileName: "test.json", size: "1376kb", createdAt: "15/12/2022" }
```

## Аргументы функции и `**kwargs`
Функции в питоне могут принимать **ключевые аргументы** (англ. `keyword arguments`) в виде `kwarg=value`. В этом случае *порядок передачи параметров* функции *не важен*.
```py
def print_user_info(status, name, age): # порядок не важен
    print("\nUser info:")
    print("- {} is {}".format("name",name))
    print("- {} is {}".format("age",age))
    print("- {} is {}".format("status",status))

print_user_info(name="Alex", age=22, status="married")
# User info:
# - name is Alex
# - age is 22
# - status is married
```
Чтобы функция могла принимать любое количество параметров и при этом было удобно их обрабатывать, можно использовать `**kwards`, который соберёт все параметры функции в словарь `dict` (англ. `dictionary`).
```py
def print_user_info(**user_info):
    # type(user_info) = dict
    print("\nUser info:")
    for key, value in user_info.items():
        print("- {} is {}".format(key,value))

print_user_info(name="Alex", age=22, status="married")
# User info:
# - name is Alex
# - age is 22
# - status is married
print_user_info(name="Kate", age=18, sex="female", status="single")
# User info:
# - name is Kate
# - age is 18
# - sex is female
# - status is single
```
```js
// JavaScript
// ключевые аргументы не доступны в языке - передаём объект с любым количеством свойств
function printUserInfo(userInfo) {
  console.log("\nUser info:");
  for (const [key, value] of Object.entries(userInfo)) {
      console.log(`- ${key} is ${value}`);
  }
}
printUserInfo({ name: "Alex", age: 22, status: "married" })
// User info:
// - name is Alex
// - age is 22
// - status is married
printUserInfo({ name: "Kate", age: 18, sex: "female", status: "single" })
// User info:
// - name is Kate
// - age is 18
// - sex is female
// - status is single
```
## Все виды аргументов сразу в функции
`error_type` - одиночный позиционный аргумент.
`*messages` собирает позиционные элементы, начиная со второго и до последнего, в кортеж `tuple`.
`**metadata` собирает все ключевые аргументы в словарь `dict`.
```py
def print_errors(error_type, *messages, **metadata):
    # type(user_info) = dict
    print(f"{error_type}:")
    for index, message in enumerate(messages):
    	print(f" * Error #{index}: - {message} *")
    for key, value in metadata.items():
        print("** Metadata: - {} is {} **".format(key,value))

print_errors(
    "ValidationError",
    "password is too short",
    "password must contain at least one special character",
    "password must contain at least one capital letter",
    email="test@smail.com",
    browser="Safari"
)
# ValidationError:
#  * Error #0: - password is too short *
#  * Error #1: - password must contain at least one special character *
#  * Error #2: - password must contain at least one capital letter *
# ** Metadata: - email is test@smail.com **
# ** Metadata: - browser is Safari **
```
```js
// JavaScript
// ключевые аргументы в языке отсутствуют, оператор `...` должен быть в конце,
// значит используем объект в качестве `metadata` и меняем его местами с `messages`
function printErrors(errorType, metadata, ...messages) {
    console.log(`${errorType}:`)
    for (const [index, message] of Object.entries(messages)) {
    	console.log(` * Error #${index}: - ${message} *`)
    }
    for (const [key, value] of Object.entries(metadata)) {
	console.log(`** Metadata: - ${key} is ${value} **`)
    }
}

printErrors(
    "ValidationError",
    { email: "test@smail.com", browser: "Safari" },
    "password is too short",
    "password must contain at least one special character",
    "password must contain at least one capital letter"
)
// ValidationError:
//  * Error #0: - password is too short *
//  * Error #1: - password must contain at least one special character *
//  * Error #2: - password must contain at least one capital letter *
// ** Metadata: - email is test@smail.com **
// ** Metadata: - browser is Safari **
```


## Полезные функции

### `is_json`

```py
import json

def is_json(myjson):
  try:
    json.loads(myjson)
  except ValueError as e:
    return False
  return True
```
