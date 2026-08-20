[[Logging(ELK + EFK + Loki). Конспект]]

## 1. Основы логирования

- [ ] Что такое лог
- [ ] Зачем нужны логи
- [ ] Application logs
- [ ] System logs
- [ ] Access logs
- [ ] Audit logs
- [ ] Structured vs Unstructured logs
- [ ] JSON logs
- [ ] Log levels
- [ ] DEBUG
- [ ] INFO
- [ ] WARN
- [ ] ERROR
- [ ] FATAL

---

## 2. Централизованное логирование

- [ ] Зачем собирать логи централизованно
- [ ] Log collection
- [ ] Log aggregation
- [ ] Log storage
- [ ] Log parsing
- [ ] Log indexing
- [ ] Log search
- [ ] Log visualization
- [ ] Log retention

---

## 3. Logging Pipeline

- [ ] Источник логов
- [ ] Log agent
- [ ] Parsing и enrichment
- [ ] Buffering
- [ ] Storage
- [ ] Visualization
- [ ] Alerting по логам

---

## 4. Логи в Linux

- [ ] `stdout` и `stderr`
- [ ] `journald`
- [ ] `journalctl`
- [ ] Syslog
- [ ] `/var/log`
- [ ] Log rotation
- [ ] `logrotate`

---

## 5. Логи контейнеров

- [ ] Docker logs
- [ ] `docker logs`
- [ ] Logging drivers
- [ ] Логи в `stdout` и `stderr`
- [ ] Ограничение размера логов
- [ ] Ротация логов контейнеров

---

## 6. Логи Kubernetes

- [ ] `kubectl logs`
- [ ] Логи конкретного контейнера
- [ ] Логи предыдущего контейнера
- [ ] Логи нескольких реплик
- [ ] Логи Node
- [ ] Логи Control Plane
- [ ] Kubernetes audit logs
- [ ] DaemonSet для сбора логов
- [ ] Почему логи нельзя хранить только внутри Pod

---

## 7. ELK / EFK Stack

- [ ] Что такое ELK
- [ ] Что такое EFK
- [ ] Elasticsearch
- [ ] Logstash
- [ ] Fluentd
- [ ] Fluent Bit
- [ ] Kibana
- [ ] ELK vs EFK

---

## 8. Elasticsearch

- [ ] Cluster
- [ ] Node
- [ ] Index
- [ ] Document
- [ ] Field
- [ ] Mapping
- [ ] Shard
- [ ] Replica
- [ ] Inverted index
- [ ] Query basics
- [ ] Index lifecycle management
- [ ] Retention
- [ ] Почему высокая cardinality опасна
- [ ] Почему Elasticsearch требует много ресурсов

---

## 9. Logstash / Fluentd / Fluent Bit

- [ ] Log collector
- [ ] Input
- [ ] Filter
- [ ] Output
- [ ] Parsing логов
- [ ] Regex и Grok basics
- [ ] Добавление labels и metadata
- [ ] Buffering
- [ ] Fluentd vs Fluent Bit
- [ ] Logstash vs Fluent Bit

---

## 10. Kibana

- [ ] Data View
- [ ] Discover
- [ ] Search
- [ ] Filters
- [ ] Dashboards
- [ ] Visualizations
- [ ] KQL basics
- [ ] Alerting basics

---

## 11. Loki

- [ ] Что такое Loki
- [ ] Loki architecture
- [ ] Log streams
- [ ] Labels
- [ ] Chunks
- [ ] Index
- [ ] Loki vs Elasticsearch
- [ ] Почему Loki не индексирует полный текст логов
- [ ] Cardinality labels
- [ ] Хорошие и плохие labels

---

## 12. Компоненты Loki

- [ ] Loki
- [ ] Promtail
- [ ] Grafana Alloy basics
- [ ] Distributor
- [ ] Ingester
- [ ] Querier
- [ ] Query frontend
- [ ] Object Storage
- [ ] Single binary mode
- [ ] Distributed mode

---

## 13. LogQL

- [ ] Что такое LogQL
- [ ] Log stream selector
- [ ] Label filters
- [ ] Line filters
- [ ] `|=`
- [ ] `!=`
- [ ] Regex filters
- [ ] JSON parser
- [ ] Logfmt parser
- [ ] `rate`
- [ ] `count_over_time`
- [ ] Метрики из логов

---

## 14. Grafana + Loki

- [ ] Loki как Data Source
- [ ] Просмотр логов в Explore
- [ ] Logs panel
- [ ] Фильтрация по namespace
- [ ] Фильтрация по Pod
- [ ] Связь логов и метрик
- [ ] Алерты на основе логов

---

## 15. Logs, Metrics и Traces

- [ ] Logs vs Metrics
- [ ] Logs vs Traces
- [ ] Correlation ID
- [ ] Trace ID
- [ ] Связь логов с запросом пользователя
- [ ] Переход от метрики к логам
- [ ] Переход от логов к trace

---

## 16. Security

- [ ] Маскирование паролей
- [ ] Маскирование токенов
- [ ] Маскирование персональных данных
- [ ] Доступ к логам
- [ ] Audit logs
- [ ] Шифрование логов
- [ ] Retention policy
- [ ] Почему нельзя логировать secrets

---

## 17. Troubleshooting

- [ ] Логи не собираются
- [ ] Логи не появляются в хранилище
- [ ] Ошибка парсинга
- [ ] Неправильный timestamp
- [ ] Потеря логов
- [ ] Дублирование логов
- [ ] Log agent использует много CPU
- [ ] Log agent использует много памяти
- [ ] Elasticsearch cluster red/yellow
- [ ] Заканчивается место на диске
- [ ] Слишком много shards
- [ ] Loki query работает медленно
- [ ] Высокая cardinality labels
- [ ] Слишком большой объём логов

---

## 18. Практика

- [ ] Посмотреть системные логи через `journalctl`
- [ ] Настроить `logrotate`
- [ ] Посмотреть логи Docker-контейнера
- [ ] Посмотреть логи Kubernetes Pod
- [ ] Развернуть EFK stack
- [ ] Собрать Kubernetes-логи через Fluent Bit
- [ ] Найти ошибку в Kibana
- [ ] Развернуть Loki
- [ ] Подключить Loki к Grafana
- [ ] Выполнить базовые LogQL-запросы
- [ ] Создать alert на основе логов
- [ ] Найти ошибку по correlation ID

---

## 19. Interview Questions

- [ ] Зачем нужно централизованное логирование
- [ ] ELK vs EFK
- [ ] Elasticsearch vs Loki
- [ ] Fluentd vs Fluent Bit
- [ ] Что такое structured logging
- [ ] Зачем писать логи в `stdout`
- [ ] Как собираются логи в Kubernetes
- [ ] Почему нельзя хранить логи только внутри Pod
- [ ] Что такое shard и replica
- [ ] Как Loki хранит логи
- [ ] Что такое LogQL
- [ ] Почему высокая cardinality опасна
- [ ] Как найти ошибку конкретного запроса
- [ ] Что нельзя записывать в логи