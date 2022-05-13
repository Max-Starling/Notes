
# О хранилище данных (`Data Warehouse`)

**Хранилище данных** (англ. `Data Warehouse`, `DW`) - это такая система управления данными (англ. `data management system`), которая специально разработана для хранения отчётов бизнес-аналитики (англ. `business intelligence`), в том числе аналитики (англ. `analytics`), для поддержки принятия решений на уровне компании.

Чаще всего хранилища данных содержат огромные объёмы исторической информации (террабаты или даже десятки террабайт).

Данные чаще всего поступают из многих источников в разных форматах, которые затем адаптируются под один формат и доступны для выполнения поисковых SQL-запросов.

Данные в такой системе обычно доступны только для чтения.

Храни́лище да́нных (англ. Data Warehouse) — предметно-ориентированная информационная база данных, специально разработанная и предназначенная для подготовки отчётов и бизнес-анализа с целью поддержки принятия решений в организации. Строится на базе систем управления базами данных и систем поддержки принятия решений. Данные, поступающие в хранилище данных, как правило, доступны только для чтения.

# Что такое Snowflake?


Snowflake - это хранилище данных, которое полностью выполняется в облачной инфраструктуре и не может быть запущено на некотором приватном облаке.

Система Snowflake не является реляционной базой данных, поэтому в ней не поддерживается концепция первичных и вторичных ключей (англ. `primary and foreign keys`, `PK & FK`).

Тем не менее, Snowflake поддерживает набор SQL-команд для удобства чтения, в том числе:
- DDL/DML,
- SQL Functions,
- User Defined Functions (UDFs), процедуры с использованием JavaScript.

Поддерживаются команды:
```sql
SELECT
JOIN
INSERT UPDATE DELETE
VIEW
```
а также merge statements, subqueries, ACID transactions, Analytical Aggregations (cube, rollup, groping sets, windowing functions, connect-by, recursive CTE)

A data warehouse is a type of data management system that is designed to enable and support business intelligence (BI) activities, especially analytics. Data warehouses are solely intended to perform queries and analysis and often contain large amounts of historical data.

In computing, a data warehouse, also known as an enterprise data warehouse, is a system used for reporting and data analysis and is considered a core component of business intelligence. DWs are central repositories of integrated data from one or more disparate sources.
