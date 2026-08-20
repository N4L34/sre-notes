# DB

## 1. Основы

### Что такое база данных

База данных — организованное хранилище данных.

Она позволяет:

- сохранять информацию;

- искать данные;

- изменять записи;

- связывать сущности;

- обеспечивать целостность и доступность.


---

### DBMS

DBMS — Database Management System, система управления базами данных.

DBMS отвечает за:

- хранение;

- SQL-запросы;

- транзакции;

- блокировки;

- пользователей и права;

- backup и replication.


Примеры:

- PostgreSQL;

- MySQL;

- MongoDB;

- Redis;

- Oracle Database.


---

### SQL vs NoSQL

SQL databases обычно используют таблицы, строгую schema и SQL.

Примеры:

- PostgreSQL;

- MySQL;

- Oracle.


NoSQL — общее название нереляционных баз.

Типы NoSQL:

- document;

- key-value;

- column-family;

- graph.


```text
SQL   -> таблицы, JOIN, ACID
NoSQL -> гибкие модели и специализированное масштабирование
```

Выбор зависит от задачи, а не от того, какая технология «лучше».

---

### Relational Database

Relational Database хранит данные в связанных таблицах.

Например:

```text
users
orders
products
```

Связи задаются через primary и foreign keys.

---

### Table, Row, Column

Table — таблица с данными.

Row — отдельная запись.

Column — отдельное поле записи.

```text
users
+----+-------+-------------------+
| id | name  | email             |
+----+-------+-------------------+
| 1  | Alice | alice@example.com |
+----+-------+-------------------+
```

---

### Schema

Schema может означать:

1. Структуру базы: таблицы, колонки, типы, constraints.

2. Namespace внутри базы, например в PostgreSQL:


```text
public.users
billing.invoices
```

Schema помогает разделять объекты логически.

---

### Primary Key

Primary Key уникально идентифицирует строку.

```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY,
    name TEXT
);
```

Primary Key:

- уникален;

- не может быть `NULL`;

- обычно имеет index.


---

### Foreign Key

Foreign Key связывает таблицы и обеспечивает referential integrity.

```sql
CREATE TABLE orders (
    id BIGINT PRIMARY KEY,
    user_id BIGINT REFERENCES users(id)
);
```

Нельзя создать order с `user_id`, которого нет в `users`, если constraint активен.

---

### Constraints

Constraints — ограничения на данные.

Основные:

- `PRIMARY KEY`;

- `FOREIGN KEY`;

- `UNIQUE`;

- `NOT NULL`;

- `CHECK`.


Они защищают целостность данных на уровне базы.

---

### `UNIQUE`

`UNIQUE` запрещает повторяющиеся значения.

```sql
CREATE TABLE users (
    email TEXT UNIQUE
);
```

Обычно DBMS создает unique index для проверки.

Особенности поведения нескольких `NULL` зависят от DBMS и настройки constraint.

---

### `NOT NULL`

`NOT NULL` запрещает отсутствие значения.

```sql
CREATE TABLE users (
    email TEXT NOT NULL
);
```

Используется для обязательных полей.

---

### `CHECK`

`CHECK` проверяет условие при записи данных.

```sql
CREATE TABLE products (
    price NUMERIC CHECK (price >= 0),
    quantity INTEGER CHECK (quantity >= 0)
);
```

Если условие false, запись будет отклонена.

---

## 2. SQL Basics

### `SELECT`

`SELECT` читает данные.

```sql
SELECT id, name
FROM users;
```

Все колонки:

```sql
SELECT *
FROM users;
```

В production-коде лучше явно перечислять нужные колонки.

---

### `INSERT`

`INSERT` добавляет строки.

```sql
INSERT INTO users (name, email)
VALUES ('Alice', 'alice@example.com');
```

Несколько строк:

```sql
INSERT INTO users (name, email)
VALUES
    ('Alice', 'alice@example.com'),
    ('Bob', 'bob@example.com');
```

---

### `UPDATE`

`UPDATE` изменяет существующие строки.

```sql
UPDATE users
SET name = 'Alice Smith'
WHERE id = 1;
```

Без `WHERE` будут обновлены все строки.

---

### `DELETE`

`DELETE` удаляет строки.

```sql
DELETE FROM users
WHERE id = 1;
```

Без `WHERE` будут удалены все строки таблицы.

---

### `WHERE`

`WHERE` фильтрует строки.

```sql
SELECT *
FROM users
WHERE active = true
  AND created_at >= '2026-01-01';
```

---

### `ORDER BY`

`ORDER BY` сортирует результат.

```sql
SELECT *
FROM users
ORDER BY created_at DESC;
```

Несколько полей:

```sql
ORDER BY status ASC, created_at DESC;
```

---

### `GROUP BY`

`GROUP BY` группирует строки для агрегатных функций.

```sql
SELECT status, COUNT(*)
FROM orders
GROUP BY status;
```

---

### `HAVING`

`HAVING` фильтрует уже сгруппированные результаты.

```sql
SELECT user_id, COUNT(*) AS orders_count
FROM orders
GROUP BY user_id
HAVING COUNT(*) > 10;
```

```text
WHERE  -> до группировки
HAVING -> после группировки
```

---

### `JOIN`

`JOIN` объединяет строки из нескольких таблиц.

```sql
SELECT users.name, orders.id
FROM users
JOIN orders ON orders.user_id = users.id;
```

Основные типы:

- `INNER JOIN`;

- `LEFT JOIN`;

- `RIGHT JOIN`;

- `FULL JOIN`.


---

### Subquery

Subquery — запрос внутри другого запроса.

```sql
SELECT *
FROM users
WHERE id IN (
    SELECT user_id
    FROM orders
    WHERE total > 1000
);
```

Иногда subquery удобнее заменить на `JOIN` или Common Table Expression.

---

### Aggregate functions

Aggregate functions вычисляют значение по набору строк.

Основные:

```sql
COUNT(*)
SUM(total)
AVG(price)
MIN(created_at)
MAX(created_at)
```

Пример:

```sql
SELECT
    COUNT(*) AS orders,
    SUM(total) AS revenue
FROM orders;
```

---

## 3. Индексы

### Что такое индекс

Индекс — отдельная структура данных для ускорения поиска.

Без index база часто должна проверить каждую строку.

```text
Без index -> Sequential Scan
С index   -> Index Scan
```

Индекс ускоряет чтение, но занимает место и замедляет запись.

---

### B-tree index

B-tree — основной тип index в PostgreSQL и MySQL.

Подходит для:

- `=`;

- `<`, `>`;

- `BETWEEN`;

- сортировки;

- prefix поиска по составному index.


```sql
CREATE INDEX idx_users_email
ON users(email);
```

---

### Composite Index

Composite Index содержит несколько колонок.

```sql
CREATE INDEX idx_orders_user_status
ON orders(user_id, status);
```

Он хорошо подходит для запросов:

```sql
WHERE user_id = 10
```

и:

```sql
WHERE user_id = 10
  AND status = 'paid'
```

Но может не помочь запросу только по `status`.

---

### Unique Index

Unique Index одновременно:

- ускоряет поиск;

- запрещает повторяющиеся значения.


```sql
CREATE UNIQUE INDEX idx_users_email_unique
ON users(email);
```

---

### Index Scan vs Sequential Scan

Sequential Scan читает таблицу последовательно.

Index Scan использует index для поиска подходящих строк.

Sequential Scan может быть быстрее, если:

- таблица маленькая;

- нужно прочитать большую часть строк;

- index мало селективен.


Использование index не всегда является целью само по себе.

---

### Когда индекс ускоряет запрос

Индекс особенно полезен, когда:

- условие возвращает малую часть таблицы;

- часто используется `WHERE`;

- есть `JOIN` по колонке;

- нужна сортировка или поиск диапазона;

- таблица большая;

- значение достаточно селективно.


---

### Когда индекс мешает

Каждый index нужно обновлять при:

```text
INSERT
UPDATE
DELETE
```

Проблемы лишних indexes:

- медленнее запись;

- больше disk usage;

- дольше backup;

- больше cache pressure;

- выше стоимость VACUUM и maintenance.


---

### Порядок колонок в индексе

Для index:

```sql
CREATE INDEX idx_orders_user_status
ON orders(user_id, status);
```

Порядок важен.

Обычно сначала ставят колонки, которые:

- часто участвуют в условиях;

- используются с equality;

- хорошо ограничивают выборку;

- соответствуют реальным query patterns.


Принцип называют leftmost prefix.

---

### `EXPLAIN`

`EXPLAIN` показывает query plan без выполнения запроса.

```sql
EXPLAIN
SELECT *
FROM users
WHERE email = 'alice@example.com';
```

Можно увидеть:

- тип scan;

- join algorithm;

- estimated rows;

- estimated cost.


---

### `EXPLAIN ANALYZE`

`EXPLAIN ANALYZE` реально выполняет запрос и показывает фактическое время.

```sql
EXPLAIN ANALYZE
SELECT *
FROM users
WHERE email = 'alice@example.com';
```

Важно: для `UPDATE`, `DELETE` и `INSERT` запрос действительно изменит данные, если не выполнять его внутри transaction с rollback.

---

## 4. Транзакции

### ACID

ACID — свойства транзакций.

```text
A — Atomicity
C — Consistency
I — Isolation
D — Durability
```

Atomicity — либо выполняются все операции, либо ни одна.

Consistency — данные остаются валидными по правилам базы.

Isolation — параллельные транзакции не должны некорректно мешать друг другу.

Durability — после commit изменения сохраняются даже после сбоя.

---

### `BEGIN`

`BEGIN` начинает транзакцию.

```sql
BEGIN;
```

После этого изменения еще не зафиксированы окончательно.

---

### `COMMIT`

`COMMIT` подтверждает изменения.

```sql
COMMIT;
```

После успешного commit изменения становятся видны согласно isolation level и должны сохраняться.

---

### `ROLLBACK`

`ROLLBACK` отменяет изменения текущей транзакции.

```sql
ROLLBACK;
```

Полезно при ошибке или проверке миграции.

---

### Isolation Levels

Основные уровни изоляции:

```text
Read Uncommitted
Read Committed
Repeatable Read
Serializable
```

Чем сильнее isolation, тем меньше аномалий, но потенциально больше blocking, retries и накладных расходов.

Реальное поведение зависит от конкретной DBMS.

---

### Dirty Read

Dirty Read — чтение данных, которые другая транзакция еще не закоммитила.

Если та транзакция сделает rollback, прочитанные данные никогда реально не существовали в committed state.

---

### Non-repeatable Read

Non-repeatable Read — одна транзакция дважды читает одну строку и получает разные значения.

Причина: другая транзакция успела изменить и закоммитить строку между чтениями.

---

### Phantom Read

Phantom Read — повторный запрос по условию возвращает другой набор строк.

Например, между запросами другая транзакция добавила новую строку, подходящую под `WHERE`.

---

### Locks

Locks защищают данные при параллельных операциях.

Бывают:

- row locks;

- table locks;

- shared locks;

- exclusive locks;

- advisory locks.


Пример:

```sql
SELECT *
FROM accounts
WHERE id = 1
FOR UPDATE;
```

Строка блокируется для конкурирующего изменения.

---

### Deadlock

Deadlock — ситуация, когда транзакции ждут друг друга.

```text
Transaction A держит Row 1 и ждет Row 2
Transaction B держит Row 2 и ждет Row 1
```

DBMS обнаруживает deadlock и отменяет одну транзакцию.

Защита:

- блокировать ресурсы в одинаковом порядке;

- держать транзакции короткими;

- не выполнять внешние вызовы внутри transaction;

- обрабатывать retry.


---

### Optimistic vs Pessimistic Locking

Pessimistic locking блокирует данные заранее.

```sql
SELECT ... FOR UPDATE;
```

Optimistic locking предполагает, что конфликт редок, и проверяет версию при записи.

```sql
UPDATE documents
SET body = 'new', version = version + 1
WHERE id = 10
  AND version = 5;
```

Если обновлено 0 строк, произошел конфликт.

---

## 5. Производительность

### Slow Queries

Slow Query — запрос, выполняющийся дольше ожидаемого.

Причины:

- отсутствует index;

- плохой query plan;

- большой объем данных;

- lock;

- disk I/O;

- неверная статистика;

- N+1;

- сложные joins или сортировки.


---

### Query Plan

Query Plan показывает, как DBMS собирается выполнить запрос.

Он содержит:

- scans;

- joins;

- sorting;

- aggregation;

- estimated rows;

- costs.


Плохая оценка количества строк может привести к неправильному плану.

---

### Missing Index

Признаки отсутствующего index:

- Sequential Scan большой таблицы;

- много прочитанных строк;

- запрос возвращает мало строк;

- высокая latency;

- высокий disk I/O.


Но index нужно создавать под реальные запросы, а не на каждую колонку.

---

### Connection Pool

Connection Pool хранит готовые подключения к базе.

```text
Application threads
        ↓
Connection Pool
        ↓
Database connections
```

Плюсы:

- меньше стоимости создания connections;

- ограничение параллелизма;

- защита базы от connection storm.


---

### Maximum Connections

Database имеет предел подключений.

Слишком большое значение опасно:

- каждое connection потребляет память;

- растет context switching;

- больше конкуренция за locks и CPU.


Лучше использовать connection pool и разумно ограничивать concurrency.

---

### Long-running Transactions

Долгие транзакции могут:

- держать locks;

- мешать VACUUM;

- увеличивать bloat;

- удерживать старые версии строк;

- раздувать WAL;

- задерживать replication.


Транзакции нужно делать короткими.

---

### Lock Contention

Lock contention — много транзакций конкурируют за один ресурс.

Симптомы:

- запросы ждут;

- latency растет;

- CPU может быть невысоким;

- active connections накапливаются.


Причины:

- одна «горячая» строка;

- длинные transactions;

- массовый update;

- миграция;

- неправильный порядок locks.


---

### CPU bottleneck

База может использовать много CPU из-за:

- тяжелых joins;

- сортировок;

- агрегаций;

- слишком большого concurrency;

- отсутствующих indexes;

- compression;

- плохого query plan.


Проверять нужно одновременно OS metrics и active queries.

---

### Disk I/O bottleneck

Признаки:

- высокая disk latency;

- высокий I/O wait;

- низкий cache hit ratio;

- много physical reads;

- медленные checkpoints;

- большие sequential scans.


Команды на Linux:

```bash
iostat -xz 1
vmstat 1
```

---

### Cache Hit Ratio

Cache Hit Ratio показывает, какая часть чтений обслуживается из memory cache, а не с disk.

Высокий ratio обычно хорошо, но его нельзя оценивать отдельно от workload.

Низкий показатель может означать:

- мало RAM;

- слишком большой working set;

- большие sequential scans;

- cache pollution.


---

### N+1 Query Problem

N+1 — приложение выполняет один запрос для списка и затем отдельный запрос для каждого элемента.

```text
1 запрос получает 100 users
100 запросов получают orders каждого user
Итого: 101 запрос
```

Решения:

- `JOIN`;

- batch query;

- eager loading;

- application-side batching.


---

## 6. Репликация и High Availability

### Primary и Replica

Primary принимает записи.

Replica копирует изменения с Primary.

```text
Writes -> Primary
Reads  -> Primary или Replica
```

Replica используется для:

- read scaling;

- failover;

- backup;

- reporting.


---

### Synchronous Replication

При synchronous replication commit подтверждается только после подтверждения replica.

Плюсы:

- ниже риск потери committed data.


Минусы:

- выше latency;

- проблемы replica могут задерживать writes.


---

### Asynchronous Replication

Primary подтверждает commit, не ожидая полного применения на replica.

Плюсы:

- меньше latency;

- replica меньше влияет на writes.


Минус:

- при аварии можно потерять последние изменения.


---

### Replication Lag

Replication Lag — отставание replica от Primary.

Причины:

- медленная сеть;

- слабая replica;

- тяжелые queries на replica;

- большой поток writes;

- блокировки;

- недостаточный disk I/O.


При lag чтение с replica может возвращать устаревшие данные.

---

### Read Replica

Read Replica обслуживает read-only запросы.

Подходит для:

- отчетов;

- аналитики;

- чтения каталогов;

- снижения read load с Primary.


Нужно учитывать eventual consistency и replication lag.

---

### Failover

Failover — переключение роли Primary на другую node.

Этапы:

- обнаружить отказ;

- выбрать новую Primary;

- повысить Replica;

- перенаправить clients;

- защититься от старой Primary.


Failover бывает manual и automatic.

---

### Split Brain

Split Brain — несколько nodes одновременно считают себя Primary.

Это может привести к разным версиям данных.

Защита:

- quorum;

- fencing;

- consensus;

- надежный leader election;

- отключение старой Primary.


---

### Quorum basics

Quorum — минимальное число голосов для принятия решения.

В cluster из трех nodes обычно требуется большинство:

```text
2 из 3
```

Это помогает избежать двух Primary при network partition.

---

### Connection через database proxy

Database proxy дает приложению стабильный endpoint.

Он может:

- направлять writes на Primary;

- направлять reads на replicas;

- переключать после failover;

- делать pooling;

- ограничивать connections.


Примеры архитектур:

- application → proxy → database cluster;

- application → managed DB endpoint.


---

## 7. Backup и Recovery

### Full Backup

Full Backup содержит полную копию данных на определенный момент.

Плюсы:

- проще восстановление.


Минусы:

- занимает много места;

- долго создается;

- высокая нагрузка.


---

### Incremental Backup

Incremental Backup содержит только изменения после предыдущего backup.

Плюсы:

- быстрее;

- меньше storage.


Минусы:

- восстановление сложнее;

- нужна последовательность backups.


---

### Logical Backup

Logical Backup сохраняет логические объекты и данные.

Например:

- SQL statements;

- schema;

- rows.


Инструменты:

```text
PostgreSQL -> pg_dump
MySQL      -> mysqldump
```

Плюсы:

- переносимость;

- можно восстановить отдельные таблицы.


Минусы:

- медленнее на больших базах;

- восстановление требует выполнения SQL.


---

### Physical Backup

Physical Backup копирует файлы хранения базы.

Плюсы:

- быстрее для больших баз;

- подходит для полного восстановления instance.


Минусы:

- зависит от версии и DBMS;

- нельзя просто копировать работающую базу без согласованного механизма.


---

### Point-in-Time Recovery

PITR восстанавливает базу на конкретный момент.

```text
Full/Physical Backup
        +
WAL/Binlog до нужного времени
```

Например, можно восстановиться за минуту до ошибочного `DELETE`.

---

### WAL / Transaction Log

Transaction Log сначала фиксирует изменения в журнале.

PostgreSQL использует WAL.

MySQL использует redo log и binlog для разных задач.

Журнал нужен для:

- crash recovery;

- replication;

- PITR;

- durability.


---

### RPO

RPO — Recovery Point Objective.

Показывает допустимую потерю данных.

```text
RPO = 5 минут
```

Значит бизнес допускает потерю максимум последних пяти минут данных.

---

### RTO

RTO — Recovery Time Objective.

Показывает допустимое время восстановления сервиса.

```text
RTO = 30 минут
```

---

### Проверка восстановления backup

Backup без проверки восстановления нельзя считать надежным.

Нужно регулярно:

- восстанавливать в отдельное окружение;

- проверять целостность;

- запускать SQL-запросы;

- измерять фактический RTO;

- документировать процедуру.


---

### Backup Retention

Backup Retention определяет срок хранения backup.

Пример:

```text
daily   -> 14 дней
weekly  -> 8 недель
monthly -> 12 месяцев
```

Retention зависит от:

- бизнеса;

- compliance;

- стоимости;

- RPO;

- защиты от поздно обнаруженных ошибок.


---

## 8. Масштабирование

### Vertical Scaling

Vertical Scaling — увеличение ресурсов одной node.

```text
Больше CPU
Больше RAM
Быстрее disk
```

Плюсы:

- просто.


Минусы:

- есть предел;

- дороже;

- не решает Single Point of Failure.


---

### Horizontal Scaling

Horizontal Scaling — добавление nodes.

Для relational databases сложнее, потому что нужно координировать данные.

Варианты:

- replicas;

- sharding;

- distributed database;

- разделение workloads.


---

### Read Replicas

Read Replicas масштабируют чтение.

```text
Writes -> Primary
Reads  -> Replica-1 / Replica-2
```

Не масштабируют writes и могут отдавать устаревшие данные.

---

### Partitioning

Partitioning делит одну большую таблицу на части внутри одной логической базы.

Например по дате:

```text
events_2026_01
events_2026_02
events_2026_03
```

Для приложения это обычно остается одной таблицей.

---

### Sharding

Sharding распределяет данные между разными database nodes.

Например:

```text
users 1–1M   -> shard-1
users 1M–2M  -> shard-2
```

Сложности:

- выбор shard key;

- cross-shard joins;

- transactions;

- rebalance;

- hotspots.


---

### Connection Pooling

Connection Pooling масштабирует доступ к базе и защищает ее от слишком большого числа connections.

Типы:

- application pool;

- external pooler;

- database proxy.


---

### Caching

Cache уменьшает количество запросов к базе.

Примеры:

- Redis;

- application cache;

- CDN для публичных данных.


Проблемы:

- invalidation;

- stale data;

- cache stampede;

- consistency.


---

### Database Proxy

Database Proxy может:

- pooling;

- routing;

- failover;

- read/write split;

- authentication;

- connection limits.


Proxy также становится важным компонентом, который нужно делать отказоустойчивым.

---

## 9. PostgreSQL / MySQL Basics

### Архитектура PostgreSQL

PostgreSQL обычно использует отдельный server process для каждого client connection.

Основные компоненты:

- postmaster/main server process;

- backend processes;

- shared buffers;

- WAL;

- background writer;

- checkpointer;

- autovacuum workers;

- replication processes.


```text
Client -> Backend Process -> Shared Buffers / Storage
```

---

### Архитектура MySQL

MySQL использует один server process с threads для client connections.

Основные части:

- connection layer;

- SQL parser/optimizer;

- execution engine;

- storage engine;

- buffer pool;

- redo log;

- binlog.


Чаще всего используется storage engine InnoDB.

---

### Processes и Connections

Каждый database connection потребляет ресурсы.

PostgreSQL обычно создает отдельный process.

MySQL обычно использует thread.

Поэтому thousands connections могут привести к:

- большому memory usage;

- context switching;

- lock contention;

- деградации latency.


---

### PostgreSQL WAL

WAL — Write-Ahead Log.

Сначала изменение записывается в WAL, затем изменяются data pages.

Используется для:

- durability;

- crash recovery;

- replication;

- PITR.


---

### MySQL Binlog

Binary Log записывает изменения данных.

Используется для:

- replication;

- PITR;

- auditing изменений;

- CDC.


Binlog отличается от InnoDB redo log: redo нужен прежде всего для crash recovery storage engine.

---

### Vacuum basics

PostgreSQL использует MVCC.

При `UPDATE` или `DELETE` старые версии строк не всегда удаляются сразу.

`VACUUM`:

- освобождает dead tuples для повторного использования;

- обновляет visibility map;

- предотвращает transaction ID wraparound.


---

### Autovacuum

Autovacuum автоматически запускает VACUUM и ANALYZE.

Он нужен для:

- удаления dead tuples;

- поддержания статистики;

- защиты от wraparound;

- контроля bloat.


Отключать autovacuum глобально обычно нельзя.

---

### Table Bloat

Table Bloat — разрастание таблицы и indexes из-за dead tuples и неиспользуемого пространства.

Причины:

- много updates/deletes;

- долгие transactions;

- autovacuum не успевает;

- неправильные настройки.


Последствия:

- больше disk usage;

- медленнее scans;

- хуже cache efficiency.


---

### Slow Query Log

MySQL Slow Query Log записывает медленные запросы.

Настраивается порог выполнения и другие параметры.

Для PostgreSQL похожую задачу решают:

- `log_min_duration_statement`;

- `pg_stat_statements`;

- monitoring.


---

### System Tables

System tables/views содержат metadata и статистику.

PostgreSQL:

```text
pg_catalog
information_schema
pg_stat_activity
pg_stat_user_tables
```

MySQL:

```text
information_schema
performance_schema
mysql
sys
```

Они помогают диагностировать queries, locks, users и состояние базы.

---

## 10. Security

### Users и Roles

Database user или role определяет identity и права.

PostgreSQL использует roles, которые могут иметь login.

MySQL использует accounts вида:

```text
'user'@'host'
```

Не использовать один общий admin user для всех приложений.

---

### Grants и Permissions

Права выдаются через `GRANT`.

Пример:

```sql
GRANT SELECT, INSERT, UPDATE
ON orders
TO app_user;
```

Отозвать:

```sql
REVOKE DELETE
ON orders
FROM app_user;
```

---

### Least Privilege

Приложение должно иметь только необходимые права.

Например:

- read-only сервису не нужен `DELETE`;

- приложению не нужен superuser;

- migration user можно отделить от runtime user;

- доступ ограничивается нужной database/schema.


---

### TLS для подключения

TLS защищает credentials и данные между client и database.

Нужно:

- включить TLS;

- проверять CA;

- проверять hostname;

- не использовать insecure mode без причины.


---

### Encryption at Rest

Encryption at Rest защищает данные на storage.

Варианты:

- encrypted disk;

- database-native encryption;

- cloud storage encryption;

- encrypted backups.


Она не защищает от пользователя, который уже имеет SQL-доступ.

---

### Secrets для подключения

Database credentials нельзя хранить:

- в Git;

- в image;

- в открытом config;

- в logs.


Лучше использовать:

- secret manager;

- mounted secret files;

- workload identity;

- short-lived credentials;

- dynamic secrets.


---

### Audit Logs

Audit logs могут записывать:

- login;

- failed authentication;

- изменение roles;

- DDL;

- доступ к критичным таблицам;

- administrative operations.


Нужно учитывать объем и чувствительность этих логов.

---

### SQL Injection basics

SQL Injection возникает, когда user input вставляется в SQL как строка.

Плохо:

```text
"SELECT * FROM users WHERE name = '" + userInput + "'"
```

Правильно использовать:

- prepared statements;

- parameterized queries;

- ORM parameters;

- минимальные DB permissions.


---

### Ротация credentials

При rotation:

- создается новый password/token;

- приложение переводится на новую версию;

- соединения обновляются;

- старый credential отзывается;

- проверяются audit logs.


Желательно поддерживать короткий период перекрытия версий.

---

## 11. Monitoring

### Database Availability

Проверяется:

- принимает ли база connections;

- отвечает ли на простой query;

- доступна ли Primary;

- работает ли cluster;

- успешен ли health check.


Простой TCP-check недостаточен: port может быть открыт, но SQL не работать.

---

### Query Latency

Нужно смотреть:

- average;

- p95;

- p99;

- latency по типам запросов;

- slow query count;

- время ожидания locks.


---

### Connections

Мониторят:

- active;

- idle;

- waiting;

- maximum;

- pool usage;

- connection creation rate.


Рост connections может быть как причиной, так и следствием проблемы.

---

### Active Queries

Active Queries показывают, что выполняется прямо сейчас.

PostgreSQL:

```sql
SELECT *
FROM pg_stat_activity
WHERE state = 'active';
```

MySQL:

```sql
SHOW PROCESSLIST;
```

---

### Transactions

Мониторят:

- transactions per second;

- commits;

- rollbacks;

- длительность;

- idle in transaction;

- failed transactions.


---

### Locks

Важно видеть:

- ожидающие queries;

- blocker;

- тип lock;

- длительность ожидания;

- объект блокировки.


---

### Deadlocks

Deadlocks нужно считать и расследовать.

Редкие deadlocks могут быть ожидаемы при concurrency, но приложение должно корректно повторять transaction.

Постоянные deadlocks указывают на проблему порядка операций или transaction design.

---

### Replication Lag

Мониторят:

- lag по времени;

- lag в bytes/WAL;

- replay position;

- replication status;

- состояние replication slot;

- задержку apply.


---

### Disk Usage

Проверять:

- свободное место;

- скорость роста;

- WAL/binlog;

- temporary files;

- indexes;

- backups;

- tablespaces;

- inode.


Диск лучше не доводить до 100%.

---

### Database Size

Нужно отслеживать:

- общий размер;

- самые большие таблицы;

- самые большие indexes;

- рост по дням;

- bloat;

- размер WAL/binlog.


---

### Cache Hit Ratio

Cache hit ratio помогает оценить эффективность memory cache.

Но его нужно сопоставлять с:

- latency;

- disk reads;

- working set;

- типом workload;

- sequential scans.


---

### Slow Queries

Нужно собирать:

- normalized query;

- calls;

- total time;

- average time;

- rows;

- shared block reads;

- execution plan.


В PostgreSQL часто используют `pg_stat_statements`.

---

## 12. Troubleshooting

### Приложение не подключается к базе

Проверить по слоям:

```text
DNS
Route
Firewall
Port
TLS
Authentication
Database permissions
Database availability
```

Команды:

```bash
getent hosts db.example.com
nc -vz db.example.com 5432
```

Для PostgreSQL:

```bash
psql -h db.example.com -U app -d appdb
```

Для MySQL:

```bash
mysql -h db.example.com -u app -p
```

---

### `Connection refused`

`Connection refused` означает, что TCP target отклонил подключение.

Причины:

- база не запущена;

- слушает другой port;

- слушает только localhost;

- неправильный IP;

- firewall отправляет reject;

- container port не опубликован.


Проверить на server:

```bash
ss -lntp
systemctl status postgresql
systemctl status mysql
```

---

### Connection timeout

Timeout означает, что ответ на соединение не получен.

Причины:

- firewall drop;

- Security Group;

- NetworkPolicy;

- неправильный route;

- недоступная сеть;

- зависшая database node;

- неправильный IP.


Проверить:

```bash
nc -vz -w 5 db.example.com 5432
traceroute db.example.com
```

---

### Authentication failed

Проверить:

- username;

- password;

- database;

- allowed source host;

- authentication method;

- TLS requirements;

- secret version;

- password expiration.


PostgreSQL также проверить `pg_hba.conf`.

MySQL — account `'user'@'host'`.

---

### Too many connections

Симптом: база достигла `max_connections`.

Проверить:

- сколько connections;

- кто их создал;

- active или idle;

- connection pool;

- утечку connections;

- долгие queries.


Временные действия:

- завершить лишние idle connections;

- уменьшить traffic;

- перезапустить проблемное приложение.


Долгосрочно:

- настроить pool;

- уменьшить число app workers;

- исправить leak;

- использовать proxy/pooler;

- осторожно пересмотреть limit.


---

### База использует 100% CPU

Проверить:

- активные queries;

- top queries по total CPU/time;

- query plans;

- число connections;

- lock contention;

- autovacuum/maintenance;

- background jobs.


На host:

```bash
top
pidstat -p <PID> 1
```

Высокий CPU часто вызван несколькими тяжелыми запросами, а не самой DBMS «в целом».

---

### Медленные SQL-запросы

Порядок:

1. Найти конкретный query.

2. Посмотреть `EXPLAIN ANALYZE`.

3. Сравнить estimated и actual rows.

4. Проверить indexes.

5. Проверить locks.

6. Проверить disk I/O.

7. Проверить объем результата.

8. Проверить application N+1.


Не добавлять index вслепую без плана запроса.

---

### Запрос заблокирован

Нужно найти:

- waiting query;

- blocking transaction;

- lock type;

- длительность blocker;

- owner приложения.


Варианты:

- дождаться;

- завершить blocker;

- отменить query;

- исправить transaction design.


Перед убийством transaction нужно понимать бизнес-последствия rollback.

---

### Deadlock

DBMS обычно сама отменяет одну transaction.

Нужно:

- найти deadlock log;

- определить SQL и порядок locks;

- сократить transactions;

- блокировать строки в одинаковом порядке;

- добавить retry в приложение;

- проверить indexes, чтобы update не блокировал лишние строки.


---

### Replication Lag

Проверить:

- network;

- CPU и disk replica;

- heavy read queries;

- blocked replay;

- поток writes;

- replication status;

- WAL/binlog retention.


Временные меры:

- убрать тяжелые queries с replica;

- увеличить ресурсы;

- перенаправить критичные reads на Primary.


---

### Replica недоступна

Проверить:

- database process;

- network;

- disk;

- replication connection;

- credentials;

- logs;

- состояние cluster manager.


Если приложение использует replica для обязательного чтения, нужен fallback или HA proxy.

---

### На базе закончился диск

Сначала определить, что заняло место:

```bash
df -h
df -i
du -xh /var/lib | sort -h | tail
```

Возможные причины:

- data files;

- WAL/binlog;

- temporary files;

- logs;

- backups;

- replication slots;

- bloat.


Нельзя просто удалять database files или WAL вручную.

Нужно:

- остановить некритичные writes;

- освободить безопасное место;

- расширить disk;

- исправить retention;

- проверить replication и backup.


---

### Долгая транзакция

Долгая transaction может держать locks и старые row versions.

Проверить:

- начало transaction;

- выполняемый query;

- состояние `idle in transaction`;

- blocker;

- application owner.


Можно отменить query или завершить session, но это вызовет rollback.

---

### Migration заблокировала таблицу

Причины:

- `ALTER TABLE`;

- создание index без online/concurrent режима;

- изменение типа колонки;

- добавление constraint с полной проверкой;

- долгий update.


Действия:

- проверить locks;

- остановить migration при сильном impact;

- использовать online migration;

- делать изменения поэтапно;

- ставить lock timeout;

- тестировать на production-like объеме.


---

### После failover приложение подключается к старому Primary

Причины:

- DNS cache;

- connection pool держит старые connections;

- неправильный proxy endpoint;

- старый Primary не был fenced;

- статический IP в config;

- слишком большой DNS TTL.


Действия:

- проверить текущую Primary;

- закрыть старые connections;

- обновить discovery/DNS;

- перезапустить или refresh pool;

- изолировать старую Primary от writes.


---

### Backup не восстанавливается

Причины:

- backup поврежден;

- несовместимая версия;

- отсутствуют части incremental chain;

- нет WAL/binlog;

- неправильные permissions;

- недостаточно disk;

- backup создавался неконсистентно;

- отсутствуют encryption keys.


Поэтому restore нужно регулярно тестировать заранее, а не впервые во время аварии.

---

## 13. Interview Questions

### SQL vs NoSQL

SQL databases используют relational model, schema, joins и обычно сильные transaction guarantees.

NoSQL объединяет разные нереляционные модели и часто оптимизируется под конкретные workloads и масштабирование.

Выбор зависит от данных, consistency и query patterns.

---

### Primary Key vs Unique Key

Primary Key:

- основной идентификатор строки;

- один на таблицу;

- не допускает `NULL`.


Unique Key:

- обеспечивает уникальность;

- в таблице может быть несколько;

- поведение `NULL` зависит от DBMS.


---

### Что такое индекс

Индекс — дополнительная структура данных, ускоряющая поиск и сортировку.

Он занимает disk и замедляет writes, потому что его нужно обновлять вместе с таблицей.

---

### Почему индекс может не использоваться

Причины:

- запрос возвращает большую часть таблицы;

- таблица маленькая;

- условие не соответствует порядку composite index;

- функция применяется к indexed column;

- type cast;

- низкая селективность;

- устаревшая статистика;

- optimizer считает Sequential Scan дешевле.


---

### Что такое ACID

ACID:

- Atomicity;

- Consistency;

- Isolation;

- Durability.


Это базовые свойства надежных database transactions.

---

### Что такое Isolation Level

Isolation Level определяет, какие изменения параллельных транзакций видит текущая transaction и какие anomalies допустимы.

Чем выше isolation, тем сильнее гарантии, но возможны дополнительные locks, aborts и retries.

---

### Что такое Deadlock

Deadlock — циклическое ожидание locks между транзакциями.

DBMS отменяет одну transaction, после чего приложение должно корректно повторить операцию.

---

### Primary vs Replica

Primary принимает writes.

Replica получает изменения через replication и может обслуживать reads или использоваться для failover.

Replica может отставать.

---

### Что такое Replication Lag

Replication Lag — задержка между изменением на Primary и его применением на Replica.

Из-за lag replica может возвращать устаревшие данные.

---

### Sharding vs Partitioning

Partitioning делит таблицу на части внутри одной логической базы.

Sharding распределяет данные между независимыми database nodes.

Sharding сложнее для joins, transactions и rebalance.

---

### RPO vs RTO

RPO — сколько данных допустимо потерять.

RTO — сколько времени допустимо восстанавливать сервис.

```text
RPO -> потеря данных
RTO -> время простоя
```

---

### Как найти медленный запрос

Нужно:

- найти slow query в monitoring/logs;

- посмотреть частоту и total time;

- выполнить `EXPLAIN ANALYZE`;

- проверить indexes;

- проверить locks;

- проверить disk I/O;

- проверить объем результата и N+1.


---

### Что делать при `Too many connections`

Сначала:

- найти источник connections;

- проверить active/idle;

- завершить явно зависшие sessions;

- снизить traffic.


Дальше:

- настроить pooling;

- исправить connection leak;

- ограничить workers;

- использовать pooler/proxy;

- только после этого пересматривать `max_connections`.


---

### Что делать, если база заполнила диск

Нужно:

- определить источник роста;

- остановить опасные writes при необходимости;

- расширить disk;

- безопасно удалить ненужные logs/backups;

- проверить WAL/binlog и replication slots;

- проверить bloat и retention.


Нельзя вручную удалять файлы из database data directory без понимания формата хранения.