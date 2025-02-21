- [Основы SQL](#основы-sql)
  - [Типы команд в SQL: `DDL`, `DQL`, `DML`, `DCL`, `TCL`](#типы-команд-в-sql-ddl-dql-dml-dcl-tcl)
- [Инструкции SQL](#инструкции-sql)
  - [Инструкции для работы с базой данных](#инструкции-для-работы-с-базой-данных)
  - [Создание таблицы с помощью `CREATE`](#создание-таблицы-с-помощью-create)
  - [Вставка в таблицу с помощью `INSERT`](#вставка-в-таблицу-с-помощью-insert)
  - [Запросы `SELECT`](#запросы-select)
  - [Запросы `SELECT` с условиями `WHERE`](#запросы-select-с-условиями-where)
  - [Приоритет операторов (`Operator Precedence`)](#приоритет-операторов-operator-precedence)
  - [Сортировка при помощи `ORDER BY`](#сортировка-при-помощи-order-by)
  - [Агрегатные функции `AVG`, `COUNT`, `MAX`, `MIN`, `SUM`](#агрегатные-функции-avg-count-max-min-sum)
  - [Группировка значений при помощи `GROUP BY`](#группировка-значений-при-помощи-group-by)
  - [Связь таблиц при помощи `JOIN`](#связь-таблиц-при-помощи-join)
  - [Виртуальная таблица `VIEW`](#виртуальная-таблица-view)
  - [Изменение данных с помощью `UPDATE`](#изменение-данных-с-помощью-update)
  - [Удаление данных с помощью `DROP`](#удаление-данных-с-помощью-drop)
  - [Удаление данных с помощью `TRUNKATE` и `DELETE`](#удаление-данных-с-помощью-trunkate-и-delete)
  - [Вложенные запросы `SELECT`](#вложенные-запросы-select)
  - [Объединение результатов запросов при помощи `UNION` и `UNION ALL`](объединение-результатов-запросов-при-помощи-union-и-union-all)
- [Типы данных SQL](#типы-данных-sql) 


# Основы SQL

**Язык структурированных запросов** (Structured Query Language, SQL) — язык, предназначенный для управления данными в системе реляционных баз данных (RDBMS).

## Типы команд в SQL: `DDL`, `DQL`, `DML`, `DCL`, `TCL`

### Язык определения данных (`DDL`)

**Язык определения данных** (`Data Definition Language`, `DDL`) состоит из команд, которые используются для определения схемы базы данных. 

DDL-команды помогают задать или изменить структуру объектов базы данных.

**Объектами базы данных** (`Database objects`) являются таблицы, индексы, функции, представления, процедуры и триггеры.

Примеры DDL-команд
* `CREATE` — создание базы данных или её объектов.
* `ALTER` — изменение структуры базы данных.
* `DROP` — удаление объектов из базы данных.
* `TRUNCATE` — удаление всех записей из таблицы, в том числе и записей о самом удалении.

### Язык запросов данных (`DQL`)

**Язык запросов данных** (`Data Query Language`, `DQL`) предоставляет выражения, которые позволяют получить часть данных из базы данных по заданному запросу (query).

Пример DQL-команд
* `SELECT` — получение данных из базы данных.

### Язык манипулирования данными (`DML`)

**Язык манипулирования данными** (`Data Manipulation Language`, `DML`) состоит из команд, которые используются для манипулирования данными, которые хранятся в базе.

Пример DML-команд
* `INSERT` — вставка данных в таблицу.
* `UPDATE` — обновление данных таблицы.
* `DELETE` — удаление записей из таблицы.

### Язык контроля над данными (`DCL`)

**Язык управления данными** (`Data Control Language`, `DCL`) предоставляет команды, позволяющие управлять правами доступа, разрешения

Пример DCL-команд
* `GRANT` — предоставление пользователю доступа к базе данных.
* `REVOKE` — изымание доступа пользователя к базе данных.

### Язык управления транзакциями (`TCL`)

**Язык управления транзакциями** (`Transaction Control Language`, `TCL`) предоставляет команды, позволяющие управлять транзациями в базе данных.

Транзакции выполняются либо полностью, либо никак. Промежуточных результатов быть не может: они отменяются при неудаче.

Примеры TCL-команд
* `COMMIT` — сохранение результатов успешно выполненной транзакции в базу данных.
* `ROLLBACK` — откат транзакции в случае ошибок.
* `SAVEPOINT` — создание контрольной точки в пределах транзакции, чтобы при неудаче откатывать не всю транзакцию целиком.  

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

## Создание таблицы с помощью `CREATE`
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
## Вставка в таблицу с помощью `INSERT`
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

## Запросы `SELECT`

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
![SQL Example](../assets/SQL_1.png)
```SQL
SELECT * FROM timers;
```
![SQL Example](../assets/SQL_2.png)

### `SELECT DISTINCT`

Чтобы запрос *не выдавал повторяющиеся строки*, существует `SELECT DISTINCT`.

Например, в таблице `notes` две строки содержат `null` в `description`.
```SQL
SELECT DISTINCT description FROM notes;
```

![SQL Example](../assets/SQL_3.png)

*Строка пропускается* только, если *все её значения совпадают* со *значеними другой строки*.
```SQL
SELECT DISTINCT title, description FROM notes;
```

![SQL Example](../assets/SQL_4.png)
 
## Запросы `SELECT` с условиями `WHERE`

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
![SQL Example](../assets/SQL_5.png)
```SQL
SELECT *
  FROM notes
  WHERE title != "Article #1" AND description IS NOT NULL;
```
![SQL Example](../assets/SQL_6.png)
```SQL
SELECT *
  FROM timers
  WHERE seconds > 30;
```
![SQL Example](../assets/SQL_7.png)

## Логические операторы

**Логические операторы** (англ. `Logical operators`) проверяют заданное выражение на истинность и возвращают логическое значение `TRUE` или `FALSE`.

В SQL имеются следующие логические операторы:
* `AND` (логическое "И", условное "И") - бинарный логический оператор, который возвращает `TRUE`, если оба операнда (оба условия) выполняются, иначе `FALSE`.
```SQL
x AND y
1 > 0 AND 2 > 1 = TRUE
1 > 0 AND 2 < 1 = FALSE
``` 
* `OR` (логическое "ИЛИ", условное "ИЛИ") - бинарный логический оператор, который возвращает `TRUE`, если хотя бы один операнд (одно из условий) выполняются, иначе `FALSE`.
```SQL
x OR y
1 > 0 OR 2 > 1 = TRUE
1 > 0 OR 2 < 1 = FALSE
```
* `NOT` (логическое "НЕ", отрицание) - унарный оператор, который возвращает `TRUE`, если значение ложно, и `FALSE`, если значение истино.
* `IN` (включение, англ. `includes`) - бинарный логический оператор, который возвращает `TRUE`, если операнд содержится в списке выражений.
```SQL
x IN (y1, y2, ...)
0 IN (1, 2, 3) = FALSE
"bar IN ("foo", "bar, "baz") = TRUE
WHERE name IN ("Max", "Rob", "Tom")
```
* `BETWEEN` (между) - тернарный логический оператор, который возвращает `TRUE`, если заданное значение (*число*, *строка*, *дата*) находится в промежутке между двумя другими значениями.
```SQL
x BETWEEN y AND z
'20230217' BETWEEN '20221212' AND '20230605' = TRUE
17 BETWEEN 0 AND 10 = FALSE
```
* `LIKE` (подобно) - бинарный логический оператор, который проверяет, удовлетволяет ли указанная строка заданному поисковому шаблону (регулярному выражению). Для поиска используются подстановочные знаки (англ. `wildcards`). Например, знак `%` заменяет *любое количество символов*, `_` — только один символ.
```SQL
'abc' LIKE 'a_c' AND 'adc' LIKE 'a_c' = TRUE
'max.starling' LIKE 'max_' = TRUE
'max.starling' LIKE '_star_' = TRUE
```
 
## Приоритет операторов (`Operator Precedence`)
Когда мы имеем дело со сложным выражением, в котором участвует несколько разных операторов, встаёт вопрос, какие операции выполнятся раньше. Ведь в зависимости от порядка выполнения операций меняется результат.

Например, у умножения приортитет всегда выше, чем у сложения. Но если поставить оператор группировки, то приоритеты поменяются и результат тоже
```SQL
5 + 1 * 5 = 30
(5 + 1) * 5 = 30
5 + (1 * 5) = 10
```
Чем выше приоритет, тем раньше выполняется операция.

Стоит отметить, что в каждой реализации SQL порядок операторов может отличаться.

#### Приоритет операторов в Postgres
| Operator/Element | Associativity | Description                                 |
|------------------|---------------|---------------------------------------------|
| ::               | left          | PostgreSQL-style typecast                   |
| [ ]              | left          | array element selection                     |
| .                | left          | table/column name separator                 |
| -                | right         | unary minus                                 |
| ^                | left          | exponentiation                              |
| * / %            | left          | multiplication, division, modulo            |
| + -              | left          | addition, subtraction                       |
| IS               |               | test for TRUE, FALSE, UNKNOWN, NULL         |
| ISNULL           |               | test for NULL                               |
| NOTNULL          |               | test for NOT NULL                           |
| (any other)      | left          | all other native and user-defined operators |
| IN               |               | set membership                              |
| BETWEEN          |               | containment                                 |
| OVERLAPS         |               | time interval overlap                       |
| LIKE ILIKE       |               | string pattern matching                     |
| < >              |               | less than, greater than                     |
| =                | right         | equality, assignment                        |
| NOT              | right         | logical negation                            |
| AND              | left          | logical conjunction                         |
| OR               | left          | logical disjunction                         |

#### Приоритет операторов в MySQL
```MySQL
INTERVAL
BINARY, COLLATE
!
- (unary minus), ~ (unary bit inversion)
^
*, /, DIV, %, MOD
-, +
<<, >>
&
|
= (comparison), <=>, >=, >, <=, <, <>, !=, IS, LIKE, REGEXP, IN, MEMBER OF
BETWEEN, CASE, WHEN, THEN, ELSE
NOT
AND, &&
XOR
OR, ||
= (assignment), :=
```

#### Приоритет операторов в MariaDB
```SQL
INTERVAL
BINARY, COLLATE
!
- (unary minus), [[bitwise-not|]] (unary bit inversion)
|| (string concatenation)
^
*, /, DIV, %, MOD
-, +
<<, >>
&
|
= (comparison), <=>, >=, >, <=, <, <>, !=, IS, LIKE, REGEXP, IN
BETWEEN, CASE, WHEN, THEN, ELSE, END
NOT
&&, AND
XOR
|| (logical or), OR
= (assignment), :=
```

#### Приоритет операторов в Oracle
```SQL
()
* /
+ -
= <> < > <= >=
IS (IS NULL, IS NOT NULL, IS EMPTY, IS NOT EMPTY)
BETWEEN
NOT
AND
OR
```

Как видно, приоритеты могут отличаться, но общие правила есть всегда:
1) Оператор группировки `()` (англ. `parentheses`, `grouping`) имеет *наивысший приоритет*, в то время, как оператор присваивания `=` - *наинизший*.
2) Арифметические операторы всегда идут в следующем порядке: *возведение в степень*, *умножение/деление*, затем *сложение/вычитание*.
3) Логические операторы всегда идут в следующем порядке: сперва отрицание `!`, `NOT`, затем логическое "И" (`AND`, `&&`), затем *логическое "ИЛИ"* (`OR`, `||`).
4) Сперва идут *арифметические операторы*, затем *операторы сравнения*, затем *логические операторы*.


Например, поскольку у `AND` приоритет выше, чем у `OR`, следующие две строчки эквивалентны (то есть оператор группировки можно смело убирать)
```SQL
(x < y AND y < z) OR (x > y AND y > z)
x < y AND y < z OR x > y AND y > z
```

## Сортировка при помощи `ORDER BY`

**Ключевое слово ORDER BY** используется для *сортировки результатов запроса* `SELECT`.

Можно указать *порядок сортировки*:
1) `ASC` (ascending) — *по возрастанию*, используется *по умолчанию*;
2) `DESC` (descending) — *по убыванию*.
```SQL
SELECT *
  FROM notes
  ORDER BY description;
```
![SQL Example](../assets/SQL_orderby)
```SQL
SELECT *
  FROM notes
  ORDER BY ID DESC;
```
![SQL Example](../assets/SQL_orderby_2.png)

## Агрегатные функции `AVG`, `COUNT`, `MAX`, `MIN`, `SUM`

**Агрегатные функции** *применяются* к *столбцу* и *возвращают единое значение*.

* `AVG (column_name)` — среднее значение столбца `column_name`.
* `COUNT (column_name)` — количество строк.
* `MAX (column_name)` — наибольшее значение столбца.
* `MIN (column_name)` — наименьшее значение столбца.
* `SUM (column_name)` — сумма значений в столбце.

```SQL
SELECT COUNT(ID), MIN(ID), AVG(ID), MAX(ID), SUM(ID) FROM notes;
```
![SQL Example](../assets/SQL_aggregate.png)

### Оператор `AS`

**Оператор  AS** используется для *переименования столбца* в *результате* запроса `SELECT`.
```sql
-- схема
SELECT <column_name> AS <name> FROM <table_name>;
```

```sql
SELECT MAX(seconds) AS max_seconds FROM timers;
```
![SQL Example](../assets/SQL_as.png)

## Группировка значений при помощи `GROUP BY`

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
![SQL Example](../assets/SQL_groupby.png)

Когда имеет смысл использовать группировку?
Когда столбец имеет повторяющиеся значения. Например, профессия - мы можем сгруппировать данные средней зарлаты по всем сотрудникам.
 ```SQL
SELECT job, AVG(salary) AS job_salary
  FROM jobs_data
  WHERE job = "SysAdmin"
  GROUP BY job;
```
В этом случае в выборку попадут строки с уникальными значениями `job`, каждая из которых будет содержать вычисленное значение средней заработной платы `AVG(salary)`.

Если необходимо вычислить зарплату по какой-либо конкретной профессии, то следует добавить условие `WHERE`. Но стоит учесть, что для `WHERE` нельзя указывать выражение, содержащее агрегатную функцию.
```SQL
SELECT job, AVG(salary) AS sysadmin_salary
  FROM jobs_data
  WHERE job = "SysAdmin"
  GROUP BY job;
```

Можно *группировать одновременно* по *нескольким колонкам*: в этом случае в выборку попадут *все существующие комбинации* будут учтены все допустимые значения.
Например,
```SQL
SELECT job, seniority, AVG(salary) AS sysadmin_salary
  FROM jobs_data
  GROUP BY job, seniority;
```
Запрос выше позволяет сгруппировать данные не только по профессии, но и по стажу работы в соответствии с позициями (`Junior`, `Middle`, `Senior`).

Важно также отметить, что при использовании группировки `GROUP BY` в `SELECT` обычно имеет смысть выбирать только те колонки, которые:  
1) либо указаны в `GROUP BY` через запятую.  
2) либо вычисляются в одной из агрегационной функции.  
Все остальные колонки вернут непредвиденный результат, зависящий от движка SQL: это может быть первое значение из группы, последнее или же вовсе ошибка выполнения запроса с пометкой, что запрашиваемое поле нужно добавить в `GROUP BY`.

### `GROUP BY` с условиями `HAVING`

Как уже отмечалось выше, *ключевое слово* `WHERE` *не может использоваться* с *агрегатными функциями*. Для этих целей ввели **ключевое слово HAVING**, которое должно быть использовано сразу же после `GROUP BY`.
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
Запрос ниже выдаст ошибку:
```SQL
-- ошибка
SELECT *, COUNT(ID) AS rows_count
  FROM notes
  WHERE COUNT(description) = 1;
  GROUP BY description
```

### Когда использовать `WHERE`, а когда `HAVING`

Если выражение не содержит агрегатную функцию, то его можно поместить либо в `WHERE`, либо в `HAVING` с сохранением результата. Например,
```SQL
WHERE created_at > '1998-02-17'
GROUP BY created_at
-- или
GROUP BY created_at
HAVING created_at > '1998-02-17'
```
Что будет работать производительнее?

`WHERE` должно отработать быстрее `HAVING`, поскольку `WHERE` применяется раньше, чем `HAVING` (`HAVING` выполняется предпоследним: перед `LIMIT`), а значит все последующие операторы оперируют меньшим числом строк (меньше работы - меньше затраченных ресурсов - меньше время выполнения). Некоторые SQL-движки позволяют сгладить это таким образом, что.

Ниже представлены несколько сносок со Stackoverflow на эту тему.

> The SQL Standard theory says that WHERE restricts the result set before returning rows and HAVING restricts the result set after bringing all the rows. So WHERE is faster. On SQL Standard compliant DBMSs in this regard, only use HAVING where you cannot put the condition on a WHERE (like computed columns in some RDBMSs.)

> The HAVING clause is applied nearly last, just before items are sent to the client, with no optimization. (LIMIT is applied after HAVING.)

> The two queries are equivalent and your DBMS query optimizer should recognise this and produce the same query plan. It may not, but the situation is fairly simple to recognise, so I'd expect any modern system - even Sybase - to deal with it.

> HAVING clauses should be used to apply conditions on group functions, otherwise they can be moved into the WHERE condition. For example. if you wanted to restrict your query to groups that have COUNT(DZIALU) > 10, say, you would need to put the condition into a HAVING because it acts on the groups, not the individual rows.

## Связь таблиц при помощи `JOIN`

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
![SQL Example](../assets/SQL_join_1.png)
![SQL Example](../assets/SQL_join_2.png)

* `JOIN`, `INNER JOIN` (внутреннее объединение) — строки, содержащиеся и в A, и в B.

```sql
SELECT *
  FROM seconds A
  JOIN timers B
  ON A.value = B.seconds;
```
![SQL Example](../assets/SQL_join_3.png)

* `LEFT JOIN` (левосторонее объединение) — строки, содержащиеся в A, даже при их отсутствии в B.
```sql
SELECT A.ID AS a_id, B.ID AS b_id, A.value, B.seconds
  FROM seconds A
  LEFT JOIN timers B
  ON A.value = B.seconds;
```
![SQL Example](../assets/SQL_join_4.png)

* `LEFT JOIN` без пересечения — строки, содержащиеся в A, но не содержащиеся в B.
```sql
SELECT *
  FROM seconds A
  LEFT JOIN timers B
  ON A.value = B.seconds
  WHERE B.seconds IS NULL;
```
![SQL Example](../assets/SQL_join_5.png)

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

## Виртуальная таблица `VIEW`

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
![SQL Example](../assets/SQL_view_1.png)

Поскольку `VIEW` хранит ссылки на реальные данные, он знает всё об их изменениях и всегда отображает последнюю версию.
```SQL
INSERT INTO timers VALUES ("timer7", 25);

SELECT * FROM timers_less_30sec;
```
![SQL Example](../assets/SQL_view_2.png)


## Изменение данных с помощью `UPDATE`

Обновление строк таблицы
```SQL
UPDATE <table_name>
  SET <column_name> = <value>, <another_column_name> = <another_value>, /* ... */
  WHERE <condition>; 
```
# Удаление данных с помощью `DROP`
Удаление таблицы.
```SQL
DROP TABLE <table_name>; 
```
Удаление `VIEW`.
```SQL
DROP VIEW <view_name>; 
```

## Удаление данных с помощью `TRUNKATE` и `DELETE`

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

## Вложенные запросы `SELECT`

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

## Объединение результатов запросов при помощи `UNION` и `UNION ALL`

Оператор объединения `UNION` предназначен для объединения результатов двух и более `SELECT`-запросов.

Условия, при которых применим `UNION`, накладываются на его операнды. Каждый `SELECT`, попадающий под объединение, должен иметь
1) Одинаковое количество столбцов с другими `SELECT`.
2) Типы данных в столбцах должны совпадать.
3) Порядок столбцов также должен совпадать.

```sql
SELECT <column_names...> FROM table1
UNION
SELECT <column_names...> FROM table2;
```
Оператор `UNION` по умолчачнию отображает лишь уникальные строки.

Чтобы позволить повторения, необходимо использовать оператор `UNIAN ALL`.
```sql
SELECT <column_names...> FROM table1
UNION ALL
SELECT <column_names...> FROM table2;
```

# Типы данных в PostgreSQL

## Дата

Приведение текущей даты в формате `YYYY-MM-DD`.
```sql
CAST(CURRENT_TIMESTAMP AS DATE)
-- или
CURRENT_TIMESTAMP::DATE
```
Можно подставить название столбца, чтобы привести его значение в формат `YYYY-MM-DD`.
```sql
SELECT created_at::DATE
  FROM users
```

