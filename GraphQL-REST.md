# REST

# GraphQL

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
```
GET /route?arr[]=1&arr[]=3&arr[]=7
```

### Данные в BODY
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

### PARAMS
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
