- [Базы данных](#базы-данных)
  - [Типы баз данных](#типы-баз-данных)
  - [ORM и ODM](#orm-и-odm)
  - [Транзакции и их свойства](#транзакции-и-их-свойства)
  - [Масштабирование баз данных](#масштабирование-баз-данных)
  - [Индекс базы данных](#индекс-базы-данных)
- [Основы SQL](#основы-sql)
  - [Типы команд в SQL](#типы-команд-в-sql)
  - [Отношения](#отношения)
- [Инструкции SQL](#инструкции-sql)
  - [Инструкции для работы с базой данных](#инструкции-для-работы-с-базой-данных)
  - [Создание таблицы с помощью CREATE](#создание-таблицы-с-помощью-create)
  - [Вставка в таблицу с помощью INSERT](#вставка-в-таблицу-с-помощью-insert)
  - [Запросы SELECT](#запросы-select)
  - [Запросы SELECT с условиями WHERE](#запросы-select-с-условиями-where)
  - [Сортировка при помощи ORDER BY](#сортировка-при-помощи-order-by)
  - [Агрегатные функции](#агрегатные-функции)
  - [Группировка значений при помощи GROUP BY](#группировка-значений-при-помощи-group-by)
  - [Связь таблиц при помощи JOIN](#связь-таблиц-при-помощи-join)
  - [Виртуальная таблица VIEW](#виртуальная-таблица-view)
  - [Изменение данных с помощью UPDATE](#изменение-данных-с-помощью-update)
  - [Удаление данных с помощью DROP](#удаление-данных-с-помощью-drop)
  - [Удаление данных с помощью TRUNKATE и DELETE](#удаление-данных-с-помощью-trunkate-и-delete)
  - [Вложенные запросы SELECT](#вложенные-запросы-select) 

**DAO** (Data Access Object) — объект, представляющий абстрактный интерфейс к какой-то базе данных или другому механизму хранения (persistence mechanism). Он сопоставляет вызовы приложения (app calls) с уровнем хранения (persistance layer), предоставляя некоторые методы работы с данными, не раскрывая детали базы данных. Пример инструмента с DAO: ORM.

**DTO** (Data Transfer Object) — объект, передающий данные между подсистемами приложения. В отличие от DAO, DTO не может содержать поведение (кроме хранения, поиска, сериализации и десериализации своих собственных данных). DTO используют, чтобы объединить в них данные нескольких запросов в один запрос (поскольку подсистемы могут общаться запросами, а каждый запрос - дорогостоящая операция).

**CRUD** (Create, Read, Update, Delete) — базовый набор операций постоянного хранилища (persistent storage): создание, чтение, обновление, удаление.

# Базы данных

**Система управления базами данных** (СУБД, DBMS, Database Management System) — программное обеспечение, которое определяет и управляет данными в базе данных. 

СУБД отвечает за всё, что связано с данными: формат, названия, структура, правила размещения данных в бд и управления над ними.

Пример DBMS: MySQL, Oracle.

## Типы баз данных
* **Реляционная** (Relational). Данные организованы как логически независимые таблицы. 
* **Плоская** (Flat).
* **Объектно-ориентированная** (Object-Oriented). Использует концепции ООП.
* **Иерархическая** (Hierarchical). Данные организованны в виде иерархии.
* **Документная** (Document). Тип нереляционных баз данных, предназначенный для хранения и запроса данных в виде документов в формате, подобном JSON. 

## ORM и ODM

**ORM** (Object Relational Mapper) связывает реляционную базу данных с объектной моделью, позволяя переводить данные из объектов в коде в реляционное представление и наоборот. (sequelize) 

**ODM** (Object Document Mapper) связывает документную базу данных с объектной моделью. (mongoose)

## Транзакции и их свойства

**Транзакция** (Transaction) — логическая единица  (logical unit), которая осуществляет доступ к базе данных и, возможно, изменяет данные в ней. 

Транзакции работают с данными, используя операции чтения и записи.

### Свойства ACID
* **Атомарность** (Atomicity) гарантирует, что транзакция не может быть зафиксирована частично: либо выполняются все подоперации транзакции, либо ни одна. Если какая-то операция из последовательности не проходит, то происходит откат (rollback) всей последовательности.
* **Согласованность, консистентность** (Consistency). Каждая успешная транзакция фиксирует только допустимый для системы результат. База данных должна быть согласованна до и после транзакции, во время проведения транзакции согласованность не требуется.
* **Изолированность** (Isolation). Во время выполнения транзакции другие параллельные транзакции не должны оказывать влияния на её результат. Требование дорогое, поэтому в реальных базах транзакции изолируются не полностью: создаются уровни изолированности.
* **Долговечность** (Durability). Если пользователь получил подтверждение от системы, что транзакция выполнена, он может быть уверен, что сделанные им изменения не будут отменены из-за какого-либо сбоя. Изменения, сделанные успешно завершённой транзакцией, должны остаться сохранёнными после возвращения системы в работу.

### Пример транзакции

Пусть деньги переводятся с одного счёта на другой. Используются две основные операции: вывод денег с одного счёта `1000$ - 100$ = 400$`, зачисление их на другой счёт `500$ + 100$ = 600$`. Если первая операция удалась, а вторая нет, то происходит откат, поскольку в противном случае имеем несогласованность (inconsistency) данных до и после транзакции: до транзакции на счетах в сумме было `1500$`, после — `1400$`.

## Индекс базы данных

**Индекс базы данных** (Database Index) — структура данных, которая ускоряет операции поиска в конкретной таблице (коллекции) базы данных за счёт хранения дополнительной информации в базе.

Записи в таблице (документы в коллекции) могут храниться произвольно и поиск по заданному критерию последовательным просмотром большой таблицы (коллекции) может занимать много времени.

Индекс формируется из значений одного или нескольких столбцов таблицы (полей документов коллекции) и указателя на соответствующие строки таблицы (документы коллекции). Также иногда индексы могут создаваться из выражений.

Скорость достигается за счёт структуры самого индекса, которая может быть представлена, например, сбалансированным деревом поиска.

### Замечание об использовании индексов

Важно быть аккуратными с индексами, потому что за любым изменением в таблице (коллекции) должно последовать обновление индекса. Когда данных очень много, это может повлечь за собой очень трудоёмкие вычисления и большую нагрузку на сервер соответственно.

Более того, индексы занимают дополнительное место, поэтому не стоит их создавать (хранить) без необходимости.

## Масштабирование баз данных

# Основы SQL

**Язык структурированных запросов** (Structured Query Language, SQL) — язык, предназначенный для управления данными в системе реляционных баз данных (RDBMS).

## Типы команд в SQL

### Язык определения данных (DDL)

**Язык определения данных** (Data Definition Language, DDL) состоит из команд, которые используются для определения схемы базы данных. 

DDL-команды помогают задать или изменить структуру объектов базы данных.

**Объектами базы данных** (Database objects) являются таблицы, индексы, функции, представления, процедуры и триггеры.

Примеры DDL-команд
* `CREATE` — создание базы данных или её объектов.
* `ALTER` — изменение структуры базы данных.
* `DROP` — удаление объектов из базы данных.
* `TRUNCATE` — удаление всех записей из таблицы, в том числе и записей о самом удалении.

### Язык запросов данных (DQL)

**Язык запросов данных** (Data Query Language, DQL) предоставляет выражения, которые позволяют получить часть данных из базы данных по заданному запросу (query).

Пример DQL-команд
* `SELECT` — получение данных из базы данных.

### Язык манипулирования данными (DML)

**Язык манипулирования данными** (Data Manipulation Language, DML) состоит из команд, которые используются для манипулирования данными, которые хранятся в базе.

Пример DML-команд
* `INSERT` — вставка данных в таблицу.
* `UPDATE` — обновление данных таблицы.
* `DELETE` — удаление записей из таблицы.

### Язык контроля над данными (DCL)

**Язык управления данными** (Data Control Language, DCL) предоставляет команды, позволяющие управлять правами доступа, разрешения

Пример DCL-команд
* `GRANT` — предоставление пользователю доступа к базе данных.
* `REVOKE` — изымание доступа пользователя к базе данных.

### Язык управления транзакциями (TCL)

**Язык управления транзакциями** (Transaction Control Language, TCL) предоставляет команды, позволяющие управлять транзациями в базе данных.

Транзакции выполняются либо полностью, либо никак. Промежуточных результатов быть не может: они отменяются при неудаче.

Примеры TCL-команд
* `COMMIT` — сохранение результатов успешно выполненной транзакции в базу данных.
* `ROLLBACK` — откат транзакции в случае ошибок.
* `SAVEPOINT` — создание контрольной точки в пределах транзакции, чтобы при неудаче откатывать не всю транзакцию целиком.  

## Отношения

### Один-к-одному

### Один-ко-многим

### Многие-ко-многим

# Инструкции SQL

## Инструкции для работы с базой данных
```SQL
-- создание бд
CREATE DATABASE test;
-- просмотр имеющихся бд
SHOW DATABASES;
-- выбор конкретной бд для использования
USE test;
-- удаление бд
DROP DATABASE test; 
```

## Создание таблицы с помощью CREATE
```SQL
-- схема
CREATE TABLE <table_name> (
  <column_name> <column_type>,
  <another_column_name> <another_column_type>,
  <one_more_column_name> <one_more_column_type>,
  /* ... */,
  PRIMARY KEY (<column_name>),
  FOREIGN KEY (<another_column_name>) REFERENCES <another_table_name>(<another_column_name>)
); 
```
```SQL
CREATE TABLE notes (
  ID INT,
  title VARCHAR(255) NOT NULL,
  description VARCHAR(255),
  PRIMARY KEY (ID)
); 
```
### Ограничения
Столбцам таблицы можно устанавливать **ограничения** (constraints).
* `NOT NULL` — столбец не может иметь значение `NULL`.
* `UNIQUE` — каждое значение в столбце должно быть уникальным.
* `PRIMARY KEY (<key>)` — *первичный ключ*; уникальный идентификатор строки (row) таблицы. Комбинирует в себе ограничения `NOT NULL` и `UNIQUE`. 
* `FOREIGN KEY (<key>) REFERENCES <table>(<key>)` — *внешний ключ*; уникальный идентификатор строки в другой таблице.
* `CHEСK (<condition>)` — проверка удовлетворения заданному условию `condition`.
* `DEFAULT <value>` — задаёт значение по умолчанию.

```sql
CREATE TABLE timers (
  ID CHAR(5),
  seconds INT CHECK (seconds < 60) DEFAULT 0,
  PRIMARY KEY(ID)
);
```
## Вставка в таблицу с помощью INSERT
```SQL
-- схема
INSERT INTO <table_name> (<column_name>, <another_column_name>, /* ... */)
  VALUES (<value>, <another_value>, /* ... */);
```
Если в `VALUES` перечислены значения для каждого столбца и в правильном порядке, то столбцы можно не указывать.
```SQL
INSERT INTO notes (ID, title, description)
  VALUES (1, "Article #1", "Description 1");

INSERT INTO notes
  VALUES (2, "Article #2", "Description 2");

-- error (2 values for 3 columns)
INSERT INTO notes (ID, title, description)
  VALUES (3, "Article #3"); 

INSERT INTO notes (ID, title, description)
  VALUES (4, "Article #4", NULL);

INSERT INTO notes (ID, title)
  VALUES (5, "Article #5"); 

-- error (NOT NULL constraint failed)
INSERT INTO notes (ID, title, description)
  VALUES (6, NULL, "Description 6");
```
```SQL
INSERT INTO timers
  VALUES ("timer1", 59);

-- error (CHECK constraint failed)
INSERT INTO timers
  VALUES ("timer2", 61);

-- error (1 value for 2 columns)
INSERT INTO timers
  VALUES ("timer3");

INSERT INTO timers (ID)
  VALUES ("timer4");
```

## Запросы SELECT

*Получение данных* из *таблицы* происходит при помощи *запросов* `SELECT`.
```SQL
-- схема
SELECT <column_name>, <another_column_name>, /* ... */
  FROM <table_name>;
```
Можно *выбрать определённые столбцы таблицы*, а можно *выбрать* сразу *все* при помощи `*`.
```SQL
SELECT * FROM notes;

```
![SQL Example](./assets/SQL_1.png)
```SQL
SELECT * FROM timers;
```
![SQL Example](./assets/SQL_2.png)

### SELECT DISTINCT

Чтобы запрос *не выдавал повторяющиеся строки*, существует `SELECT DISTINCT`.

Например, в таблице `notes` две строки содержат `null` в `description`.
```SQL
SELECT DISTINCT description FROM notes;
```

![SQL Example](./assets/SQL_3.png)

*Строка пропускается* только, если *все её значения совпадают* со *значеними другой строки*.
```SQL
SELECT DISTINCT title, description FROM notes;
```

![SQL Example](./assets/SQL_4.png)
 
## Запросы SELECT с условиями WHERE

**Ключевое слово WHERE** используется для *указания условий* в *запросе* `SELECT`.

```SQL
-- схема
SELECT <column_names>
  FROM <table_name>
  WHERE <condition>; 
```
В условии `condition` можно *производить сравнение текста* и *чисел*, а также *использовать логические операторы* `AND`, `OR`, `NOT`.
```SQL
SELECT *
  FROM notes
  WHERE title = "Article #4" OR title = "Article #5";
```
![SQL Example](./assets/SQL_5.png)
```SQL
SELECT *
  FROM notes
  WHERE title != "Article #1" AND description NOT NULL;
```
![SQL Example](./assets/SQL_6.png)
```SQL
SELECT *
  FROM timers
  WHERE seconds > 30;
```
![SQL Example](./assets/SQL_7.png)

### BETWEEN, IN, LIKE
С помощью *оператора* `BETWEEN` можно задать *промежуток значений* (*число*, *строка*, *дата*).
```SQL
SELECT *
  FROM notes
  WHERE ID BETWEEN 2 AND 5;
```
С помощью *оператора* `IN` можно задать *несколько возможных значений*.
```SQL
SELECT *
  FROM notes
  WHERE ID IN (1, 2, 4);
```
С помощью *оператора* `LIKE` можно задать *шаблон для поиска* (`%` — любое количество символов, `_` — один символ).
```SQL
SELECT *
  FROM notes
  WHERE title LIKE "Article___" AND description LIKE "DESCRIPTION%";
```
## Сортировка при помощи ORDER BY

**Ключевое слово ORDER BY** используется для *сортировки результатов запроса* `SELECT`.

Можно указать *порядок сортировки*: `ASC` (ascending) — *по возрастанию*, используется *по умолчанию*; `DESC` (descending) — *по убыванию*.
```SQL
SELECT *
  FROM notes
  ORDER BY description;
```
![SQL Example](./assets/SQL_orderby.png)
```SQL
SELECT *
  FROM notes
  ORDER BY ID DESC;
```
![SQL Example](./assets/SQL_orderby_2.png)

## Агрегатные функции

**Агрегатные функции** *применяются* к *столбцу* и *возвращают единое значение*.

* `AVG (column_name)` — среднее значение столбца `column_name`.
* `COUNT (column_name)` — количество строк.
* `MAX (column_name)` — наибольшее значение столбца.
* `MIN (column_name)` — наименьшее значение столбца.
* `SUM (column_name)` — сумма значений в столбце.

```SQL
SELECT COUNT(ID), MIN(ID), AVG(ID), MAX(ID), SUM(ID) FROM notes;
```
![SQL Example](./assets/SQL_aggregate.png)

### Оператор AS

**Оператор  AS** используется для *переименования столбца* в *результате* запроса `SELECT`.
```sql
-- схема
SELECT <column_name> AS <name> FROM <table_name>;
```

```sql
SELECT MAX(seconds) AS max_seconds FROM timers;
```
![SQL Example](./assets/SQL_as.png)

## Группировка значений при помощи GROUP BY

**Ключевое слово GROUP BY** используется для *группировки значений* в *столбце*. 
```SQL
-- схема
SELECT <column_names>
  FROM <table_name>
  GROUP BY <column_name>;
```

*Несколько строк группируются* в *одну*, что *позволяет* использовать *агрегатные функции* для *каждой* получившейся *группы*.
```SQL
SELECT *, COUNT(ID) AS rows_count
  FROM notes
  GROUP BY description;
```
![SQL Example](./assets/SQL_groupby.png)

### GROUP BY с условиями HAVING

*Ключевое слово* `WHERE` *не может использоваться* с *агрегатными функциями*, поэтому ввели **ключевое слово HAVING**.
```SQL
-- схема
SELECT <column_names>
  FROM <table_name>
  GROUP BY <column_name>
  HAVING <condition>;
```

```SQL
SELECT *, COUNT(ID) AS rows_count
  FROM notes
  GROUP BY description
  HAVING COUNT(description) = 1;
```

## Связь таблиц при помощи JOIN

**Ключевое слово JOIN** используется для связи нескольких таблиц в одну по их общим атрибутам.
```sql
-- схема
SELECT <column_names>
  FROM <table_A>
  JOIN <table_B>
  ON <table_A.column_name_1> = <table_B.column_name_2>; 
```

С `JOIN` удобно использовать **псевдонимы** — *сокращённые названия таблиц*.
```sql
-- схема
SELECT <column_names>
  FROM <table_A> A
  JOIN <table_B> B
  ON A.<column_name_1> = B.<column_name_2>; 
```

Создадим и заполним ещё одну таблицу для примера.
```sql
CREATE TABLE seconds (
  ID INT,
  value INT CHECK (value < 60) DEFAULT 0,
  PRIMARY KEY(ID)
);

INSERT INTO seconds VALUES(1, 0);
INSERT INTO seconds VALUES(2, 30);
INSERT INTO seconds VALUES(3, 59);

INSERT INTO timers VALUES ("timer5", 15);

SELECT * FROM timers;
SELECT * FROM seconds;
```
![SQL Example](./assets/SQL_join_1.png)
![SQL Example](./assets/SQL_join_2.png)

* `JOIN`, `INNER JOIN` (внутреннее объединение) — строки, содержащиеся и в A, и в B.

```sql
SELECT *
  FROM seconds A
  JOIN timers B
  ON A.value = B.seconds;
```
![SQL Example](./assets/SQL_join_3.png)

* `LEFT JOIN` (левосторонее объединение) — строки, содержащиеся в A, даже при их отсутствии в B.
```sql
SELECT A.ID AS a_id, B.ID AS b_id, A.value, B.seconds
  FROM seconds A
  LEFT JOIN timers B
  ON A.value = B.seconds;
```
![SQL Example](./assets/SQL_join_4.png)

* `LEFT JOIN` без пересечения — строки, содержащиеся в A, но не содержащиеся в B.
```sql
SELECT *
  FROM seconds A
  LEFT JOIN timers B
  ON A.value = B.seconds
  WHERE B.seconds IS NULL;
```
![SQL Example](./assets/SQL_join_5.png)

*Аналогично* определяются `FULL OUTER JOIN` (все строки в A и все в B), `FULL OUTER JOIN` без пересечения, `RIGHT JOIN` (правостороннее объединение), `RIGHT JOIN` без пересечения.

* `FULL OUTER JOIN` без пересечения.
```sql
SELECT *
  FROM seconds A
  FULL OUTER JOIN timers B
  ON A.value = B.seconds
  WHERE B.seconds IS NULL AND A.value IS NULL;
```

Можно *объединить большее количество таблиц*.
```sql
SELECT A.column_1, B.column_2, C.column_3
  FROM table_A A 
  JOIN table_B B ON A.column_1 = B.column_2 
  JOIN table_C C ON B.column_2 = C.column_3;
```

## Виртуальная таблица VIEW

**VIEW** — *виртуальная таблица*. 

Виртуальная таблица похожа на обычную таблицу, но ей не является, поскольку не хранит реальные данные, а лишь ссылается на них. По этой причине данные в виртуальной таблице нельзя изменить.

Создаётся на основании результата выполнения выражения (обычно результат запроса `SELECT`). Может содержать данные из нескольких таблиц одновременно.
```SQL
-- схема
CREATE VIEW <view_name> AS
  SELECT <column_names>
  FROM <table_name>; 
```

```SQL
CREATE VIEW timers_less_30sec AS
  SELECT *
  FROM timers
  WHERE seconds < 30;

SELECT * FROM timers_less_30sec;
```
![SQL Example](./assets/SQL_view_1.png)

Поскольку `VIEW` хранит ссылки на реальные данные, он знает всё об их изменениях и всегда отображает последнюю версию.
```SQL
INSERT INTO timers VALUES ("timer7", 25);

SELECT * FROM timers_less_30sec;
```
![SQL Example](./assets/SQL_view_2.png)


## Изменение данных с помощью UPDATE

Обновление строк таблицы
```SQL
UPDATE <table_name>
  SET <column_name> = <value>, <another_column_name> = <another_value>, /* ... */
  WHERE <condition>; 
```
# Удаление данных с помощью DROP
Удаление таблицы.
```SQL
DROP TABLE <table_name>; 
```
Удаление `VIEW`.
```SQL
DROP VIEW <view_name>; 
```

## Удаление данных с помощью TRUNKATE и DELETE

Удаление строк в таблице по какому-то условию.
```SQL
DELETE FROM <table_name>
  WHERE <condition>;
```

*Удалить все строки* из *таблицы* можно *двумя способами*.
```SQL
DELETE FROM <table_name>;
-- или (Transact-SQL)
TRUNCATE TABLE <table_name>;
```

`TRUNCATE` работает быстрее, но имеет следующие ограничения.
* Отсутствие условий `WHERE`.
* Не отрабатывают триггеры (в том числе на удаление).
* `TRUNCATE` не работает, если на удаляемую таблицу имеется ссылка по внешнему ключу.
* Не журнализируется удаление отдельных строк таблицы.

## Вложенные запросы SELECT

*Запросы* `SELECT` могут быть **вложенными** (nested). *Вложенный запрос* называется **подзапросом** (subquery).
```SQL
SELECT <column_names>
  FROM  <table_name>
  WHERE <value> IN (SELECT <column-name>
                      FROM <another_table_name>
                      WHERE <condition>)
```
```SQL
SELECT <column_name> = (SELECT <column_name> FROM <table_name> WHERE <condition>)
  FROM <table_name>
  WHERE <condition>
```
