- [О MongoDB](#о-mongodb)
- [Основные понятия](#основные-понятия)
  - [Документ](#документ)
  - [Коллекция](#коллекция)
- [Операции над данными](#операции-над-данными)
  - [Агрегации](#агрегации)
  - [Работа с типами данных](#работа-с-типами-данных)
- [Обновление данных в массиве](#обновление-данных-в-массиве)
  - [Оператор $push](#оператор-push)
  - [Оператор $pull](#оператор-pull)
- [Выборка элементов](#выборка-элементов)
  - [Поиск объекта в массиве объектов (`$elemMatch`)](#поиск-объекта-в-массиве-объектов-elemmatch)

# О MongoDB

**MongoDB** - это *документно-ориентированная* база данных (англ. document-oriented database), что означает, что *каждый объект* (реальный или абстрактный) *представляется* в виде *отдельного документа*, *хранящегося* в *базе данных* в *формате JSON*.

*MongoDB* относят к *типу NoSQL баз данных* или *нереляционных баз данных*, поскольку *MongoDB* не *использует синтаксис языка SQL* и *реляционную модель данных*. 

# Основные понятия
- [Документ](#документ)
- [Коллекция](#коллекция)


## Документ

Любой объект в MongoDB представляются в виде **документа** (англ. document). 

*Каждый документ представляет* собой *множество* пар **"ключ-значение"** (англ. key-value). 

Ключи также называют **полями** (англ. fields). 

Каждый документ обязательно хранит поле `_id`, по которому можно уникально идентифицировать любой документ. Это поле имеет тип `ObjectId` и генерируется автоматически при добавлении документа, если его не задать.

*Пример документа*, *хранящего данные* некоторого *сообщения*, отправленного в некоторой *системе* для *обмена сообщениями*:
```js
{
  "_id": ObjectId("60b3a171e3168e00b9f28e33"),
  "text": "Go for it.",
  "sender": ObjectId("5f694dcf36a0f350f8ea75f3"),
  "recipients": [
    ObjectId("5f694dce36a0f350f8ea75cf"
  ]
}
```

Аналагом документа в SQL является **строка таблицы** (англ. row), а одному *полю* соответствует **столбец таблицы** (англ. column).

## Коллекция
Несколько объектов, обладающих схожими свойствами, объединяют в **коллекцию** (англ. collection).

Пример коллекции `users`:
```js
[{
  "_id": ObjectId("3ae0017l0ff1234567b0be7e"),
  "username": "Max-Starling",
  "following": [
    ObjectId("5f694d9936a0f350f8ea6e83")
  ],
  "followers": [
    ObjectId("5f694d9936a0f350f8ea6e83"),
    ObjectId("4a694d7196e3f350f4ea3e16")
  ],
  "settings": {
    "mode": "dark"
  },
  "createdAt": "2017-07-17T01:07:01.787+00:00",
},
{
  "_id": ObjectId("4a694d7196e3f350f4ea3e16"),
  "username": "Manfredi",
  "following": [
    ObjectId("3ae0017l0ff1234567b0be7e")
  ],
  "followers": [],
  "settings": {
    "mode": "light",
    "fontSize": 12,
  },
  "createdAt": "2018-08-18T00:04:01.123+00:00",
}]
```

# Операции над данными
- [Агрегации](#агрегации)
- [Доступные операции в методе aggregate](#доступные-операции-в-методе-aggregate)
- [Работа с типами данных](#работа-с-типами-данных)


## Агрегации
- [Определения и разновидности агрегаций](#определения-и-разновидности-агрегаций)
- [Доступные операции в методе aggregate](#доступные-операции-в-методе-aggregate)

### Определения и разновидности агрегаций

**Агрегация** (от лат. `aggregatio` - *присоединение*, англ. `agregation`) в *общем случае* означает *процесс объединения* некоторых *объектов* в одну *группу* ( (англ. `a cluster of things`, `a collection of items`).

<!-- В рамках MongoDB агрегация представляет собой составную поисковую операцию из нескольких последовательных операций.  -->

<!-- Одной из таких операций является операция группировки, которая уменьшает количество -->

В терминалогии баз данных **агрегациями** или **агрегационными функциями** (англ. `aggregate function`) называют *операции*, которые *преобразуют* некоторую *заданную группу значений* (a set of values) в *одно значение* (single value).

Например, такими агрегационными функциями являются: 
* *Сумма* (`sum`).
* *Подсчёт количества элементов* (`count`).
* *Вычисление среднего значения* (`avg`).
* *Нахождение минимума* (`min`).
* *Нахождение максимума* (`max`).


К *агрегациям* также *относят операцию группировки* (`group`).

В *JavaScript* нечто *подобное* можно *сделать* с *массивом* при помощи *метода* `Array.reduce()`:
```js
const values = [1, 2, 3];

const sum = values.reduce((sum, value) => sum + value, 0);
console.log(sum); // 6

const count = values.reduce((count, value) => count + 1, 0);
console.log(count); // 3

const avg = values.reduce((sum, value) => sum + (value / count), 0);
console.log(avg); // 2

const min = values.reduce((sum, value) => Math.min(sum, value));
console.log(min); // 1

const max = values.reduce((sum, value) => Math.max(sum, value));
console.log(max); // 3
```

Так, например, при помощи агрегации можно выбрать документы из коллекции, сгруппировать их по некоторому критерию и оставить только необходимые поля.

В MongoDB агрегирование чаще всего осуществляется за счёт метода `collection.aggregate(pipeline)`, где `pipeline` - массив, каждый объект которого описывает некоторую операцию над данными.

<!-- Например, нам нужно найти определённые документы в коллекции, сгруппировать их по определённому принципу. -->

### Доступные операции в методе aggregate
#### Операция $match
#### Операция $group
#### Операция $project

### Пример агрегации с группировкой и подсчётом

В примере ниже мы ищем сотрудников компании с зарплатой больше 1000, группируем их по их роду деятельности и считаем количество сотрудников в каждой группе.
```js
const pipeline = [
  { $match: { salary: { $gte: 1000 } },
  {
    $group: {
      _id: '$profession',
      number: { $sum: 1 },
    },
  },
  {
    $project: {
      _id: 0,
      profession: '$_id',
      number: 1,
    },
  },
]);
];

/* workers: [
  { _id: 1, salary: 500, profession: 'cleaner' },
  { _id: 2, salary: 1200, profession: 'developer' },
  { _id: 3, salary: 1500, profession: 'developer' },
  { _id: 3, salary: 1000, profession: 'tester' },
  { _id: 4, salary: 900, profession: 'tester' },
] */

const workersCollection = db.collection('workers');

const bigSalaryInfo = await workersCollection.aggregate(pipeline);

/* bigSalaryInfo: [
  { profession: 'developer', number: 2 },
  { profession: 'tester', number: 1 },
] */
```



## Работа с типами данных
- [Получение документов по заданному типу данных](#получение-документов-по-заданному-типу-данных)
- [Приведение типа некоторого поля документов коллекции](#приведение-типа-некоторого-поля-у-документов-коллекции)

Допустимые типы данных:
```js
const MONGO_TYPES = {
  bool: 'bool',
  date: 'date',
  decimal: 'decimal',
  double: 'double',
  int: 'int',
  long: 'long',
  objectId: 'objectId',
  string: 'string'
}
```

### Получение документов по заданному типу данных
 
```js
const animalsCollection = db.collection('animals');
const document animalsCollection.find({
  age: { $type: 'int' },
})
```

### Приведение типа некоторого поля у документов коллекции

Допустимые методы приведения типов:
```js
const MONGO_CAST_METHODS= {
  bool: '$toBool',
  date: '$toDate',
  decimal: '$toDecimal',
  double: '$toDouble',
  int: '$toInt',
  long: '$toLong',
  objectId: '$toObjectId',
  string: '$toString',
};
```
Код ниже *обновляет только* те *документы коллекции* `bills`, *значение поля* `amount` которых имеет *строковый тип*, при этом *производится приведение* этого *значения* к *числовому типу* `double`.
```js
const billsCollection = db.collection('bills');

/* bills: [
  { _id: ObjectId(...), "amount': "123.50" },
  { _id: ObjectId(...), "amount': 17 }
] */

await billsCollection.update(
  {
    "amount": { $type: 'string' },
  },
  [{
    $set: {
      "amount": { $toDouble: "$amount" },
    },
  }],
  { multi: true },
);

/* bills: [
  { _id: ObjectId(...), "amount': 123.5 },
  { _id: ObjectId(...), "amount': 17 }
] */
```

Метод ниже *находит все документы колекции* `collection`, *значение поля* `fieldName` которых *имеет строковый тип*, и *приводит* их к *типу* `date`.
```js
const castStringFieldToDate = (collection, fieldName) => {
  return collection.update(
    {
      `${fieldName}`: { $type: 'string' },
    },
    [
      {
        $set: {
          `${fieldName}`: {
            $toDate: `$${fieldName}`,
          },
        },
      },
    ],
    { multi: true },
  );
}

/* ... */

const usersCollection = db.collection('users');
/* users: [{ _id: ObjectId(...), "joinDate': "2020-09-22T01:05:18.486Z" }] */
await castStringFieldToDouble(usersCollection, 'joinDate')
/* users: [{ _id: ObjectId(...), "joinDate': { $date: "2020-09-22T01:05:18.486Z" }}] */
```

# Обновление данных в массиве

## Оператор $push

*Оператор* `$push` *добавляет один элемент* `<newArrayItem>` в *массив* `<arrayFieldName>`.
```js
{ $push: { <arrayFieldName>: <newArrayItem> } }
```

```js
db.favoritePages.insertOne({
  _id: 17,
  pages: [3, 21, 73]
})
// favoritePages: [{ _id: 17, pages: [3, 21, 73] }]
db.favoritePages.updateOne(
   { _id: 17 },
   { $push: { pages: 101 } }
)
// favoritePages: [{ _id: 17, pages: [3, 21, 73, 101] }]
```
Для *добавления нескольких элементов* `<item1>`, `<item2>` в *массив* `<arrayFieldName>`

```js
{ $push: { <arrayFieldName>: { $each: [<item1>, <item2>] } } }
```

```js
db.favoritePages.updateOne(
   { _id: 17 },
   { $push: { pages: { $each: [277, 314] } } }
)
// favoritePages: [{ _id: 17, pages: [3, 21, 73, 101, 277, 314] }]
```

## Оператор $pull

# Выборка элементов

## Поиск объекта в массиве объектов (`$elemMatch`)

Допустим, у нас в схеме коллекции `workspace` содержится массив пользователей.
```js
/* Workspace Schema */
{
  uuid: String;
  name: String;
  users: [{
    uuid: String;
    name: String;
  }]
}
```
Если мы хотим найти воркспейс, содержащий определённого пользователя, следует использовать оператор `$elemMatch`.

Оператор `$elemMatch` находит документы, которые содержат в поле-массиве хотя бы один элемент, который удовлетворяет всем указанным параметрам вложенного query-запроса, передаваемого в качестве значения для `$elemMatch`.

Допустим, в коллекции имеется следующий документ.
```js
{
  id: 'w17',
  name: 'lab',
  users: [{
    id: 'u19',
    name: 'Kat',
  }, {
    id: 'u22',
    name: 'Tom',
  }]
}
```
Оба query-запроса ниже включат документ выше в выборку:
```js
{ users: { $elemMatch: { id: 'u19' } } }
{ users: { $elemMatch: { name: 'Tom' } } }
```
