## 1. Основы

### Что такое Grafana

Grafana — инструмент для визуализации observability-данных.

Она показывает:

- metrics;
    
- logs;
    
- traces;
    
- alerts;
    
- dashboards.
    

Grafana сама обычно не собирает метрики, а читает их из Data Sources.

---

### Для чего используется Grafana

Grafana используют для:

- dashboards;
    
- графиков;
    
- анализа метрик;
    
- просмотра логов;
    
- alerting;
    
- troubleshooting;
    
- мониторинга Kubernetes, серверов и приложений.
    

---

### Grafana в стеке Observability

Типичный стек:

```text
Prometheus -> metrics -> Grafana
Loki       -> logs    -> Grafana
Tempo      -> traces  -> Grafana
```

Grafana — это интерфейс для просмотра и анализа данных.

---

### Dashboard

Dashboard — набор панелей на одной странице.

Например:

- CPU;
    
- memory;
    
- latency;
    
- error rate;
    
- requests per second;
    
- pod restarts.
    

Dashboard помогает быстро понять состояние системы.

---

### Visualization

Visualization — способ отображения данных.

Например:

- graph;
    
- table;
    
- stat;
    
- gauge;
    
- heatmap;
    
- logs view.
    

Одна и та же метрика может отображаться разными способами.

---

### Panel

Panel — отдельный блок внутри dashboard.

Например:

- график CPU;
    
- таблица Pod'ов;
    
- stat с error rate;
    
- logs panel.
    

Каждая panel обычно содержит query к Data Source.

---

### Data Source

Data Source — источник данных для Grafana.

Примеры:

- Prometheus;
    
- Loki;
    
- Elasticsearch;
    
- InfluxDB;
    
- CloudWatch;
    
- Azure Monitor;
    
- Google Cloud Monitoring.
    

---

## 2. Architecture

### Grafana Server

Grafana Server — backend-сервис Grafana.

Он:

- обслуживает UI;
    
- хранит dashboards/settings;
    
- подключается к data sources;
    
- выполняет alert rules;
    
- управляет users/permissions.
    

---

### Backend

Backend Grafana отвечает за:

- API;
    
- authentication;
    
- работу с database;
    
- plugins;
    
- alerting;
    
- запросы к data sources.
    

---

### Frontend

Frontend — web UI Grafana.

Через него пользователь:

- открывает dashboards;
    
- редактирует panels;
    
- пишет queries;
    
- смотрит alerts;
    
- настраивает data sources.
    

---

### Database

Grafana использует database для хранения своих данных.

Там лежат:

- dashboards;
    
- users;
    
- teams;
    
- folders;
    
- data source configs;
    
- alert rules.
    

По умолчанию может быть SQLite, но для production лучше PostgreSQL/MySQL.

---

### Plugins

Plugins расширяют Grafana.

Бывают:

- data source plugins;
    
- panel plugins;
    
- app plugins.
    

Например, plugin для конкретного cloud monitoring или нестандартной visualization.

---

### Provisioning

Provisioning — настройка Grafana через файлы.

Можно описывать как код:

- data sources;
    
- dashboards;
    
- alerting;
    
- folders.
    

Это удобно для Kubernetes, Helm и GitOps.

---

## 3. Data Sources

### Что такое Data Source

Data Source — система, откуда Grafana берет данные.

Grafana отправляет query в Data Source и отображает результат на panel.

```text
Grafana Panel -> Query -> Data Source -> Data -> Visualization
```

---

### Prometheus

Prometheus — самый частый Data Source для metrics.

Grafana выполняет PromQL-запросы и строит графики.

Пример query:

```promql
rate(http_requests_total[5m])
```

---

### Loki

Loki — Data Source для logs.

Grafana позволяет искать и фильтровать logs через LogQL.

Пример:

```logql
{app="api"} |= "error"
```

---

### Elasticsearch

Elasticsearch часто используют как Data Source для logs и search.

Grafana может строить dashboards по данным из Elasticsearch.

Например:

- application logs;
    
- nginx logs;
    
- audit logs.
    

---

### InfluxDB

InfluxDB — time series database.

Grafana может читать из нее metrics и строить dashboards.

Часто встречается в старых monitoring setup'ах или IoT.

---

### Cloud monitoring services

Grafana может подключаться к cloud monitoring.

Примеры:

- AWS CloudWatch;
    
- Google Cloud Monitoring;
    
- Azure Monitor.
    

Это удобно, если инфраструктура уже мониторится cloud provider'ом.

---

### Подключение Data Source

Обычно нужно указать:

- type;
    
- URL;
    
- credentials;
    
- auth method;
    
- default settings.
    

Для Prometheus пример URL:

```text
http://prometheus:9090
```

---

## 4. Dashboards

### Dashboard structure

Dashboard состоит из:

- panels;
    
- rows;
    
- variables;
    
- time range;
    
- annotations;
    
- settings.
    

Обычно один dashboard делают под конкретную систему или сервис.

---

### Panels

Panels — основные элементы dashboard.

Каждая panel обычно имеет:

- query;
    
- visualization type;
    
- title;
    
- legend;
    
- thresholds;
    
- units.
    

---

### Rows

Rows группируют panels внутри dashboard.

Например:

- Overview;
    
- CPU/Memory;
    
- Network;
    
- Errors;
    
- Latency.
    

Это помогает держать dashboard читаемым.

---

### Variables

Variables позволяют делать dashboard динамическим.

Например:

- выбрать namespace;
    
- выбрать pod;
    
- выбрать service;
    
- выбрать instance.
    

Так один dashboard можно использовать для разных сервисов.

---

### Templates

Templates в Grafana — это dashboards с variables.

Например, dashboard для Kubernetes может иметь переменную `$namespace`.

```promql
sum(rate(container_cpu_usage_seconds_total{namespace="$namespace"}[5m]))
```

---

### Dashboard import/export

Dashboard можно импортировать и экспортировать.

Обычно export — это JSON.

Это удобно для:

- backup;
    
- sharing;
    
- Git;
    
- provisioning;
    
- переноса между Grafana instances.
    

---

### Dashboard JSON

Dashboard JSON — полное описание dashboard.

В нем есть:

- panels;
    
- queries;
    
- variables;
    
- layout;
    
- settings.
    

Его можно хранить в Git как dashboard-as-code.

---

### Shared dashboards

Dashboard можно шарить:

- по ссылке;
    
- snapshot;
    
- export JSON;
    
- через folder permissions.
    

В production важно контролировать доступ к sensitive dashboards.

---

## 5. Visualizations

### Time series

Time series — график значений во времени.

Подходит для:

- CPU;
    
- memory;
    
- request rate;
    
- latency;
    
- errors;
    
- disk usage.
    

Это самый частый тип panel.

---

### Gauge

Gauge показывает текущее значение относительно шкалы.

Например:

- disk usage 82%;
    
- CPU usage 70%;
    
- memory usage 65%.
    

Хорошо подходит для capacity/saturation.

---

### Stat

Stat показывает одно число.

Например:

- current RPS;
    
- error rate;
    
- number of pods;
    
- uptime;
    
- p95 latency.
    

Удобно для overview dashboard.

---

### Table

Table показывает данные в виде таблицы.

Например:

- список Pod'ов;
    
- usage по namespaces;
    
- топ сервисов по ошибкам;
    
- состояние PVC.
    

---

### Heatmap

Heatmap показывает распределение значений во времени.

Часто используется для latency histograms.

Например, можно увидеть, как менялось распределение request duration.

---

### Logs panel

Logs panel показывает логи.

Обычно используется с Loki или Elasticsearch.

Можно фильтровать logs по:

- service;
    
- pod;
    
- namespace;
    
- level;
    
- text search.
    

---

### Annotations

Annotations — отметки событий на графиках.

Например:

- deploy;
    
- incident;
    
- config change;
    
- restart;
    
- autoscaling event.
    

Они помогают связать изменение метрик с событием.

---

## 6. Prometheus + Grafana

### Подключение Prometheus

Для подключения Prometheus нужно добавить Data Source.

Обычно:

```text
Type: Prometheus
URL: http://prometheus:9090
```

После этого Grafana сможет выполнять PromQL.

---

### PromQL в Grafana

Grafana использует PromQL для запросов к Prometheus.

Пример:

```promql
sum(rate(http_requests_total[5m])) by (service)
```

Результат можно отобразить как graph, table, stat и т.д.

---

### Query editor

Query editor — интерфейс для написания запросов.

В нем можно:

- писать PromQL;
    
- выбирать labels;
    
- смотреть preview;
    
- настраивать legend;
    
- использовать variables.
    

---

### Variables из labels

Grafana может создавать variables из Prometheus labels.

Например, список namespaces:

```promql
label_values(kube_pod_info, namespace)
```

Потом переменную можно использовать в queries.

---

### Dashboard для Kubernetes

Kubernetes dashboard обычно показывает:

- Nodes;
    
- Pods;
    
- CPU/memory;
    
- restarts;
    
- network;
    
- PVC;
    
- Deployment status;
    
- cluster health.
    

Часто готовые dashboards идут с `kube-prometheus-stack`.

---

### Dashboard для Node Exporter

Node Exporter dashboard показывает Linux host metrics.

Обычно:

- CPU;
    
- RAM;
    
- disk;
    
- filesystem;
    
- network;
    
- load average;
    
- system info.
    

---

### Dashboard для приложений

Application dashboard должен показывать:

- request rate;
    
- error rate;
     
- latency p95/p99;
    
- saturation;
    
- dependencies;
    
- business metrics;
    
- deploy annotations.
    

Лучше строить dashboard вокруг пользовательского impact, а не только CPU/RAM.

---

## 7. Alerts

### Grafana Alerting

Grafana Alerting позволяет создавать alerts прямо в Grafana.

Она может проверять queries и отправлять уведомления в contact points.

Например:

- Slack;
    
- Telegram;
    
- email;
    
- PagerDuty;
    
- webhook.
    

---

### Alert rules

Alert rule — правило с условием срабатывания.

Например:

```text
p95 latency > 500ms for 5m
```

Rule должна быть actionable, то есть требовать понятного действия.

---

### Alert conditions

Alert condition — условие, которое Grafana проверяет.

Например:

- значение выше threshold;
    
- нет данных;
    
- query вернула ошибку;
    
- error rate выше нормы.
    

---

### Alert evaluation

Alert evaluation — периодическая проверка alert rule.

Например, каждые 1 минуту Grafana выполняет query и проверяет condition.

---

### Contact points

Contact points — куда отправлять alert.

Примеры:

- Slack channel;
    
- email;
    
- Telegram;
    
- PagerDuty;
    
- webhook.
    

---

### Notification policies

Notification policies определяют routing alert'ов.

Например:

- critical → on-call;
    
- warning → team channel;
    
- database alerts → DB team;
    
- frontend alerts → frontend team.
    

---

### Silences

Silence временно отключает уведомления по alert'ам.

Используется при:

- maintenance;
    
- planned deploy;
    
- known issue;
    
- шумном alert.
    

Silence не исправляет проблему, а только убирает уведомления.

---

### Grafana Alerting vs Alertmanager

Alertmanager обычно используется вместе с Prometheus alert rules.

Grafana Alerting — alerting внутри Grafana, может работать с разными Data Sources.

```text
Prometheus rules -> Alertmanager
Grafana rules    -> Grafana Alerting
```

В Kubernetes/Prometheus-стеке часто используют Alertmanager.

---

## 8. Variables и Templating

### Dashboard variables

Dashboard variables делают dashboard переиспользуемым.

Например:

- `$cluster`;
    
- `$namespace`;
    
- `$pod`;
    
- `$service`;
    
- `$instance`.
    

---

### Query variables

Query variable получает значения через query к Data Source.

Например, из Prometheus:

```promql
label_values(up, job)
```

Так Grafana автоматически получает список jobs.

---

### Label variables

Label variables часто строятся из labels метрик.

Например:

- namespace;
    
- pod;
    
- service;
    
- instance;
    
- job.
    

Это удобно для Kubernetes dashboards.

---

### Multi-value variables

Multi-value variable позволяет выбрать несколько значений сразу.

Например, несколько namespaces.

В PromQL часто используют regex match:

```promql
namespace=~"$namespace"
```

---

### Использование variables в PromQL

Пример:

```promql
sum(rate(http_requests_total{namespace="$namespace", app="$app"}[5m]))
```

Если variable multi-value:

```promql
sum(rate(http_requests_total{namespace=~"$namespace"}[5m]))
```

---

## 9. Security

### Users

Users — пользователи Grafana.

Им можно давать доступ к:

- dashboards;
    
- folders;
    
- data sources;
    
- alerts;
    
- admin settings.
    

---

### Teams

Teams — группы пользователей.

Например:

- backend;
    
- frontend;
    
- platform;
    
- sre;
    
- security.
    

Через teams проще управлять permissions.

---

### Roles

Roles определяют уровень доступа.

Типичные роли:

- Viewer;
    
- Editor;
    
- Admin.
    

Viewer смотрит dashboards, Editor может редактировать, Admin управляет настройками.

---

### Permissions

Permissions управляют доступом к folders и dashboards.

Например:

- команда backend может редактировать свои dashboards;
    
- остальные могут только смотреть;
    
- security dashboards доступны только SRE/security.
    

---

### Organizations

Organizations позволяют разделять пользователей, dashboards и data sources.

В небольших setup'ах часто используют одну organization.

В больших компаниях иногда разделяют по командам или клиентам.

---

### Authentication basics

Authentication — как пользователь входит в Grafana.

Варианты:

- login/password;
    
- LDAP;
    
- OAuth;
    
- SAML;
    
- auth proxy.
    

В production лучше использовать централизованную authentication.

---

### OAuth basics

OAuth позволяет входить через внешний identity provider.

Например:

- GitHub;
    
- GitLab;
    
- Google;
    
- Okta;
    
- Azure AD / Entra ID.
    

Это удобнее и безопаснее, чем локальные пользователи.

---

## 10. Provisioning и Automation

### Grafana provisioning

Provisioning позволяет настраивать Grafana через YAML-файлы.

Можно provision'ить:

- data sources;
    
- dashboards;
    
- alerting;
    
- plugins.
    

Это удобно для GitOps и Kubernetes.

---

### Dashboards as code

Dashboards as code — хранение dashboards в Git.

Обычно это JSON-файлы.

Плюсы:

- review изменений;
    
- rollback;
    
- единый source of truth;
    
- восстановление Grafana.
    

---

### Datasources as code

Data Sources можно описывать YAML-файлом.

Например:

```yaml
apiVersion: 1
datasources:
  - name: Prometheus
    type: prometheus
    url: http://prometheus:9090
```

Так data source создается автоматически при старте Grafana.

---

### Configuration files

Основные config-файлы Grafana:

- `grafana.ini`;
    
- provisioning YAML;
    
- dashboards JSON;
    
- alerting configs.
    

В Kubernetes это обычно ConfigMap/Secret.

---

### Grafana Helm deployment

В Kubernetes Grafana часто ставят через Helm.

Например:

- отдельным Grafana chart;
    
- внутри `kube-prometheus-stack`.
    

Values позволяют настроить:

- admin password;
    
- data sources;
    
- dashboards;
    
- ingress;
    
- persistence;
    
- plugins.
    

---

## 11. Kubernetes Integration

### Grafana в Kubernetes

Grafana в Kubernetes обычно запускается как Deployment.

Компоненты:

- Pod с Grafana;
    
- Service;
    
- Ingress;
    
- ConfigMaps;
    
- Secrets;
    
- PVC для persistent storage.
    

---

### kube-prometheus-stack

`kube-prometheus-stack` часто ставит сразу:

- Prometheus;
    
- Grafana;
    
- Alertmanager;
    
- node-exporter;
    
- kube-state-metrics;
    
- default dashboards.
    

Это самый популярный быстрый способ поднять monitoring stack в Kubernetes.

---

### Kubernetes dashboards

Kubernetes dashboards обычно показывают:

- cluster overview;
    
- node resources;
    
- pod resources;
    
- deployments;
    
- namespaces;
    
- restarts;
    
- PVC usage;
    
- alerts.
    

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
    
- DiskPressure/MemoryPressure.
    

---

### Monitoring Pods

Для Pods смотрят:

- CPU/memory;
    
- restarts;
    
- OOMKilled;
    
- readiness;
    
- status phase;
    
- network;
    
- container filesystem.
    

---

### Monitoring Applications

Для приложений смотрят:

- RPS;
    
- error rate;
    
- latency;
    
- saturation;
    
- dependency errors;
    
- queue size;
    
- business metrics.
    

Это важнее, чем просто “Pod Running”.

---

## 12. Troubleshooting

### Dashboard показывает No data

Причины:

- Data Source недоступен;
    
- неправильный PromQL/LogQL;
    
- выбран неправильный time range;
    
- labels не совпадают;
    
- метрик реально нет;
    
- переменная выбрала пустое значение.
    

Проверить query напрямую в Data Source.

---

### Ошибка подключения Data Source

Проверить:

- URL;
    
- DNS;
    
- network;
    
- credentials;
    
- TLS;
    
- proxy settings;
    
- доступ из Pod Grafana до Data Source.
    

В Kubernetes часто проблема в Service name или namespace.

---

### Неправильный PromQL

Признаки:

- query возвращает пусто;
    
- слишком много series;
    
- неверные значения;
    
- ошибка syntax.
    

Проверить:

- metric name;
    
- labels;
    
- range vector;
    
- aggregation;
    
- rate/increase только для counters.
    

---

### Медленные Dashboard

Причины:

- слишком тяжелые queries;
    
- большой time range;
    
- слишком много panels;
    
- высокая cardinality;
    
- нет recording rules;
    
- слишком частое refresh interval.
    

Решение: упростить queries, добавить recording rules, уменьшить panels.

---

### Слишком много series

Слишком много series перегружает Grafana и Data Source.

Причины:

- плохие labels;
    
- query без aggregation;
    
- multi-value variable выбирает слишком много;
    
- высок cardinality.
    

Нужно агрегировать через `sum by`, `avg by` и ограничивать labels.

---

### Высокая нагрузка на Grafana

Причины:

- много пользователей;
    
- много dashboards;
    
- тяжелые queries;
    
- частый auto-refresh;
    
- много alert rules;
    
- медленная database.
    

Важно помнить: нагрузка часто падает не только на Grafana, но и на Prometheus/Loki.

---

### Проблемы с permissions

Проблемы с permissions:

- пользователь не видит dashboard;
    
- не может редактировать panel;
    
- нет доступа к folder;
    
- нет прав на data source;
    
- роль только Viewer.
    

Проверять нужно organization, team, folder permissions и role.

---

## 13. Практика

### Подключить Prometheus как Data Source

В Grafana:

```text
Connections -> Data sources -> Add data source -> Prometheus
```

URL:

```text
http://prometheus:9090
```

После этого нажать Save & Test.

---

### Создать Dashboard

Создать dashboard:

- New dashboard;
    
- Add visualization;
    
- выбрать Data Source;
    
- написать query;
    
- выбрать visualization;
    
- сохранить dashboard.
    

---

### Добавить CPU/RAM/Disk панели

Примеры panel:

- CPU usage;
    
- memory usage;
    
- disk usage;
    
- filesystem available;
    
- network traffic.
    

Для Node Exporter это обычно делается через Prometheus queries.

---

### Создать переменную для namespace

Пример query variable:

```promql
label_values(kube_pod_info, namespace)
```

Variable можно назвать:

```text
namespace
```

И использовать как `$namespace`.

---

### Сделать Kubernetes Dashboard

Минимальные panels:

- CPU by namespace;
    
- Memory by namespace;
    
- Pod restarts;
    
- Pods not ready;
    
- Node status;
    
- PVC usage;
    
- Deployment replicas.
    

---

### Импортировать готовый Dashboard

В Grafana можно импортировать dashboard:

- через dashboard ID;
    
- через JSON;
    
- через provisioning.
    

Готовые dashboards часто используют для Node Exporter и Kubernetes.

---

### Настроить Alert

Пример:

- выбрать query;
    
- задать condition;
    
- настроить evaluation interval;
    
- выбрать contact point;
    
- сохранить alert rule.
    

Alert должен быть понятным и actionable.

---

### Развернуть Grafana через Helm

Пример:

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

helm install grafana grafana/grafana -n monitoring --create-namespace
```

Или поставить вместе с `kube-prometheus-stack`.

---

## 14. Interview Questions

### Что такое Grafana

Grafana — инструмент для dashboards, visualizations и alerting.

Она подключается к data sources и показывает metrics/logs/traces в удобном виде.

---

### Grafana vs Prometheus

Prometheus собирает и хранит metrics.

Grafana визуализирует данные из Prometheus и других sources.

```text
Prometheus -> хранит метрики
Grafana    -> показывает dashboards
```

---

### Что такое Dashboard

Dashboard — страница с набором panels.

Он показывает состояние системы, приложения или инфраструктуры.

---

### Что такое Panel

Panel — отдельная визуализация внутри dashboard.

Например:

- график CPU;
    
- таблица Pod'ов;
    
- stat error rate;
    
- logs panel.
    

---

### Что такое Data Source

Data Source — источник данных для Grafana.

Например:

- Prometheus;
    
- Loki;
    
- Elasticsearch;
    
- CloudWatch;
    
- Azure Monitor.
    

---

### Как Grafana получает данные

Grafana отправляет query в Data Source.

Например, PromQL в Prometheus.

```text
Grafana -> Prometheus query -> result -> panel
```

---

### Можно ли хранить метрики в Grafana

Обычно нет.

Grafana в основном визуализирует данные.

Метрики хранятся в Prometheus, InfluxDB, Mimir, VictoriaMetrics или других хранилищах.

---

### Grafana Alerting vs Alertmanager

Grafana Alerting работает внутри Grafana и может использовать разные Data Sources.

Alertmanager принимает alerts от Prometheus и занимается routing/grouping/silencing.

В Prometheus-стеке часто основной alerting делают через Prometheus + Alertmanager.

---

### Как сделать Dashboard для Kubernetes

Нужно:

- собрать metrics через Prometheus;
    
- поставить kube-state-metrics и node-exporter;
    
- подключить Prometheus в Grafana;
    
- создать panels по Nodes, Pods, Deployments, PVC;
    
- добавить variables для cluster/namespace/pod;
    
- настроить alerts на важные симптомы.