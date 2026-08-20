[[Prometheus. Конспект]]

## 1. Основы

- [ ] Что такое Prometheus
- [ ] Для чего используется Prometheus
- [ ] Pull model
- [ ] Time series database
- [ ] Metrics endpoint `/metrics`
- [ ] Prometheus architecture

---

## 2. Компоненты

- [ ] Prometheus Server
- [ ] Exporters
- [ ] Pushgateway
- [ ] Alertmanager
- [ ] Service Discovery
- [ ] Targets

---

## 3. Типы метрик

- [ ] Counter
- [ ] Gauge
- [ ] Histogram
- [ ] Summary
- [ ] Когда использовать каждый тип

---

## 4. Metrics и Labels

- [ ] Metric name
- [ ] Labels
- [ ] Time series
- [ ] Cardinality
- [ ] Почему высокая cardinality опасна
- [ ] Хорошие и плохие labels

---

## 5. Scraping

- [ ] Scrape interval
- [ ] Scrape timeout
- [ ] Targets
- [ ] Jobs
- [ ] `prometheus.yml`
- [ ] Static configuration
- [ ] Service Discovery
- [ ] Проверка состояния target

---

## 6. PromQL

- [ ] Что такое PromQL
- [ ] Instant vector
- [ ] Range vector
- [ ] Scalar
- [ ] Label filtering
- [ ] Arithmetic operators
- [ ] Comparison operators
- [ ] Aggregation
- [ ] `sum`
- [ ] `avg`
- [ ] `min`
- [ ] `max`
- [ ] `count`
- [ ] `rate`
- [ ] `irate`
- [ ] `increase`
- [ ] `histogram_quantile`

---

## 7. Exporters

- [ ] Что такое Exporter
- [ ] Node Exporter
- [ ] Blackbox Exporter
- [ ] kube-state-metrics
- [ ] cAdvisor
- [ ] Database exporters basics
- [ ] Как Prometheus собирает метрики с exporter

---

## 8. Alerting Rules

- [ ] Alerting rules
- [ ] Recording rules
- [ ] `for`
- [ ] Labels
- [ ] Annotations
- [ ] Severity
- [ ] Передача алертов в Alertmanager
- [ ] Проверка правил через `promtool`

---

## 9. Storage

- [ ] Local TSDB
- [ ] Retention
- [ ] Blocks
- [ ] WAL
- [ ] Compaction basics
- [ ] Почему Prometheus не является долгосрочным хранилищем
- [ ] Remote Write basics
- [ ] Thanos / Cortex / Mimir basics

---

## 10. Kubernetes Integration

- [ ] Prometheus в Kubernetes
- [ ] Kubernetes Service Discovery
- [ ] ServiceMonitor
- [ ] PodMonitor
- [ ] Prometheus Operator
- [ ] kube-prometheus-stack
- [ ] Monitoring Pods
- [ ] Monitoring Nodes
- [ ] Monitoring Deployments и StatefulSets

---

## 11. Troubleshooting

- [ ] Target находится в состоянии Down
- [ ] Метрики не появляются
- [ ] Неправильный metrics path
- [ ] Timeout при scrape
- [ ] Ошибка DNS
- [ ] Ошибка TLS
- [ ] Неправильные labels
- [ ] Высокая cardinality
- [ ] Prometheus использует много памяти
- [ ] Заканчивается место на диске
- [ ] PromQL-запрос возвращает неожиданный результат

---

## 12. Практика

- [ ] Запустить Prometheus
- [ ] Подключить Node Exporter
- [ ] Добавить static target
- [ ] Выполнить базовые PromQL-запросы
- [ ] Посчитать CPU usage
- [ ] Посчитать memory usage
- [ ] Посчитать rate HTTP-запросов
- [ ] Создать recording rule
- [ ] Создать alert rule
- [ ] Подключить Prometheus к Grafana
- [ ] Развернуть kube-prometheus-stack
- [ ] Найти и исправить target Down

---

## 13. Interview Questions

- [ ] Как работает Prometheus
- [ ] Почему Prometheus использует Pull model
- [ ] Counter vs Gauge
- [ ] Histogram vs Summary
- [ ] Что такое time series
- [ ] Что такое labels
- [ ] Что такое cardinality
- [ ] `rate` vs `increase`
- [ ] Что такое exporter
- [ ] Что такое recording rule
- [ ] Как Prometheus отправляет алерты
- [ ] Почему target может быть Down
- [ ] Как мониторить Kubernetes через Prometheus