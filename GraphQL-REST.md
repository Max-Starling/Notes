- [REST](#rest)
- [GraphQL](#graphql)
  - [Преимущества GraphQL](преимущества-graphql)
- [REST на практике](#rest-на-практике)
  - [Данные в QUERY](#данные-в-query)
  - [Данные в BODY](#данные-в-body)
  - [Данные в PARAMS](#данные-в-params)

# REST

# GraphQL

**GraphQL** — *язык запросов* (query language) для API. 

Клиент посылает **запрос** (query) к сервису GraphQL и получает ответ в виде JSON-объекта по указанной в запросе схеме.

Например, клиент может послать запрос.
```Graphql
query {
  currentUser {
    id
    username
    role
  }
}
```
Если сервер позволяет получить данные по заданной схеме, по клиенту придётся ответ по типу.
```json
{
  "data": {
    "currentUser": {
      "id": "1",
      "username": "Notes",
      "role": "Admin"
    }
  }
}
```

## Запросы query и mutation

Существуют два типа запросов (requests): *query* и *mutation*. 

**Запрос query** отвечает за получение данных, он не может их изменять (аналог GET-запроса в REST).
```Graphql
query {
  users {
    id
    username
  }
}
```

**Запрос mutation** отвечает за изменение данных (объединяет в себе возможности POST, PUT, DELETE и других в REST).
```Graphql
mutation {
  logOut
}
```
## Схемы и типы

Все поля, которые может получить клиент, должны быть описаны в **типе** (type).

Типы `Query` и `Mutation` являются типами по умолчанию. В них должны быть описаны все возможные запросы.

```Graphql
type Query {
  currentUser: User
}

type Mutation {
  logOut
}
```
```Graphql
schema {
  query: Query
  mutation: Mutation
}
```
Могут создаваться пользовательские типы.
```Graphql
type User {
  id: ID
  username: String
  role: String
  image: String
}
```

Если схема в запросе с клиента не удовлетворяет схеме на сервере, то возникнет валидационная ошибка. Например, поле `age` отсутствует в типе `User`.
```json
{
  "data": null,
  "errors": [
    {
      "...": "...",
      "message": "Validation error of type FieldUndefined: Field 'age' in type 'User' is undefined
    }
  ]
}
```


## Взаимодействие с сервером

### Отправка query
```GraphQL
query Users {
  users {
    id
    username
    role
  }
}
```

### Отправка query с переменными
```GraphQL
query User($userId: ID!) {
  user(userId: $userId) {
    id
    username
    role
  }
}
```
В этом случае `query variables`
```GraphQL
{
  "userId": "auth0|72d45e398924235638341891"
}
```



## Преимущества GraphQL

* *Строгая типизация*. Конкретная схема, полностью описывающая, как можно работать с данными.
```gql
type User {
  id: ID!
  username: String!
}

type Article {
  id: ID!
  title: String!
  description: String
  author: User!
}

type Query {
  articles: [Article]
  article(id: ID!): Article
}
```
* Клиент всегда запрашивает только то, что ему нужно. Он не может получить те поля, которые не запрашивал, ровно как и те, которые не предусмотрены описанной схемой.

Следующий запрос вернёт `articles`, содержащие только поля `id` и `title`. Все эти поля должны присутствовать в схеме запроса на бэкенде (описана выше), иначе будет ошибка.
```gql
query {
  articles {
    id
    title
  }
}
```
* Один запрос может объединять в себе несколько различных ресурсов.

Следующий запрос объединяет в себе информацию, собранную из `Articles` и `Users`.
```gql
query {
  articles {
    id
    title
    description
    author {
      id
      username
    }
  }
}
```

# REST на практике

## Данные в QUERY
* Форма запроса на клиенте.
```http
GET /route?field=value&anotherField=anotherValue
```
```js
/* axios */
axios.get('/route', {
  params: {
    field: 'value',
    anotherField: 'anotherValue',
  },
});
```
* Форма запроса на сервере.
```http
GET /route
```
* Получение данных из запроса на сервере.
```js
/* Express */
app.get('/route', (request, response) => {
  const { field, anotherField } = request.query;
});
```

Передача массива `arr [1, 3, 7]` с клиента.
```http
GET /route?arr[]=1&arr[]=3&arr[]=7
```
Передача объекта `obj { foo: '1', bar: '7' }` с клиента.
```http
GET /route?obj[foo]=1&obj[bar]=7
```

## Данные в BODY
* Форма запроса на клиенте.
```http
POST /route
field=value&anotherField=anotherValue
```
```js
/* axios */
axios.post('/route', {
  field: 'value',
  anotherField: 'anotherValue',
});
```
* Форма запроса на сервере.
```http
POST /route
```
* Получение данных из запроса на сервере.
```js
/* Express */
app.post('/route', (request, response) => {
  const { field, anotherField } = request.body;
});
```

## Данные в PARAMS
* Форма запроса на клиенте.
```http
GET /route/paramValue
```
```js
/* axios */
axios.get(`/route/{paramValue}`);
```
* Форма запроса на сервере.
```http
GET /route/:param
```

* Получение данных из запроса на сервере.
```js
/* Express */
app.post('/route/:param', (request, response) => {
  const { param } = request.params;
});
```

Пример отправки нескольких параметров.
```http
PATCH /users/17/name
PATCH /users/:id/:field
```
