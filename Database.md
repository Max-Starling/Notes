**DBMS** (Database Management System) — программное обеспечение, которое определяет (define) и управляет (manage) данными в базе данных. Отвечает за всё, что связано с данными: формат, названия, структура и прочее. Определяет правила размещения данных в бд и управления над ними. 

Пример DBMS: MySQL, Oracle.

**DAO** (Data Access Object) — объект, представляющий абстрактный интерфейс к какой-то базе данных или другому механизму хранения (persistence mechanism). Он сопоставляет вызовы приложения (app calls) с уровнем хранения (persistance layer), предоставляя некоторые методы работы с данными, не раскрывая детали базы данных. Пример инструмента с DAO: ORM.

**DTO** (Data Transfer Object) — объект, передающий данные между подсистемами приложения. В отличие от DAO, DTO не может содержать поведение (кроме хранения, поиска, сериализации и десериализации своих собственных данных). DTO используют, чтобы объединить в них данные нескольких запросов в один запрос (поскольку подсистемы могут общаться запросами, а каждый запрос - дорогостоящая операция).

**CRUD** (Create, Read, Update, Delete) — базовый набор операций постоянного хранилища (persistent storage): создание, чтение, обновление, удаление.

## ORM и ODM

**ORM** (Object Relational Mapper) связывает реляционную базу данных с объектной моделью, позволяя переводить данные из объектов в коде в реляционное представление и наоборот. (sequelize) 

**ODM** (Object Document Mapper) связывает документную базу данных с объектной моделью. (mongoose)

## Типы баз данных
* **Реляционная** (Relational). Данные организованы как логически независимые таблицы. 
* **Плоская** (Flat).
* **Объектно-ориентированная** (Object-Oriented). Использует концепции ООП.
* **Иерархическая** (Hierarchical). Данные организованны в виде иерархии.

## Транзации

**Транзакция** — логическая единица  (logical unit), осуществляющая доступ к базе данных и, возможно, изменяющая её содержимое. Транзакции работают с данными, используя операции чтения и записи.

### Свойства ACID
* **Атомарность** (Atomicity) гарантирует, что транзакция не может быть зафиксирована частично: либо выполняются все подоперации транзакции, либо ни одна. Если какая-то операция из последовательности не проходит, то происходит откат (rollback) всей последовательности.
* **Согласованность, консистентность** (Consistency). Каждая успешная транзакция фиксирует только допустимый для системы результат. База данных должна быть согласованна до и после транзакции, во время проведения транзакции согласованность не требуется.
* **Изолированность** (Isolation). Во время выполнения транзакции другие параллельные транзакции не должны оказывать влияния на её результат. Требование дорогое, поэтому в реальных базах транзакции изолируются не полностью: создаются уровни изолированности.
* **Долговечность** (Durability). Если пользователь получил подтверждение от системы, что транзакция выполнена, он может быть уверен, что сделанные им изменения не будут отменены из-за какого-либо сбоя. Изменения, сделанные успешно завершённой транзакцией, должны остаться сохранёнными после возвращения системы в работу.

Пусть деньги переводятся с одного счёта на другой. Используются две основные операции: вывод денег с одного счёта `1000$ - 100$ = 400$`, зачисление их на другой счёт `500$ + 100$ = 600$`. Если первая операция удалась, а вторая нет, то происходит откат, поскольку в противном случае имеем несогласованность (inconsistency) данных до и после транзакции: до транзакции на счетах в сумме было `1500$`, после — `1400$`.

# Основы SQL

**Structured Query Language** (SQL) — язык структурированных запросов; предназначен для управления данными в системе реляционных баз данных (RDBMS).

## Работа с базами данных
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

## Создание таблицы
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
* `CHEK (<condition>)` — проверка удовлетворения заданному условию `condition`.
* `DEFAULT <value>` — задаёт значение по умолчанию.

```sql
CREATE TABLE timers (
  ID CHAR(5),
  seconds INT CHECK (seconds < 60) DEFAULT 0,
  PRIMARY KEY(ID)
);
```
## Вставка в таблицу
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
SELECT <column_name>, <another_column_name>, /* ... */
  FROM <table_name>
  WHERE <condition>; 
```
В условии `condition` можно *производить сравнение текста* и *чисел*, а также *использовать логические операторы* `AND`, `OR`, `NOT`.
```SQL
SELECT * FROM notes
  WHERE title = "Article #4" OR title = "Article #5";
```
![SQL Example](./assets/SQL_5.png)
```SQL
SELECT * FROM notes
  WHERE title != "Article #1" AND description NOT NULL;
```
![SQL Example](./assets/SQL_6.png)
```SQL
SELECT * FROM timers
  WHERE seconds > 30;
```
![SQL Example](./assets/SQL_7.png)

### BETWEEN, IN, LIKE
С помощью *оператора* `BETWEEN` можно задать *промежуток значений* (*число*, *строка*, *дата*).
```SQL
SELECT * FROM notes
  WHERE ID BETWEEN 2 AND 5;
```
С помощью *оператора* `IN` можно задать *несколько возможных значений*.
```SQL
SELECT * FROM notes
  WHERE ID IN (1, 2, 4);
```
С помощью *оператора* `LIKE` можно задать *шаблон для поиска* (`%` — любое количество символов, `_` — один символ).
```SQL
SELECT * FROM notes
  WHERE title LIKE "Article___" AND description LIKE "DESCRIPTION%";
```
## Сортировка при помощи ORDER BY

**Ключевое слово ORDER BY** используется для *сортировки результатов запроса* `SELECT`.

Можно указать *порядок сортировки*: `ASC` (ascending) — *по возрастанию*, используется *по умолчанию*; `DESC` (descending) — *по убыванию*.
```SQL
SELECT * FROM notes
  ORDER BY description;
```
![SQL Example](./assets/SQL_orderby.png)
```SQL
SELECT * FROM notes
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

*Несколько строк группируются* в *одну*, что *позволяет* использовать *агрегатные функции* для *каждой* получившейся *группы*.
```SQL
SELECT *, COUNT(ID) AS rows_count FROM notes
  GROUP BY description;
```
![SQL Example](./assets/SQL_groupby.png)

