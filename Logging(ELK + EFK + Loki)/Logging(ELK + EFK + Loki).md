[[Logging(ELK + EFK + Loki). Конспект]]

## 1. Основы логирования

- [x] Что такое лог
- [x] Зачем нужны логи
- [x] Application logs
- [x] System logs
- [x] Access logs
- [x] Audit logs
- [x] Structured vs Unstructured logs
- [x] JSON logs
- [x] Log levels
- [x] DEBUG
- [x] INFO
- [x] WARN
- [x] ERROR
- [x] FATAL

---

## 2. Централизованное логирование

- [x] Зачем собирать логи централизованно
- [x] Log collection
- [x] Log aggregation
- [x] Log storage
- [x] Log parsing
- [x] Log indexing
- [x] Log search
- [x] Log visualization
- [x] Log retention

---

## 3. Logging Pipeline

- [x] Источник логов
- [x] Log agent
- [x] Parsing и enrichment
- [x] Buffering
- [x] Storage
- [x] Visualization
- [x] Alerting по логам

---

## 4. Логи в Linux

- [x] `stdout` и `stderr`
- [x] `journald`
- [x] `journalctl`
- [x] Syslog
- [x] `/var/log`
- [x] Log rotation
- [x] `logrotate`

---

## 5. Логи контейнеров

- [x] Docker logs
- [x] `docker logs`
- [x] Logging drivers
- [x] Логи в `stdout` и `stderr`
- [x] Ограничение размера логов
- [x] Ротация логов контейнеров

---

## 6. Логи Kubernetes

- [x] `kubectl logs`
- [x] Логи конкретного контейнера
- [x] Логи предыдущего контейнера
- [x] Логи нескольких реплик
- [x] Логи Node
- [x] Логи Control Plane
- [x] Kubernetes audit logs
- [x] DaemonSet для сбора логов
- [x] Почему логи нельзя хранить только внутри Pod

---

## 7. ELK / EFK Stack

- [x] Что такое ELK
- [x] Что такое EFK
- [x] Elasticsearch
- [x] Logstash
- [x] Fluentd
- [x] Fluent Bit
- [x] Kibana
- [x] ELK vs EFK

---

## 8. Elasticsearch

- [x] Cluster
- [x] Node
- [x] Index
- [x] Document
- [x] Field
- [x] Mapping
- [x] Shard
- [x] Replica
- [x] Inverted index
- [x] Query basics
- [x] Index lifecycle management
- [x] Retention
- [x] Почему высокая cardinality опасна
- [x] Почему Elasticsearch требует много ресурсов

---

## 9. Logstash / Fluentd / Fluent Bit

- [x] Log collector
- [x] Input
- [x] Filter
- [x] Output
- [x] Parsing логов
- [x] Regex и Grok basics
- [x] Добавление labels и metadata
- [x] Buffering
- [x] Fluentd vs Fluent Bit
- [x] Logstash vs Fluent Bit

---

## 10. Kibana

- [x] Data View
- [x] Discover
- [x] Search
- [x] Filters
- [x] Dashboards
- [x] Visualizations
- [x] KQL basics
- [x] Alerting basics

---

## 11. Loki

- [x] Что такое Loki
- [x] Loki architecture
- [x] Log streams
- [x] Labels
- [x] Chunks
- [x] Index
- [x] Loki vs Elasticsearch
- [x] Почему Loki не индексирует полный текст логов
- [x] Cardinality labels
- [x] Хорошие и плохие labels

---

## 12. Компоненты Loki

- [x] Loki
- [x] Promtail
- [x] Grafana Alloy basics
- [x] Distributor
- [x] Ingester
- [x] Querier
- [x] Query frontend
- [x] Object Storage
- [x] Single binary mode
- [x] Distributed mode

---

## 13. LogQL

- [x] Что такое LogQL
- [x] Log stream selector
- [x] Label filters
- [x] Line filters
- [x] `|=`
- [x] `!=`
- [x] Regex filters
- [x] JSON parser
- [x] Logfmt parser
- [x] `rate`
- [x] `count_over_time`
- [x] Метрики из логов

---

## 14. Grafana + Loki

- [x] Loki как Data Source
- [x] Просмотр логов в Explore
- [x] Logs panel
- [x] Фильтрация по namespace
- [x] Фильтрация по Pod
- [x] Связь логов и метрик
- [x] Алерты на основе логов

---

## 15. Logs, Metrics и Traces

- [x] Logs vs Metrics
- [x] Logs vs Traces
- [x] Correlation ID
- [x] Trace ID
- [x] Связь логов с запросом пользователя
- [x] Переход от метрики к логам
- [x] Переход от логов к trace

---

## 16. Security

- [x] Маскирование паролей
- [x] Маскирование токенов
- [x] Маскирование персональных данных
- [x] Доступ к логам
- [x] Audit logs
- [x] Шифрование логов
- [x] Retention policy
- [x] Почему нельзя логировать secrets

---

## 17. Troubleshooting

- [x] Логи не собираются
- [x] Логи не появляются в хранилище
- [x] Ошибка парсинга
- [x] Неправильный timestamp
- [x] Потеря логов
- [x] Дублирование логов
- [x] Log agent использует много CPU
- [x] Log agent использует много памяти
- [x] Elasticsearch cluster red/yellow
- [x] Заканчивается место на диске
- [x] Слишком много shards
- [x] Loki query работает медленно
- [x] Высокая cardinality labels
- [x] Слишком большой объём логов

---

## 18. Практика

- [x] Посмотреть системные логи через `journalctl`
- [x] Настроить `logrotate`
- [x] Посмотреть логи Docker-контейнера
- [x] Посмотреть логи Kubernetes Pod
- [x] Развернуть EFK stack
- [x] Собрать Kubernetes-логи через Fluent Bit
- [x] Найти ошибку в Kibana
- [x] Развернуть Loki
- [x] Подключить Loki к Grafana
- [x] Выполнить базовые LogQL-запросы
- [x] Создать alert на основе логов
- [x] Найти ошибку по correlation ID

---

## 19. Interview Questions

- [x] Зачем нужно централизованное логирование
- [x] ELK vs EFK
- [x] Elasticsearch vs Loki
- [x] Fluentd vs Fluent Bit
- [x] Что такое structured logging
- [x] Зачем писать логи в `stdout`
- [x] Как собираются логи в Kubernetes
- [x] Почему нельзя хранить логи только внутри Pod
- [x] Что такое shard и replica
- [x] Как Loki хранит логи
- [x] Что такое LogQL
- [x] Почему высокая cardinality опасна
- [x] Как найти ошибку конкретного запроса
- [x] Что нельзя записывать в логи