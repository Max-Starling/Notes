# Elasticsearch

**Elasticsearch** (эластичный поиск) — распределённый (distributed), RESTful поисковой движок по всему тексту (full-text search).

*Elasticsearch* использует JSON-документы без схемы. Эти документы передаются при помощи REST API для сохранения их в хранилище и поиска.

## Настройка
* [Скачать архив](https://www.elastic.co/guide/en/elasticsearch/reference/current/install-elasticsearch.html#install-elasticsearch).
* Разархивировать и запустить `bin\elasticsearch` или `bin\elasticsearch.bat` (в зависимости от операционной системы).
* Поскольку общение с Elasticserach идёт при помощи HTTP-запросов, можно использовать Postman на порте `http://localhost:9200`.

## Индекс, тип и документ
**Индекс** (Index) — эквивалент базы данных в SQL или NoSQL.

**Тип** (Type) — эквивалент таблицы в SQL или коллекции в NoSQL.

**Документ** (Document) — эквивалент строки в SQL или документа в NoSQL.

## CRUD
* Создание индекса (`users`).
```http
PUT http://localhost:9200/users
```
```js
/* JSON response */
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "users"
}
```
* Создание типа (`user`) и документа в нём.
```http
POST http://localhost:9200/users/user
Content-Type: application/json

{"name":"Alen Stone","job":"Full-stack Enginer"}
```
```js
/* JSON response */
{
  "_index": "users",
  "_type": "user",
  "_id": "H3tVi3ABpFL-9AlTbAgj",
  "_version": 1,
  "result": "created",
  "_shards": {
      "total": 2,
      "successful": 1,
      "failed": 0
  },
  "_seq_no": 0,
  "_primary_term": 1
}
```
* Обновление документа (типа `user` по id).
```http
PUT http://localhost:9200/users/user/H3tVi3ABpFL-9AlTbAgj
Content-Type: application/json

{"name":"Richard Stone","job":"Full-stack Enginer"}
```
```js
/* JSON response */
{
  "_index": "users",
  "_type": "user",
  "_id": "H3tVi3ABpFL-9AlTbAgj",
  "_version": 3,
  "result": "updated",
  "_shards": {
      "total": 2,
      "successful": 1,
      "failed": 0
  },
  "_seq_no": 2,
  "_primary_term": 1
}
```
* Удаление документа (типа `user` по id).
```http
DELETE http://localhost:9200/users/user/H3tVi3ABpFL-9AlTbAgj
```
```js
/* JSON response */
{
  "_index": "users",
  "_type": "user",
  "_id": "H3tVi3ABpFL-9AlTbAgj",
  "_version": 4,
  "result": "deleted",
  "_shards": {
      "total": 2,
      "successful": 1,
      "failed": 0
  },
  "_seq_no": 5,
  "_primary_term": 1
}
```
* Создание нескольких документов (типа `user`).  
В конце BODY запроса обязателен переход на новую строку.
```http
POST http://localhost:9200/users/user/_bulk
Content-Type: application/json

{"index":{}}
{"name":"Harry Smith","job":"Dev Ops"}
{"index":{}}
{"name":"Sam Brave","job":"QA"}
   
```
```js
/* JSON response */
{
  "took": 26,
  "errors": false,
  "items": [
    {
      "index": {
          "_index": "users",
          "_type": "user",
          "_id": "Jntsi3ABpFL-9AlTdggH",
          "_version": 1,
          "result": "created",
          "_shards": {
              "total": 2,
              "successful": 1,
              "failed": 0
          },
          "_seq_no": 7,
          "_primary_term": 1,
          "status": 201
      }
    },
    {
      "index": {
          "_index": "users",
          "_type": "user",
          "_id": "J3tsi3ABpFL-9AlTdggH",
          "_version": 1,
          "result": "created",
          "_shards": {
              "total": 2,
              "successful": 1,
              "failed": 0
          },
          "_seq_no": 8,
          "_primary_term": 1,
          "status": 201
      }
    }
  ]
}
```

## Получение всех документов и поиск
* Получение всех документов.
```http
GET http://localhost:9200/users/_search
```
```js
{
  "took": 80,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 2,
      "relation": "eq"
    },
    "max_score": 1.0,
    "hits": [
        {
          "_index": "users",
          "_type": "user",
          "_id": "Jntsi3ABpFL-9AlTdggH",
          "_score": 1.0,
          "_source": {
            "name": "Harry Smith",
            "job": "Dev Ops"
          }
        },
        {
          "_index": "users",
          "_type": "user",
          "_id": "J3tsi3ABpFL-9AlTdggH",
          "_score": 1.0,
          "_source": {
              "name": "Sam Brave",
              "job": "QA"
          }
        }
    ]
  }
}
```
* Поиск по конкретному слову (слову `ops`).
```js
{
  "took": 4,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 1,
      "relation": "eq"
    },
    "max_score": 0.60996956,
    "hits": [
      {
        "_index": "users",
        "_type": "user",
        "_id": "Jntsi3ABpFL-9AlTdggH",
        "_score": 0.60996956,
        "_source": {
          "name": "Harry Smith",
          "job": "Dev Ops"
        }
      }
    ]
  }
}
```
