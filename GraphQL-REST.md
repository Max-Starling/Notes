
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

# REST

## REST на практике

## Способы передать и получить данные

### QUERY

Форма URL запроса на клиенте.
```
/route?field=value&anotherField=anotherValue
```
На сервере.
```
/route
```
```js
const { field, anotherField } = request.query;
```

Передача массива `arr [1, 3, 7]` с клиента.
```
/route?arr[]=1&arr[]=3&arr[]=7
```

### BODY
```
/route
```
### PARAMS
```
/route/param
```