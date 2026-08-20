[[DB. Конспект]]

## 1. Основы

- [ ] Что такое база данных
- [ ] DBMS
- [ ] SQL vs NoSQL
- [ ] Relational Database
- [ ] Table, Row, Column
- [ ] Schema
- [ ] Primary Key
- [ ] Foreign Key
- [ ] Constraints
- [ ] UNIQUE
- [ ] NOT NULL
- [ ] CHECK

---

## 2. SQL Basics

- [ ] SELECT
- [ ] INSERT
- [ ] UPDATE
- [ ] DELETE
- [ ] WHERE
- [ ] ORDER BY
- [ ] GROUP BY
- [ ] HAVING
- [ ] JOIN
- [ ] Subquery
- [ ] Aggregate Functions

---

## 3. Индексы

- [ ] Что такое индекс
- [ ] B-tree Index
- [ ] Composite Index
- [ ] Unique Index
- [ ] Index Scan vs Sequential Scan
- [ ] Когда индекс ускоряет запрос
- [ ] Когда индекс замедляет запись
- [ ] Порядок колонок в индексе
- [ ] EXPLAIN
- [ ] EXPLAIN ANALYZE

---

## 4. Транзакции

- [ ] ACID
- [ ] BEGIN
- [ ] COMMIT
- [ ] ROLLBACK
- [ ] Isolation Levels
- [ ] Read Uncommitted
- [ ] Read Committed
- [ ] Repeatable Read
- [ ] Serializable
- [ ] Dirty Read
- [ ] Non-repeatable Read
- [ ] Phantom Read
- [ ] Locks
- [ ] Deadlock
- [ ] Optimistic vs Pessimistic Locking

---

## 5. Производительность

- [ ] Slow Queries
- [ ] Query Plan
- [ ] Missing Index
- [ ] Connection Pool
- [ ] Maximum Connections
- [ ] Long-running Transactions
- [ ] Lock Contention
- [ ] CPU Bottleneck
- [ ] Disk I/O Bottleneck
- [ ] Cache Hit Ratio
- [ ] N+1 Query Problem

---

## 6. Репликация и High Availability

- [ ] Primary и Replica
- [ ] Synchronous Replication
- [ ] Asynchronous Replication
- [ ] Replication Lag
- [ ] Read Replica
- [ ] Failover
- [ ] Split Brain
- [ ] Quorum basics
- [ ] Database Proxy

---

## 7. Backup и Recovery

- [ ] Full Backup
- [ ] Incremental Backup
- [ ] Logical Backup
- [ ] Physical Backup
- [ ] Point-in-Time Recovery
- [ ] WAL
- [ ] Transaction Log
- [ ] RPO
- [ ] RTO
- [ ] Проверка восстановления backup
- [ ] Backup Retention

---

## 8. Масштабирование

- [ ] Vertical Scaling
- [ ] Horizontal Scaling
- [ ] Read Replicas
- [ ] Partitioning
- [ ] Sharding
- [ ] Connection Pooling
- [ ] Caching
- [ ] Database Proxy

---

## 9. PostgreSQL и MySQL

- [ ] Архитектура PostgreSQL
- [ ] Архитектура MySQL
- [ ] Processes и Connections
- [ ] PostgreSQL WAL
- [ ] MySQL Binlog
- [ ] Vacuum basics
- [ ] Autovacuum
- [ ] Table Bloat
- [ ] Slow Query Log
- [ ] System Tables

---

## 10. Security

- [ ] Users и Roles
- [ ] GRANT и Permissions
- [ ] Least Privilege
- [ ] TLS для подключения
- [ ] Encryption at Rest
- [ ] Secrets для подключения
- [ ] Audit Logs
- [ ] SQL Injection basics
- [ ] Ротация credentials

---

## 11. Monitoring

- [ ] Database Availability
- [ ] Query Latency
- [ ] Connections
- [ ] Active Queries
- [ ] Transactions
- [ ] Locks
- [ ] Deadlocks
- [ ] Replication Lag
- [ ] Disk Usage
- [ ] Database Size
- [ ] Cache Hit Ratio
- [ ] Slow Queries

---

## 12. Troubleshooting

- [ ] Приложение не подключается к базе
- [ ] Connection Refused
- [ ] Connection Timeout
- [ ] Authentication Failed
- [ ] Too Many Connections
- [ ] База использует 100% CPU
- [ ] Медленные SQL-запросы
- [ ] Запрос заблокирован
- [ ] Deadlock
- [ ] Высокий Replication Lag
- [ ] Replica недоступна
- [ ] На базе закончился диск
- [ ] Долгая транзакция
- [ ] Migration заблокировала таблицу
- [ ] После failover приложение подключается к старому Primary
- [ ] Backup не восстанавливается

---

## 13. Interview Questions

- [ ] SQL vs NoSQL
- [ ] Primary Key vs Unique Key
- [ ] Что такое индекс
- [ ] Почему индекс может не использоваться
- [ ] Что такое ACID
- [ ] Что такое Isolation Level
- [ ] Что такое Deadlock
- [ ] Primary vs Replica
- [ ] Что такое Replication Lag
- [ ] Sharding vs Partitioning
- [ ] RPO vs RTO
- [ ] Как найти медленный запрос
- [ ] Что делать при Too Many Connections
- [ ] Что делать, если база заполнила диск