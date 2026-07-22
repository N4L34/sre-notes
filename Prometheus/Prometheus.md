[[Prometheus. Конспект]]

## 1. Основы

- [x] Что такое Prometheus
- [x] Для чего используется Prometheus
- [x] Pull model
- [x] Time series database
- [x] Metrics endpoint `/metrics`
- [x] Prometheus architecture

---

## 2. Компоненты

- [x] Prometheus Server
- [x] Exporters
- [x] Pushgateway
- [x] Alertmanager
- [x] Service Discovery
- [x] Targets

---

## 3. Типы метрик

- [x] Counter
- [x] Gauge
- [x] Histogram
- [x] Summary
- [x] Когда использовать каждый тип

---

## 4. Metrics и Labels

- [x] Metric name
- [x] Labels
- [x] Time series
- [x] Cardinality
- [x] Почему высокая cardinality опасна
- [x] Хорошие и плохие labels

---

## 5. Scraping

- [x] Scrape interval
- [x] Scrape timeout
- [x] Targets
- [x] Jobs
- [x] `prometheus.yml`
- [x] Static configuration
- [x] Service Discovery
- [x] Проверка состояния target

---

## 6. PromQL

- [x] Что такое PromQL
- [x] Instant vector
- [x] Range vector
- [x] Scalar
- [x] Label filtering
- [x] Arithmetic operators
- [x] Comparison operators
- [x] Aggregation
- [x] `sum`
- [x] `avg`
- [x] `min`
- [x] `max`
- [x] `count`
- [x] `rate`
- [x] `irate`
- [x] `increase`
- [x] `histogram_quantile`

---

## 7. Exporters

- [x] Что такое Exporter
- [x] Node Exporter
- [x] Blackbox Exporter
- [x] kube-state-metrics
- [x] cAdvisor
- [x] Database exporters basics
- [x] Как Prometheus собирает метрики с exporter

---

## 8. Alerting Rules

- [x] Alerting rules
- [x] Recording rules
- [x] `for`
- [x] Labels
- [x] Annotations
- [x] Severity
- [x] Передача алертов в Alertmanager
- [x] Проверка правил через `promtool`

---

## 9. Storage

- [x] Local TSDB
- [x] Retention
- [x] Blocks
- [x] WAL
- [x] Compaction basics
- [x] Почему Prometheus не является долгосрочным хранилищем
- [x] Remote Write basics
- [x] Thanos / Cortex / Mimir basics

---

## 10. Kubernetes Integration

- [x] Prometheus в Kubernetes
- [x] Kubernetes Service Discovery
- [x] ServiceMonitor
- [x] PodMonitor
- [x] Prometheus Operator
- [x] kube-prometheus-stack
- [x] Monitoring Pods
- [x] Monitoring Nodes
- [x] Monitoring Deployments и StatefulSets

---

## 11. Troubleshooting

- [x] Target находится в состоянии Down
- [x] Метрики не появляются
- [x] Неправильный metrics path
- [x] Timeout при scrape
- [x] Ошибка DNS
- [x] Ошибка TLS
- [x] Неправильные labels
- [x] Высокая cardinality
- [x] Prometheus использует много памяти
- [x] Заканчивается место на диске
- [x] PromQL-запрос возвращает неожиданный результат

---

## 12. Практика

- [x] Запустить Prometheus
- [x] Подключить Node Exporter
- [x] Добавить static target
- [x] Выполнить базовые PromQL-запросы
- [x] Посчитать CPU usage
- [x] Посчитать memory usage
- [x] Посчитать rate HTTP-запросов
- [x] Создать recording rule
- [x] Создать alert rule
- [x] Подключить Prometheus к Grafana
- [x] Развернуть kube-prometheus-stack
- [x] Найти и исправить target Down

---

## 13. Interview Questions

- [x] Как работает Prometheus
- [x] Почему Prometheus использует Pull model
- [x] Counter vs Gauge
- [x] Histogram vs Summary
- [x] Что такое time series
- [x] Что такое labels
- [x] Что такое cardinality
- [x] `rate` vs `increase`
- [x] Что такое exporter
- [x] Что такое recording rule
- [x] Как Prometheus отправляет алерты
- [x] Почему target может быть Down
- [x] Как мониторить Kubernetes через Prometheus