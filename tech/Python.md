
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

## Dictionary

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
Ещё немного слияний массивов для закрепления
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
Применим `*args` к аргументами функции, вы получите функцию, которая может обрабатывать любое количество аргументов вплоть до бесконечности.
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
Чтобы функция могла принимать любое количество параметров и при этом было удобно их обрабатывать, можно использовать `**kwards`, который соберёт все параметры функции в `Dictionary`.
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
