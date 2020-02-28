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

## Получение всех документов и поиск
* Получение всех документов.
```http
GET http://localhost:9200/users/_search
```
*
