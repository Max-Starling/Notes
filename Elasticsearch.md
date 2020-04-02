- [Elasticsearch](#elasticsearch)
  - [Настройка](#настройка)
  - [Индекс, тип, документ](#индекс-тип-документ)
  - [Индексирование](#индексирование)
  - [Создание, изменение, удаление](#создание-изменение-удаление)
  - [Поиск](#поиск)
  - [Query DSL](#query-dsl)
  - [Перевёрнутые индексы](#перевёрнутые-индексы)
- [Настройки индекса (settings)](#настройки-индекса-settings)
- [Отображения индекса (mappings)](#отображения-индекса-mappings)
  - [Простые типы данных полей](#простые-типы-данных-полей)
  - [Составные типы данных](#составные-типы-данных)
  - [Специализированные типы данных](#специализированные-типы-данных)
  - [Мультиполя](#мультиполя)
- [Анализаторы](#анализаторы)
  - [Встроенные анализаторы](#встроенные-анализаторы)
  

# Elasticsearch

**Elasticsearch** (эластичный поиск) — распределённый (distributed), RESTful поисковой движок по всему тексту (full-text search).

*Elasticsearch* использует JSON-документы без схемы. Эти документы передаются при помощи REST API для сохранения их в хранилище и поиска.

## Настройка
* [Скачать архив](https://www.elastic.co/guide/en/elasticsearch/reference/current/install-elasticsearch.html#install-elasticsearch).
* Разархивировать и запустить `bin\elasticsearch` или `bin\elasticsearch.bat` (в зависимости от операционной системы).
* Поскольку общение с Elasticserach идёт при помощи HTTP-запросов, можно использовать Postman на порте `http://localhost:9200`.

## Индекс, тип, документ
**Индекс** (Index) — эквивалент *базы данных* в SQL или NoSQL.

**Тип** (Type) — эквивалент *таблицы* в SQL или *коллекции* в NoSQL.

**Документ** (Document) — эквивалент *строки* в SQL или *документа* в NoSQL.

## Индексирование

**Индексирование** в поисковых системах (веб-индексирование) — процесс добавления сведений (о сайте) роботом поисковой машины в базу данных, впоследствии использующуюся для (полнотекстового) поиска информации на проиндексированных сайтах.

В рамках Elasticsearch под индексированием подразумевается запись данных о документах в индекс.

## Создание, изменение, удаление
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

## Поиск
* Получение всех документов.
```http
GET http://localhost:9200/users/_search
```
```js
/* JSON response */
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
* Поиск по конкретному слову (слову `ops`) во всех полях (и `name`, и `job`).
```http
GET http://localhost:9200/users/_search?q=ops
```
```js
/* JSON response */
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
* Поиск по конкретному слову (слову `ops`) в конкретном поле (`name`).
```http
GET http://localhost:9200/users/_search?q=name:sam
```
```js
/* JSON response */
{
  "took": 3,
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
    "max_score": 0.6931472,
    "hits": [
      {
        "_index": "users",
        "_type": "user",
        "_id": "J3tsi3ABpFL-9AlTdggH",
        "_score": 0.6931472,
        "_source": {
          "name": "Sam Brave",
          "job": "QA"
        }
      }
    ]
  }
}
```

## Query DSL

Elasticsearch предоставляет **Query DSL** (Domain Specific Language) — предметно-ориентированный язык, позволяющий описывать запрос `query` в формате JSON и отправлять его в теле запроса (request body).

Query DSL позволяет запрос следующего вида
```http
GET http://localhost:9200/users/_search?q=name:sam
```
отправлять вот так
```http
GET http://localhost:9200/users/_search

{
  "query": {
    "match" : {
      "name" : "sam"
    }
  }
}
```

## Перевёрнутые индексы

Elasticsearch хранит данные как **перевёрнутые индексы** (inverted indexes), что позволяет очень быстро искать данные.

Эта технология может быть реализована разделением всех полей всех документов на токены, которыми являются слова. Из всех слов документов составляется множество уникальных слов. 

Elasticsearch проивзодит поиск по словам. Каждый документ либо содержит слово из этого множества, либо не содержит.

### Пример

Рассмотрим две фразы.  
1) `I don't like to work alone`.  
2) `I often work on weekends`.

Множество уникальных слов будет следующим: `I`, `don't`, `like`, `to`, `work`, `alone`, `often`, `on`, `weekends`.

| Слово     | Документ #1 | Документ #2 |
| --------- | ----------- | ----------- |
| I         |     +       |      +      |
| don't     |     +       |             |
| like      |     +       |             |
| to        |     +       |             |
| work      |     +       |      +      |
| alone     |     +       |             |
| often     |             |      +      |
| on        |             |      +      |
| weekends  |             |      +      |

Например, поиск по слову `work` выдаст оба документа, по слову `like` — только первый, по слову `often` — только второй.

Если ввести в `query` текст `often alone`, поиск выдаст оба документа.

| Слово     | Документ #1 | Документ #2 |
| --------- | ----------- | ----------- |
| alone     |     +       |             |
| often     |             |      +      |

# Настройки индекса (settings)

У каждого индекса может быть какой-то набор настроек.

Текущие настройки индекса можно получить по GET-запросу `_settings`.
```http
GET http://localhost:9200/users/_settings
```

```js
/* JSON response */
{
  "users": {
    "settings": {
      "index": {
        "number_of_shards": "1",
        "blocks": {
          "read_only_allow_delete": "true"
        },
        "provided_name": "users",
        "creation_date": "1582885295047",
        "number_of_replicas": "1",
        "uuid": "cNAP5avkRueTAkUGNxsHew",
        "version": {
          "created": "7030299"
        }
      }
    }
  }
}
```

*Индексы* обычно *разредяются* на *несколько подиндексов* (sub-indices), называемые **осколками** (shards). *Осколки распределяются* между *несколькими экземплярами приложения*. 

*Количество осколков* указано в *настройках индекса* в *свойстве* `number_of_shards`.

*Резервная копия всех осколков* называется **репликой** (replica). Если *один экземпляр приложения падает* вместе с данными, которые на нём хранились, *реплика* позволяет *не терять* эти *данные*.

Количество *реплик* указано в *настройках индекса* в *свойстве* `number_of_replicas`.


# Отображения индекса (mappings)

**Отображения** (mappings) индекса *определяют схему документа*, в том числе *тип данных* для *каждого поля документа*.

Текущие отображения индекса можно получить по GET-запросу `_mappings`.
```http
GET http://localhost:9200/users/_mappings
```
```js
/* JSON response */
{
  "users": {
    "mappings": {
      "properties": {
        "job": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "name": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        }
      }
    }
  }
}
```
*Запись данных* в *индекс* называют **индексированием** (indexing).

## Простые типы данных полей
* **Строковые** (string): `text`, `keyword`.
* **Числовые** (Numberic): `long`, `integer`, `short`, `byte`, `double`, `float`, `half_float`, `scaled_float`.
* **Логический** (Boolean): `boolean`.
* **Дата** (Date): `date`.
* **Бинарный** (Binary): `binary`.
* **Диапазон** (Range): `integer_range`, `float_range`, `long_range`, `double_range`, `date_range`.

### Тип данных text

Строковый тип `text` используется для индексирования полнотекстовых значений (full-text values). Примерами полнотекстовых значений являются: название, сообщение, описание.

*Полнотекстовые значения* **анализируются**, то есть обрабатываются перед *индексированием*. 

Каждое *полнотекстовое поле проходит перед индексированием* через **анализатор** (analyzer), который *конвертирует строку* в *список отдельных термов* (list of individual terms), затем этот *список индексируется*, а *поле* называют **проанализированным** (analyzed). 

**Анализирование** (analysis) позволяет *Elasticsearch* *искать отдельные слова* в *каждом полнотекстовом поле*.

*Поля текстового типа* *не используются* для *сортировки*, обычно *не используются* для *агрегаций*.

Задание типа `text`.
```http
PUT index_name

{
  "mappings": {
    "properties": {
      "description": {
        "type":  "text"
      }
    }
  }
}
```

### Тип данных keyword

*Строковый тип* `keyword` (ключевое слово) используется для *индексирования* таких *значений*, как: `ID`, `email`, `hostname`, `status code`, `tag` и прочих. Эти *значения используются* для *фильтрации*, *сортировки* и *агрегации*. 

Поля типа `keyword` *не анализируются*. Они *ищутся только по точному значению, совпадению* (exact value). Например, нельзя найти `tom@gmail.com` по слову `tom` или `gmail`.

Задание типа `keyword`.
```http
PUT index_name

{
  "mappings": {
    "properties": {
      "email": {
        "type":  "keyword"
      }
    }
  }
}
```

## Составные типы данных
* **Объект** (Object): `object`. Для JSON-объекта.
* **Вложенный** (Nested): `nested`. Для массива JSON-объектов.

### Массив

**Массив** (Array) в Elasticsearch не существует как отдельная сущность, поскольку любое поле может иметь одно или несколько значений по умолчанию. Тем не менее, все эти значения должны быть одного типа.

* Массив строк: `["1", "two"]`.
* Массив чисел: `[1, 7]`.
* Массив объектов `[{ "name": "John", "experience": 5 }, { "name": "Sam", "experience": 3 }]`.

В массиве объектов нельзя выделить конкретный объект. При такой необходимости нужно использовать тип `nested`.

Массивы смешанных типов не поддерживаются: `[1, "two"]`.

Пустой массив интерпретируется как отсутствующее значение (поле без значений).

Вставка объекта с массивом `tags`.
```http
PUT index_name/type_name/1

{
  "message": "The problem with useEffect",
  "tags":  [ "js", "react", "react-hooks" ]
}
```

### Сравнение массива объектов и вложенного типа

**Вложенный тип данных** (Nested datatype) — специальный подтип объекта (`object`), который позволяет массиву JSON-объектов индексироваться таким образом, чтобы объекты можно было получать отдельно друг от друга.

## Специализированные типы данных
* **IP**: `ip`. Для IPv4 и IPv6 адресов.
* **Completion** (Completion datatype): `completion`. Для автозаполнения предложений.
* **Join**: `join`. Для создания отношений между документами одного индекса.
* **Search-as-you-type**: `search_as_you_type`. Для поиска по мере ввода.

## Мультиполя

Elasticserach предоставляет возможность *хранить одно и то же полt несколькими способами* для *разных целей*. Такое *поле* называется **мультиполем** (multi-field).

Например, *текстовое поле* может быть *одновременно* представлено *типом* `text` для *полтотекстового поиска* и типом `keyword` *для сортировки* и *агрегации*.

Настройка мультиполя.
```http
PUT index_name

{
  "mappings": {
    "properties": {
      "position": {
        "type": "text",
        "fields": {
          "keyword": { 
            "type":  "keyword"
          }
        }
      }
    }
  }
}
```
Чтобы использовать поле `position` как ключевое слово, необходимо писать `position.keyword`, при использовании `position` будет срабатывать полнотекстовый поиск.

Поле `keyword` можно назвать как угодно (например, `raw`).
```http
"fields": {
    "raw": { 
      "type":  "keyword"
    }
  }
}
```
Тогда использование поля `position` в качестве ключевого слова: `position.raw`.

# Анализаторы

**Анализаторы** (Analyzers) определяют способ, которым данные будут анализироваться перед индексацией.

**Анализ текста** (Text analysis) — процесс преобразования обычного текста в структурированный формат, оптимизированный для поиска. Используется, когда установлен тип данных `text`.

После анализа текст поля разделяется на **термы** (terms). Таким образом, после анализа поле представлено в виде **списка термов** (list of terms), в котором оно и индексируется.

## Встроенные анализаторы

Elasticsearch предоставляет набор **встроенных анализаторов** (build-in analyzers).

Для их будем анализировать фразу `"- How old are you? - I'm 17."`.

Чтобы проверить, как работает конкретный анализатор, можно отправить следующий запрос.
```http
GET http://localhost:9200/_analyze

{ 
  "analyzer": "analyzer_name",
  "text":"- How old are you? - I'm 17."
}
```

* **Стандартный**: `standard`. Используется по умолчанию. Разбивает текст на слова, переводит их в нижний регистр (`lowercase`), удаляет знаке препинания, при необходимости удаляет стоп-слова.
```js
/* terms */
["how", "old", "are", "you", "i'm", "17"]
```
* **Простой**: `simple`. Разделяет слова каждый раз, когда встречает не букву. Все термы переводятся в нижний регистр.
```js
/* terms */
["how", "old", "are", "you", "i", "m"]
```
* **Стоп-анализатор**: `stop`. Как `simple`, но с возможностью удалять стоп-слова. По умолчанию используются стоп-слова английского языка (вспомогательные глаголы, предлоги и так далее).
```js
/* terms */
["how", "old", "you", "i", "m"]
```
* **Пробельный**: `whitespace`. Разделяет текст, когда находит пробельные символы.
```js
/* terms */
["-", "How", "old", "are", "you?", "-", "I'm", "17."]
```
* **Анализатор ключевых слов**: `keyword`. Принимает текст и его возвращает как есть.
```js
/* terms */
["- How old are you? - I'm 17."]
```
* **Языковой**: `english`, `french`. Анализирует текст соответственно специфике языка. Удаляет стоп-слова, характерные языку. Переводит в нижний регистр.
```js
/* terms */
["how", "old", "you", "i'm", "17"]
```
* **Шаблонный**: `pattern`. Для разделения текста на термы использует регулярные выражения. По умолчанию используется регулярное выражение `\W+` (всё, что не может быть словом). Переводит в нижний регистр.
```js
/* terms */
["how", "old", "are", "you", "i", "m", "17"]
```

### 
