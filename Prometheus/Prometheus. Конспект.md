# Prometheus

## 1. Основы

### Что такое Prometheus

Prometheus — система мониторинга и сбора метрик.

Он собирает числовые метрики, хранит их как time series и позволяет делать запросы через PromQL.

---

### Для чего используется Prometheus

Prometheus используют для:

- мониторинга приложений;

- мониторинга серверов;

- мониторинга Kubernetes;

- alerting;

- анализа нагрузки;

- поиска bottlenecks.


---

### Pull model

Prometheus обычно сам забирает метрики с targets.

```text
Prometheus -> HTTP GET /metrics -> target
```

Это называется pull model.

---

### Time series database

Prometheus хранит данные как time series.

Time series — это metric name + labels + значения во времени.

Пример:

```text
http_requests_total{service="api", status="200"}
```

---

### Metrics endpoint `/metrics`

Приложение или exporter обычно отдает метрики на endpoint:

```text
/metrics
```

Prometheus периодически ходит туда и забирает данные.

---

### Prometheus architecture

Упрощенно:

```text
Targets / Exporters
        ↓
Prometheus Server
        ↓
PromQL / Rules / Alerts
        ↓
Grafana / Alertmanager
```

Prometheus собирает, хранит, запрашивает и отправляет alerts.

---

## 2. Компоненты

### Prometheus Server

Prometheus Server — основной компонент.

Он:

- scrape'ит targets;

- хранит metrics;

- выполняет PromQL;

- считает rules;

- отправляет alerts в Alertmanager.
-


---

### Exporters

Exporter — процесс, который собирает метрики из системы и отдает их в формате Prometheus.

Примеры:

- Node Exporter;

- Blackbox Exporter;

- postgres_exporter;

- nginx_exporter.


---

### Pushgateway

Pushgateway используется, когда target не может быть нормально scraped.

Например:

- короткоживущие batch jobs;

- cron jobs.


Но для обычных сервисов лучше использовать pull model напрямую.

---

### Alertmanager

Alertmanager принимает alerts от Prometheus.

Он отвечает за:

- grouping;

- routing;

- silencing;

- deduplication;

- отправку уведомлений.


Например в Slack, Telegram, email, PagerDuty.

---

### Service Discovery

Service Discovery позволяет Prometheus автоматически находить targets.

Например:

- Kubernetes Pods;

- Kubernetes Services;

- EC2 instances;

- Consul services.


Без service discovery targets пришлось бы прописывать руками.

---

### Targets

Targets — endpoints, с которых Prometheus собирает метрики.

Пример target:

```text
node-exporter:9100
app:8080/metrics
```

Посмотреть targets можно в Prometheus UI в разделе **Status → Targets**.

---

## 3. Типы метрик

### Counter

Counter — метрика, которая только растет.

Примеры:

- количество HTTP requests;

- количество ошибок;

- количество обработанных сообщений.


```text
http_requests_total
```

Для Counter обычно используют `rate()` или `increase()`.

---

### Gauge

Gauge — метрика, которая может расти и падать.

Примеры:

- memory usage;

- CPU temperature;

- количество active connections;

- размер очереди.


```text
process_resident_memory_bytes
```

---

### Histogram

Histogram измеряет распределение значений по buckets.

Часто используется для latency.

Пример:

```text
http_request_duration_seconds_bucket
```

По histogram можно считать p95/p99 через `histogram_quantile()`.

---

### Summary

Summary тоже измеряет распределение значений.

Может считать quantiles на стороне приложения.

В Prometheus чаще предпочитают Histogram, потому что Histogram проще агрегировать между instances.

---

### Когда использовать каждый тип

Counter — для событий, которые только увеличиваются.

Gauge — для текущего состояния.

Histogram — для latency/размеров и percentiles.

Summary — для quantiles внутри одного instance, если не нужна нормальная агрегация между instances.

---

## 4. Metrics и Labels

### Metric name

Metric name — имя метрики.

Пример:

```text
http_requests_total
node_cpu_seconds_total
process_resident_memory_bytes
```

Хорошее имя должно понятно описывать, что измеряется.

---

### Labels

Labels — дополнительные измерения метрики.

Пример:

```text
http_requests_total{method="GET", status="200", service="api"}
```

Labels позволяют фильтровать и группировать данные.

---

### Time series

Time series — уникальная комбинация metric name и labels.

Например:

```text
http_requests_total{status="200"}
http_requests_total{status="500"}
```

Это две разные time series.

---

### Cardinality

Cardinality — количество уникальных time series.

Например, если metric имеет labels `user_id`, `request_id`, `session_id`, cardinality может стать огромной.

---

### Почему высокая cardinality опасна

Высокая cardinality опасна, потому что Prometheus начинает использовать много:

- RAM;

- CPU;

- disk;

- времени на query.


Плохой label может быстро убить Prometheus.

---

### Хорошие и плохие labels

Хорошие labels:

- `service`;

- `method`;

- `status`;

- `instance`;

- `namespace`;

- `pod`.


Плохие labels:

- `user_id`;

- `request_id`;

- `email`;

- `session_id`;

- `timestamp`.


Главное правило: label не должен иметь бесконечно много уникальных значений.

---

## 5. Scraping

### Scrape interval

`scrape_interval` — как часто Prometheus собирает метрики.

Пример:

```yaml
global:
  scrape_interval: 15s
```

Чем меньше interval, тем больше нагрузка и больше данных.

---

### Scrape timeout

`scrape_timeout` — сколько Prometheus ждет ответ от target.

Пример:

```yaml
scrape_timeout: 10s
```

Если target не ответил за это время, scrape считается failed.

---

### Targets

Targets — endpoints для scrape.

Пример:

```text
localhost:9100
app:8080
```

Каждый target должен отдавать metrics endpoint.

---

### Jobs

Job — группа targets в Prometheus config.

Пример:

```yaml
scrape_configs:
  - job_name: "node"
    static_configs:
      - targets:
          - "node1:9100"
          - "node2:9100"
```

`job` часто означает тип сервиса или exporter.

---

### `prometheus.yml`

`prometheus.yml` — основной конфигурационный файл Prometheus.

В нем задают:

- scrape configs;

- alerting;

- rule files;

- global settings;

- service discovery.


---

### Static configuration

Static configuration — targets прописаны вручную.

```yaml
scrape_configs:
  - job_name: "app"
    static_configs:
      - targets:
          - "app1:8080"
          - "app2:8080"
```

Подходит для маленьких или статичных окружений.

---

### Service Discovery

Service Discovery автоматически находит targets.

В Kubernetes Prometheus может находить:

- Pods;

- Services;

- Endpoints;

- Nodes.


Это удобнее, чем вручную обновлять `prometheus.yml`.

---

### Проверка состояния target

Проверить target можно в Prometheus UI:

```text
Status -> Targets
```

Там видно:

- `UP`;

- `DOWN`;

- endpoint;

- last scrape;

- scrape error.


---

## 6. PromQL

### Что такое PromQL

PromQL — язык запросов Prometheus.

С его помощью можно:

- смотреть метрики;

- считать rate;

- агрегировать;

- строить alerts;

- создавать dashboards.


---

### Instant vector

Instant vector — набор time series в один момент времени.

Пример:

```promql
up
```

Возвращает текущее состояние targets.

---

### Range vector

Range vector — значения time series за промежуток времени.

Пример:

```promql
http_requests_total[5m]
```

Нужен для функций типа `rate()` и `increase()`.

---

### Scalar

Scalar — одно числовое значение.

Пример:

```promql
2
```

Может использоваться в арифметике с метриками.

---

### Label filtering

Фильтрация по labels:

```promql
http_requests_total{service="api", status="500"}
```

Можно использовать regex:

```promql
http_requests_total{status=~"5.."}
```

---

### Arithmetic operators

Арифметические операторы:

```promql
+
-
*
/
%
```

Пример:

```promql
errors_total / requests_total
```

---

### Comparison operators

Операторы сравнения:

```promql
>
<
>=
<=
==
!=
```

Пример:

```promql
up == 0
```

Так часто находят недоступные targets.

---

### Aggregation

Aggregation объединяет time series.

Например:

```promql
sum by (service) (rate(http_requests_total[5m]))
```

Можно группировать через `by` или исключать labels через `without`.

---

### `sum`

`sum` суммирует значения.

Пример:

```promql
sum(rate(http_requests_total[5m]))
```

Или по сервисам:

```promql
sum by (service) (rate(http_requests_total[5m]))
```

---

### `avg`

`avg` считает среднее.

Пример:

```promql
avg by (instance) (node_load1)
```

Полезно, но с latency лучше осторожно: average часто скрывает проблемы.

---

### `min`

`min` показывает минимальное значение.

Пример:

```promql
min by (node) (node_memory_MemAvailable_bytes)
```

---

### `max`

`max` показывает максимальное значение.

Пример:

```promql
max by (pod) (container_memory_usage_bytes)
```

Полезно для поиска самого тяжелого Pod/process.

---

### `count`

`count` считает количество time series.

Пример:

```promql
count(up)
```

Или количество targets по job:

```promql
count by (job) (up)
```

---

### `rate`

`rate()` считает среднюю скорость роста Counter за период.

Пример:

```promql
rate(http_requests_total[5m])
```

Используется для requests per second, errors per second и похожих метрик.

---

### `irate`

`irate()` считает скорость по последним двум точкам.

Пример:

```promql
irate(http_requests_total[1m])
```

Более резкий и шумный, чем `rate()`.

Для alerts обычно лучше `rate()`.

---

### `increase`

`increase()` считает, насколько Counter вырос за период.

Пример:

```promql
increase(http_requests_total[1h])
```

Это удобно, если нужно количество событий за час, день и т.д.

---

### `histogram_quantile`

`histogram_quantile()` считает percentile из histogram buckets.

Пример p95 latency:

```promql
histogram_quantile(
  0.95,
  sum by (le) (rate(http_request_duration_seconds_bucket[5m]))
)
```

Для группировки по service:

```promql
histogram_quantile(
  0.95,
  sum by (service, le) (rate(http_request_duration_seconds_bucket[5m]))
)
```

---

## 7. Exporters

### Что такое Exporter

Exporter — сервис, который отдает метрики в формате Prometheus.

Он может собирать метрики из:

- Linux host;

- базы данных;

- nginx;

- blackbox checks;

- Kubernetes API;

- приложений.


---

### Node Exporter

Node Exporter собирает Linux host metrics.

Например:

- CPU;

- memory;

- disk;

- filesystem;

- network;

- load average.


Обычно слушает порт:

```text
9100
```

---

### Blackbox Exporter

Blackbox Exporter проверяет сервисы снаружи.

Например:

- HTTP availability;

- TCP port;

- ICMP ping;

- TLS certificate.


Полезен для проверки “работает ли сервис глазами клиента”.

---

### kube-state-metrics

`kube-state-metrics` экспортирует состояние Kubernetes objects.

Например:

- Deployment replicas;

- Pod phases;

- Node conditions;

- PVC status;

- Job status.


Он не показывает usage CPU/memory, он показывает состояние объектов.

---

### cAdvisor

cAdvisor собирает container metrics.

В Kubernetes его метрики обычно доступны через kubelet.

Показывает:

- container CPU;

- memory;

- filesystem;

- network.


---

### Database exporters basics

Database exporters собирают метрики из баз данных.

Примеры:

- postgres_exporter;

- mysqld_exporter;

- redis_exporter;

- mongodb_exporter.


Они показывают connections, queries, locks, replication, slow operations и другое.

---

### Как Prometheus собирает метрики с exporter

Exporter поднимает HTTP endpoint `/metrics`.

Prometheus периодически делает scrape:

```text
Prometheus -> exporter:port/metrics
```

Exporter возвращает текстовые метрики в Prometheus format.

---

## 8. Alerting Rules

### Alerting rules

Alerting rules описывают условия для alerts.

Пример:

```yaml
groups:
  - name: app-alerts
    rules:
      - alert: HighErrorRate
        expr: rate(http_requests_total{status=~"5.."}[5m]) > 1
        for: 5m
```

---

### Recording rules

Recording rules заранее сохраняют результат PromQL-запроса как новую метрику.

Пример:

```yaml
- record: job:http_requests:rate5m
  expr: sum by (job) (rate(http_requests_total[5m]))
```

Полезно для тяжелых запросов и dashboards.

---

### `for`

`for` задает, как долго условие должно быть true перед alert.

```yaml
for: 5m
```

Это защищает от коротких spikes.

---

### Labels

Labels в alert rule добавляют metadata.

Пример:

```yaml
labels:
  severity: critical
  team: backend
```

Alertmanager использует labels для routing и grouping.

---

### Annotations

Annotations содержат описание alert.

Пример:

```yaml
annotations:
  summary: "High error rate"
  description: "Service {{ $labels.service }} has high 5xx rate"
```

Annotations помогают человеку понять проблему.

---

### Severity

Severity — уровень важности alert.

Обычно:

- `warning`;

- `critical`.


`critical` должен означать реальное влияние на production или пользователей.

---

### Передача алертов в Alertmanager

Prometheus отправляет firing alerts в Alertmanager.

В config:

```yaml
alerting:
  alertmanagers:
    - static_configs:
        - targets:
            - alertmanager:9093
```

Alertmanager дальше отправляет уведомления.

---

### Проверка правил через `promtool`

`promtool` проверяет syntax rules.

```bash
promtool check rules alerts.yml
promtool check config prometheus.yml
```

Полезно запускать в CI.

---

## 9. Storage

### Local TSDB

Prometheus хранит данные в локальной TSDB.

Обычно директория:

```text
/data
```

Local TSDB быстрая, но не идеальна для долгосрочного хранения и HA.

---

### Retention

Retention — сколько Prometheus хранит данные.

Пример:

```bash
--storage.tsdb.retention.time=15d
```

Можно ограничивать и по размеру:

```bash
--storage.tsdb.retention.size=100GB
```

---

### Blocks

Prometheus хранит данные блоками.

Blocks — это куски данных за определенный период времени.

Старые blocks могут compact'иться и удаляться по retention.

---

### WAL

WAL — Write-Ahead Log.

Prometheus сначала пишет новые samples в WAL, чтобы не потерять данные при crash.

После этого данные попадают в blocks.

---

### Compaction basics

Compaction объединяет маленькие blocks в более крупные.

Это нужно для:

- экономии места;

- ускорения чтения;

- оптимизации хранения.


---

### Почему Prometheus не является долгосрочным хранилищем

Prometheus из коробки хранит данные локально.

Проблемы:

- один Prometheus = один local disk;

- ограниченный retention;

- нет полноценного long-term HA storage;

- большой объем метрик быстро ест диск.


Для long-term storage используют Thanos, Mimir, Cortex, VictoriaMetrics.

---

### Remote Write basics

Remote Write отправляет метрики из Prometheus во внешнее хранилище.

Например:

- Thanos Receive;

- Mimir;

- Cortex;

- VictoriaMetrics;

- cloud monitoring backend.


Prometheus продолжает scrape, но данные дополнительно уходят наружу.

---

### Thanos / Cortex / Mimir basics

Thanos, Cortex и Mimir решают задачи:

- long-term storage;

- global query;

- HA Prometheus;

- масштабирование;

- multi-cluster monitoring.


Они часто используются поверх Prometheus в больших инфраструктурах.

---

## 10. Kubernetes Integration

### Prometheus в Kubernetes

В Kubernetes Prometheus обычно запускают как StatefulSet.

Он собирает метрики с:

- Pods;

- Services;

- Nodes;

- kubelet;

- exporters;

- Kubernetes API objects.


---

### Kubernetes Service Discovery

Prometheus может автоматически находить Kubernetes targets.

Например:

- Pods с annotations;

- Services;

- Endpoints;

- Nodes.


Это удобно, потому что Pods постоянно создаются и удаляются.

---

### ServiceMonitor

ServiceMonitor — custom resource от Prometheus Operator.

Он описывает, какие Services нужно scrape'ить.

Примерная идея:

```text
ServiceMonitor -> Service -> Pods /metrics
```

---

### PodMonitor

PodMonitor — custom resource для scrape напрямую с Pods.

Используется, когда не хочется или нельзя создавать отдельный Service.

---

### Prometheus Operator

Prometheus Operator управляет Prometheus в Kubernetes.

Он добавляет CRDs:

- Prometheus;

- Alertmanager;

- ServiceMonitor;

- PodMonitor;

- PrometheusRule.


Это упрощает настройку monitoring stack.

---

### kube-prometheus-stack

`kube-prometheus-stack` — Helm chart с готовым monitoring stack.

Обычно включает:

- Prometheus Operator;

- Prometheus;

- Alertmanager;

- Grafana;

- node-exporter;

- kube-state-metrics;

- dashboards;

- default alert rules.


---

### Monitoring Pods

Для Pods смотрят:

- restarts;

- status/phase;

- CPU/memory;

- OOMKilled;

- readiness;

- container metrics;

- logs/events рядом с метриками.


---

### Monitoring Nodes

Для Nodes смотрят:

- CPU;

- memory;

- disk;

- filesystem;

- network;

- load average;

- NodeReady;

- DiskPressure;

- MemoryPressure.


---

### Monitoring Deployments и StatefulSets

Для Deployments и StatefulSets смотрят:

- desired replicas;

- ready replicas;

- unavailable replicas;

- rollout status;

- restarts;

- pod health;

- resource usage.


---

## 11. Troubleshooting

### Target находится в состоянии Down

Если target Down, Prometheus не может его scrape'ить.

Проверить:

- target URL;

- порт;

- network;

- DNS;

- metrics path;

- TLS;

- firewall;

- Service/Endpoints в Kubernetes.


---

### Метрики не появляются

Причины:

- target не добавлен;

- target Down;

- неправильный metrics path;

- service discovery не нашел объект;

- labels не совпали;

- exporter не отдает нужную метрику.


Проверить Prometheus UI и `/targets`.

---

### Неправильный metrics path

По умолчанию Prometheus ходит на:

```text
/metrics
```

Если приложение отдает метрики по другому path, нужно указать:

```yaml
metrics_path: /custom-metrics
```

---

### Timeout при scrape

Timeout значит target не успел ответить.

Причины:

- приложение зависло;

- endpoint тяжелый;

- network latency;

- exporter перегружен;

- слишком маленький scrape timeout.


---

### Ошибка DNS

DNS-ошибка значит Prometheus не может резолвить target name.

Проверить:

- имя Service;

- namespace;

- CoreDNS;

- DNS search domain;

- правильность target address.


---

### Ошибка TLS

TLS-ошибка может быть из-за:

- self-signed certificate;

- expired certificate;

- wrong hostname;

- неправильный CA;

- target требует client certificate.


Иногда для проверки временно используют `insecure_skip_verify`, но в production лучше настроить CA нормально.

---

### Неправильные labels

Неправильные labels ломают dashboards и alerts.

Например, alert ожидает `service`, а метрика имеет `app`.

Нужно привести naming labels к единому стандарту.

---

### Высокая cardinality

Высокая cardinality часто появляется из-за labels:

- user_id;

- request_id;

- path с dynamic IDs;

- session_id;

- pod UID.


Решение — убрать такие labels или нормализовать значения.

---

### Prometheus использует много памяти

Причины:

- высокая cardinality;

- слишком много targets;

- слишком маленький scrape interval;

- тяжелые queries;

- много active series;

- слишком много labels.


Проверять нужно TSDB status и cardinality.

---

### Заканчивается место на диске

Причины:

- большой retention;

- много metrics;

- высокая cardinality;

- много targets;

- слишком частый scrape.


Решения:

- уменьшить retention;

- увеличить disk;

- убрать лишние metrics;

- снизить cardinality;

- настроить remote storage.


---

### PromQL-запрос возвращает неожиданный результат

Проверить:

- правильный metric name;

- labels;

- range window;

- `rate` применяется к Counter;

- aggregation by/without;

- нет ли counter reset;

- не смешиваются ли разные units.


---

## 12. Практика

### Запустить Prometheus

Минимально можно запустить через Docker:

```bash
docker run -p 9090:9090 prom/prometheus
```

После запуска UI будет доступен на:

```text
http://localhost:9090
```

---

### Подключить Node Exporter

Запустить Node Exporter:

```bash
docker run -p 9100:9100 prom/node-exporter
```

Добавить target в `prometheus.yml`:

```yaml
scrape_configs:
  - job_name: "node"
    static_configs:
      - targets: ["localhost:9100"]
```

---

### Добавить static target

Пример static target:

```yaml
scrape_configs:
  - job_name: "app"
    static_configs:
      - targets:
          - "app:8080"
```

Потом проверить target в Prometheus UI.

---

### Выполнить базовые PromQL-запросы

Примеры:

```promql
up
```

```promql
scrape_duration_seconds
```

```promql
rate(prometheus_http_requests_total[5m])
```

---

### Посчитать CPU usage

Пример для Node Exporter:

```promql
100 - (
  avg by (instance) (
    rate(node_cpu_seconds_total{mode="idle"}[5m])
  ) * 100
)
```

Это показывает CPU usage в процентах.

---

### Посчитать memory usage

Пример:

```promql
100 * (
  1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes
)
```

Показывает примерный процент использования памяти.

---

### Посчитать rate HTTP-запросов

Для Counter:

```promql
sum by (service) (
  rate(http_requests_total[5m])
)
```

Это requests per second по сервисам.

---

### Создать recording rule

Пример:

```yaml
groups:
  - name: app-recording
    rules:
      - record: service:http_requests:rate5m
        expr: sum by (service) (rate(http_requests_total[5m]))
```

Такой результат потом проще использовать в dashboards и alerts.

---

### Создать alert rule

Пример alert rule:

```yaml
groups:
  - name: app-alerts
    rules:
      - alert: ServiceDown
        expr: up == 0
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "Service is down"
```

---

### Подключить Prometheus к Grafana

В Grafana нужно добавить Data Source:

```text
Type: Prometheus
URL: http://prometheus:9090
```

После этого можно строить dashboards на PromQL.

---

### Развернуть kube-prometheus-stack

Обычно через Helm:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

helm install monitoring prometheus-community/kube-prometheus-stack -n monitoring --create-namespace
```

Это поднимет Prometheus, Grafana, Alertmanager и exporters.

---

### Найти и исправить target Down

Порядок:

- открыть Prometheus UI → Targets;

- посмотреть scrape error;

- проверить DNS/port/path;

- проверить Service/Endpoints;

- проверить logs target/exporter;

- исправить config или сеть.


---

## 13. Interview Questions

### Как работает Prometheus

Prometheus периодически scrape'ит targets по HTTP, забирает метрики, сохраняет их в TSDB и позволяет запрашивать через PromQL.

Для alerting он вычисляет alert rules и отправляет alerts в Alertmanager.

---

### Почему Prometheus использует Pull model

Pull model удобен, потому что Prometheus сам контролирует:

- когда scrape'ить;

- кого scrape'ить;

- какой target жив;

- сколько длится scrape.


Также легко видеть состояние targets в UI.

---

### Counter vs Gauge

Counter только растет.

Gauge может расти и падать.

```text
Counter -> requests_total
Gauge   -> memory_usage
```

---

### Histogram vs Summary

Histogram считает buckets и хорошо агрегируется между instances.

Summary считает quantiles на стороне приложения и хуже агрегируется.

Для Prometheus dashboards/alerts чаще используют Histogram.

---

### Что такое time series

Time series — это metric name + labels + значения во времени.

Пример:

```text
http_requests_total{service="api", status="200"}
```

---

### Что такое labels

Labels — key-value metadata у метрик.

Они нужны для фильтрации и группировки.

Пример:

```text
method="GET"
status="500"
service="api"
```

---

### Что такое cardinality

Cardinality — количество уникальных time series.

Высокая cardinality увеличивает нагрузку на Prometheus.

---

### `rate` vs `increase`

`rate()` показывает скорость роста Counter в секунду.

`increase()` показывает, насколько Counter вырос за период.

```promql
rate(http_requests_total[5m])
increase(http_requests_total[1h])
```

---

### Что такое exporter

Exporter — сервис, который собирает метрики из системы и отдает их в формате Prometheus на `/metrics`.

Например Node Exporter собирает Linux metrics.

---

### Что такое recording rule

Recording rule заранее вычисляет PromQL-запрос и сохраняет результат как новую метрику.

Это ускоряет dashboards и упрощает сложные выражения.

---

### Как Prometheus отправляет алерты

Prometheus вычисляет alerting rules.

Если условие выполняется достаточно долго, alert становится firing.

Потом Prometheus отправляет alert в Alertmanager.

---

### Почему target может быть Down

Причины:

- target недоступен по сети;

- неправильный port;

- неправильный metrics path;

- DNS не работает;

- TLS ошибка;

- exporter упал;

- Service/Endpoints пустые;

- firewall/NetworkPolicy блокирует.


---

### Как мониторить Kubernetes через Prometheus

Обычно ставят `kube-prometheus-stack`.

Он включает:

- Prometheus Operator;

- Prometheus;

- Grafana;

- Alertmanager;

- node-exporter;

- kube-state-metrics.


Для приложений используют ServiceMonitor или PodMonitor.