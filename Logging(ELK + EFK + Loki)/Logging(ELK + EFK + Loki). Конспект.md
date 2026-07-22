## 1. Основы логирования

### Что такое лог

Лог — запись события в системе или приложении.

Пример:

```text
2026-07-22 12:00:01 ERROR failed to connect to database
```

Логи помогают понять, что происходило внутри приложения или сервера.

---

### Зачем нужны логи

Логи нужны для:

- troubleshooting;
    
- расследования инцидентов;
    
- audit;
    
- анализа поведения приложения;
    
- поиска ошибок;
    
- понимания контекста проблемы.
    

Метрики говорят “что сломалось”, логи часто помогают понять “почему”.

---

### Application logs

Application logs — логи самого приложения.

Примеры:

- ошибка подключения к БД;
    
- пользователь не авторизован;
    
- запрос обработан;
    
- exception stack trace;
    
- payment failed.
    

---

### System logs

System logs — логи операционной системы.

Например:

- systemd services;
    
- kernel messages;
    
- SSH login;
    
- cron jobs;
    
- disk/network errors.
    

В Linux часто смотрят через `journalctl` или файлы в `/var/log`.

---

### Access logs

Access logs — логи входящих запросов.

Например, nginx access log:

```text
10.0.0.1 - - "GET /api/users HTTP/1.1" 200
```

Полезны для анализа traffic, status codes, latency и client IP.

---

### Audit logs

Audit logs — логи действий пользователей или систем.

Например:

- кто удалил ресурс;
    
- кто изменил права;
    
- кто вошел в систему;
    
- кто прочитал secret.
    

Audit logs важны для security и compliance.

---

### Structured vs Unstructured logs

Unstructured logs — обычный текст.

```text
User login failed for user 123
```

Structured logs — логи в формате с полями.

```json
{
  "level": "error",
  "event": "login_failed",
  "user_id": "123"
}
```

Structured logs проще парсить и искать.

---

### JSON logs

JSON logs — структурированные логи в JSON.

Пример:

```json
{
  "timestamp": "2026-07-22T12:00:00Z",
  "level": "error",
  "service": "api",
  "message": "database timeout"
}
```

Хорошо подходят для Kubernetes и централизованного логирования.

---

### Log levels

Log levels показывают важность сообщения.

Обычно:

- DEBUG;
    
- INFO;
    
- WARN;
    
- ERROR;
    
- FATAL.
    

Правильный level помогает фильтровать шум.

---

### DEBUG

DEBUG — подробная информация для разработки и диагностики.

Например:

- значения переменных;
    
- детали запроса;
    
- внутренние шаги выполнения.
    

В production DEBUG обычно выключают или включают временно.

---

### INFO

INFO — нормальные важные события.

Например:

- приложение стартовало;
    
- запрос обработан;
    
- job завершился;
    
- пользователь вошел.
    

INFO не должен быть слишком шумным.

---

### WARN

WARN — потенциальная проблема, но сервис еще работает.

Например:

- retry запроса;
    
- медленный ответ dependency;
    
- deprecated config;
    
- превышен мягкий лимит.
    

---

### ERROR

ERROR — ошибка, которая повлияла на операцию.

Например:

- запрос завершился 500;
    
- не удалось подключиться к БД;
    
- job failed;
    
- exception.
    

ERROR обычно должен быть расследуемым.

---

### FATAL

FATAL — критическая ошибка, после которой приложение не может продолжать работу.

Например:

- не загрузился обязательный config;
    
- нет подключения к критичной dependency при старте;
    
- corruption данных.
    

После FATAL приложение часто завершает процесс.

---

## 2. Централизованное логирование

### Зачем собирать логи централизованно

Если логи лежат только на отдельных серверах или Pod'ах, их сложно искать.

Централизованное логирование дает:

- единый поиск;
    
- хранение после удаления Pod;
    
- dashboards;
    
- alerts;
    
- audit;
    
- удобное расследование инцидентов.
    

---

### Log collection

Log collection — сбор логов с источников.

Источники:

- приложения;
    
- контейнеры;
    
- Kubernetes nodes;
    
- systemd;
    
- nginx;
    
- databases.
    

Обычно сбор делает log agent.

---

### Log aggregation

Log aggregation — объединение логов из разных источников в одном месте.

Пример:

```text
app-1 logs
app-2 logs
node logs
nginx logs
        ↓
central log storage
```

---

### Log storage

Log storage — хранилище логов.

Примеры:

- Elasticsearch;
    
- Loki;
    
- OpenSearch;
    
- S3/Object Storage;
    
- cloud logging services.
    

---

### Log parsing

Log parsing — разбор строки лога на поля.

Например из строки:

```text
status=500 method=GET path=/api
```

получить поля:

- `status`;
    
- `method`;
    
- `path`.
    

---

### Log indexing

Log indexing — создание индекса для быстрого поиска.

Elasticsearch индексирует много полей и полный текст.

Loki индексирует в основном labels, а не весь текст логов.

---

### Log search

Log search — поиск по логам.

Например:

- найти все `ERROR`;
    
- найти request по `trace_id`;
    
- найти все 500 за 10 минут;
    
- найти логи конкретного Pod.
    

---

### Log visualization

Log visualization — отображение логов в UI.

Примеры:

- Kibana Discover;
    
- Grafana Explore;
    
- logs panels;
    
- dashboards по ошибкам.
    

---

### Log retention

Log retention — сколько времени хранить логи.

Например:

```text
debug logs    -> 7 дней
app logs      -> 30 дней
audit logs    -> 1 год
security logs -> 2 года
```

Retention нужен для контроля стоимости и compliance.

---

## 3. Logging Pipeline

### Источник логов

Источник логов — место, где логи появляются.

Примеры:

- stdout/stderr контейнера;
    
- `/var/log/nginx/access.log`;
    
- journald;
    
- application file;
    
- Kubernetes audit log.
    

---

### Log agent

Log agent собирает логи и отправляет их дальше.

Примеры:

- Fluent Bit;
    
- Fluentd;
    
- Filebeat;
    
- Promtail;
    
- Grafana Alloy;
    
- Vector.
    

В Kubernetes обычно запускается как DaemonSet.

---

### Parsing и enrichment

Parsing разбирает лог на поля.

Enrichment добавляет metadata.

Например:

- namespace;
    
- pod;
    
- container;
    
- node;
    
- environment;
    
- cluster;
    
- service.
    

Это делает поиск и фильтрацию удобнее.

---

### Buffering

Buffering временно сохраняет логи перед отправкой.

Нужно, если storage временно недоступен.

Без buffering при проблемах сети можно потерять логи.

---

### Storage

Storage хранит логи.

Примеры:

- Elasticsearch;
    
- Loki;
    
- S3;
    
- OpenSearch;
    
- cloud logging.
    

Выбор зависит от объема, поиска, цены и retention.

---

### Visualization

Visualization показывает логи пользователю.

Примеры:

- Kibana;
    
- Grafana;
    
- OpenSearch Dashboards.
    

Обычно там делают search, filters и dashboards.

---

### Alerting по логам

Alerting по логам — алерты на основе появления определенных log events.

Например:

- много `ERROR`;
    
- появилась строка `panic`;
    
- много failed logins;
    
- 500 errors больше порога.
    

Важно не делать слишком шумные log alerts.

---

## 4. Логи в Linux

### `stdout` и `stderr`

`stdout` — стандартный вывод.

`stderr` — стандартный поток ошибок.

Пример:

```bash
echo "normal message"
echo "error message" >&2
```

В контейнерах хорошая практика — писать логи в stdout/stderr.

---

### `journald`

`journald` — logging-компонент systemd.

Он собирает логи:

- systemd services;
    
- kernel;
    
- boot;
    
- stdout/stderr сервисов.
    

Смотреть через `journalctl`.

---

### `journalctl`

`journalctl` показывает логи из journald.

Примеры:

```bash
journalctl
journalctl -u nginx
journalctl -u kubelet -f
journalctl --since "1 hour ago"
```

Очень полезно для debug systemd services.

---

### Syslog

Syslog — классический механизм логирования в Linux/Unix.

Логи могут попадать в:

- `/var/log/syslog`;
    
- `/var/log/messages`;
    
- remote syslog server.
    

Сейчас часто используется вместе с rsyslog или journald.

---

### `/var/log`

`/var/log` — стандартная директория логов Linux.

Примеры:

```text
/var/log/syslog
/var/log/messages
/var/log/auth.log
/var/log/nginx/access.log
/var/log/nginx/error.log
```

---

### Log rotation

Log rotation — ротация логов.

Она нужна, чтобы логи не заполнили диск.

Обычно старый файл:

- переименовывается;
    
- сжимается;
    
- удаляется после retention.
    

---

### `logrotate`

`logrotate` управляет ротацией логов.

Пример config:

```text
/var/log/myapp/*.log {
  daily
  rotate 7
  compress
  missingok
  notifempty
}
```

---

## 5. Логи контейнеров

### Docker logs

Docker собирает stdout/stderr контейнера.

Эти логи можно смотреть через Docker CLI.

Docker сам не является полноценной системой централизованного логирования.

---

### `docker logs`

Посмотреть логи контейнера:

```bash
docker logs <container>
docker logs -f <container>
docker logs --tail 100 <container>
```

Для debug локальных контейнеров этого часто достаточно.

---

### Logging drivers

Docker logging driver определяет, куда писать логи.

Примеры:

- `json-file`;
    
- `journald`;
    
- `syslog`;
    
- `fluentd`;
    
- `awslogs`.
    

По умолчанию часто используется `json-file`.

---

### Логи в `stdout` и `stderr`

Контейнерные приложения должны писать логи в stdout/stderr.

Плохо:

```text
приложение пишет только в /app/logs/app.log
```

Хорошо:

```text
приложение пишет в stdout/stderr
```

Так Docker/Kubernetes легко собирают логи.

---

### Ограничение размера логов

Если не ограничивать размер логов контейнера, они могут заполнить диск.

Для Docker `json-file` можно настроить:

```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m",
    "max-file": "5"
  }
}
```

---

### Ротация логов контейнеров

Ротация контейнерных логов нужна, чтобы Node не умерла от DiskPressure.

В Kubernetes это обычно настраивается на уровне kubelet/container runtime.

---

## 6. Логи Kubernetes

### `kubectl logs`

`kubectl logs` показывает логи Pod.

```bash
kubectl logs pod-name
kubectl logs -f pod-name
```

Если Pod управляется Deployment:

```bash
kubectl logs deploy/myapp
```

---

### Логи конкретного контейнера

Если в Pod несколько контейнеров, нужно указать container:

```bash
kubectl logs pod-name -c app
```

Иначе Kubernetes может попросить выбрать container.

---

### Логи предыдущего контейнера

Если container перезапустился, можно посмотреть прошлые логи:

```bash
kubectl logs pod-name --previous
```

Очень полезно при CrashLoopBackOff.

---

### Логи нескольких реплик

Для нескольких Pod'ов можно использовать label selector:

```bash
kubectl logs -l app=myapp --all-containers=true
```

Для удобства часто используют tools типа `stern` или `kubetail`.

---

### Логи Node

Логи Node смотрят на самой Node.

Например:

```bash
journalctl -u kubelet
journalctl -u containerd
dmesg
```

Они нужны при проблемах kubelet, runtime, CNI, disk, kernel.

---

### Логи Control Plane

Control Plane logs зависят от способа установки.

В kubeadm control plane часто компоненты работают как static Pods.

Пример:

```bash
kubectl logs -n kube-system kube-apiserver-<node>
kubectl logs -n kube-system kube-controller-manager-<node>
kubectl logs -n kube-system kube-scheduler-<node>
```

---

### Kubernetes audit logs

Kubernetes audit logs показывают обращения к API Server.

Например:

- кто создал Secret;
    
- кто удалил Pod;
    
- кто изменил RoleBinding.
    

Это важно для security и расследований.

---

### DaemonSet для сбора логов

В Kubernetes log agent обычно запускается как DaemonSet.

```text
1 Node = 1 log agent Pod
```

Agent читает container logs с Node и отправляет их в storage.

---

### Почему логи нельзя хранить только внутри Pod

Pod может быть:

- удален;
    
- пересоздан;
    
- evicted;
    
- перемещен на другую Node.
    

Если логи только внутри Pod, они легко теряются.

Поэтому production logs нужно отправлять во внешнее хранилище.

---

## 7. ELK / EFK Stack

### Что такое ELK

ELK — стек для логирования:

```text
Elasticsearch + Logstash + Kibana
```

Logstash собирает/обрабатывает логи, Elasticsearch хранит, Kibana показывает.

---

### Что такое EFK

EFK — вариант стека:

```text
Elasticsearch + Fluentd/Fluent Bit + Kibana
```

В Kubernetes чаще встречается Fluent Bit или Fluentd вместо Logstash.

---

### Elasticsearch

Elasticsearch — search engine и storage для документов.

В логировании он:

- хранит logs как documents;
    
- индексирует поля;
    
- позволяет быстро искать;
    
- используется Kibana для визуализации.
    

---

### Logstash

Logstash — pipeline processor для логов.

Он умеет:

- принимать input;
    
- парсить и фильтровать;
    
- обогащать данные;
    
- отправлять output.
    

Но он довольно тяжелый по ресурсам.

---

### Fluentd

Fluentd — log collector/processor.

Используется для сбора, обработки и отправки логов.

Гибкий, но тяжелее Fluent Bit.

---

### Fluent Bit

Fluent Bit — легковесный log collector.

Часто используется в Kubernetes как DaemonSet.

Плюсы:

- меньше CPU/RAM;
    
- хорошо подходит для edge/node log collection;
    
- поддерживает много outputs.
    

---

### Kibana

Kibana — UI для Elasticsearch/OpenSearch.

Используется для:

- поиска логов;
    
- dashboards;
    
- visualizations;
    
- alerting;
    
- data views.
    

---

### ELK vs EFK

ELK использует Logstash.

EFK использует Fluentd/Fluent Bit.

```text
ELK -> Logstash
EFK -> Fluentd / Fluent Bit
```

В Kubernetes чаще выбирают EFK из-за DaemonSet-подхода и меньшей нагрузки.

---

## 8. Elasticsearch

### Cluster

Elasticsearch cluster — группа nodes, которые вместе хранят данные.

Cluster отвечает за:

- распределение shards;
    
- репликацию;
    
- поиск;
    
- отказоустойчивость.
    

---

### Node

Node — отдельный Elasticsearch instance.

Типы ролей:

- master;
    
- data;
    
- ingest;
    
- coordinating;
    
- machine learning.
    

Для маленького setup роли часто совмещены.

---

### Index

Index — логическое хранилище документов.

Например:

```text
logs-app-2026.07.22
```

В логировании часто используют индексы по времени.

---

### Document

Document — отдельная запись в Elasticsearch.

Один log event обычно становится одним document.

Пример document:

```json
{
  "level": "error",
  "message": "db timeout",
  "service": "api"
}
```

---

### Field

Field — поле внутри document.

Примеры:

- `timestamp`;
    
- `level`;
    
- `service`;
    
- `message`;
    
- `pod`;
    
- `namespace`.
    

---

### Mapping

Mapping описывает типы полей в index.

Например:

- `keyword`;
    
- `text`;
    
- `date`;
    
- `long`;
    
- `ip`.
    

Неправильный mapping может ломать поиск и агрегации.

---

### Shard

Shard — часть index.

Elasticsearch делит index на shards, чтобы распределять данные по nodes.

Слишком много shards — частая причина проблем с производительностью.

---

### Replica

Replica — копия primary shard.

Нужна для:

- отказоустойчивости;
    
- повышения доступности;
    
- распределения read load.
    

Если replica не может разместиться, cluster может быть yellow.

---

### Inverted index

Inverted index — структура для быстрого полнотекстового поиска.

Упрощенно:

```text
слово -> список документов, где оно встречается
```

Именно поэтому Elasticsearch хорошо ищет по тексту.

---

### Query basics

Elasticsearch поддерживает разные запросы.

Примеры:

- поиск по exact field;
    
- full-text search;
    
- range query;
    
- bool query;
    
- aggregation.
    

В Kibana часто используют KQL.

---

### Index lifecycle management

Index Lifecycle Management, или ILM, управляет жизненным циклом индексов.

Например:

```text
hot -> warm -> cold -> delete
```

Это помогает контролировать стоимость и retention.

---

### Retention

Retention — сколько хранить логи.

В Elasticsearch retention часто реализуют через ILM.

Например:

- хранить app logs 30 дней;
    
- audit logs 1 год;
    
- debug logs 7 дней.
    

---

### Почему высокая cardinality опасна

Высокая cardinality — много уникальных значений поля.

Например:

- `user_id`;
    
- `request_id`;
    
- `session_id`.
    

Это может раздувать индексы, memory usage и ухудшать aggregation/search performance.

---

### Почему Elasticsearch требует много ресурсов

Elasticsearch требует много ресурсов, потому что:

- индексирует данные;
    
- хранит shards;
    
- держит структуры поиска в памяти;
    
- выполняет тяжелые queries;
    
- делает replication;
    
- обрабатывает большой объем логов.
    

Для production ему нужны нормальные CPU/RAM/disk.

---

## 9. Logstash / Fluentd / Fluent Bit

### Log collector

Log collector собирает логи и отправляет их в storage.

Примеры:

- Logstash;
    
- Fluentd;
    
- Fluent Bit;
    
- Filebeat;
    
- Vector;
    
- Promtail.
    

---

### Input

Input — откуда collector получает логи.

Примеры:

- file;
    
- TCP;
    
- UDP;
    
- HTTP;
    
- journald;
    
- Kubernetes container logs.
    

---

### Filter

Filter обрабатывает логи.

Например:

- parse JSON;
    
- parse regex;
    
- добавить field;
    
- удалить secret;
    
- изменить timestamp;
    
- drop debug logs.
    

---

### Output

Output — куда отправлять логи.

Примеры:

- Elasticsearch;
    
- Loki;
    
- Kafka;
    
- S3;
    
- stdout;
    
- cloud logging.
    

---

### Parsing логов

Parsing превращает строку в поля.

Например:

```text
10.0.0.1 GET /api 200 15ms
```

можно разобрать в:

- ip;
    
- method;
    
- path;
    
- status;
    
- duration.
    

---

### Regex и Grok basics

Regex/Grok используют для разбора неструктурированных логов.

Grok — удобные шаблоны поверх regex.

Например для nginx/apache logs.

Минус: сложные regex могут быть медленными и хрупкими.

---

### Добавление labels и metadata

Collector может добавлять metadata.

В Kubernetes обычно добавляют:

- namespace;
    
- pod;
    
- container;
    
- node;
    
- labels;
    
- annotations.
    

Это помогает искать логи по workload.

---

### Buffering

Buffering сохраняет логи временно, если output недоступен.

Buffer может быть:

- memory;
    
- file/disk;
    
- persistent queue.
    

Это снижает риск потери логов.

---

### Fluentd vs Fluent Bit

Fluent Bit легче и быстрее.

Fluentd гибче, но тяжелее.

```text
Fluent Bit -> lightweight agent
Fluentd    -> more powerful processor
```

В Kubernetes часто Fluent Bit стоит на nodes, а Fluentd может быть aggregator.

---

### Logstash vs Fluent Bit

Logstash мощный, но тяжелый.

Fluent Bit легкий и хорошо подходит для DaemonSet на каждой Node.

```text
Logstash   -> heavy processing
Fluent Bit -> lightweight collection
```

---

## 10. Kibana

### Data View

Data View описывает, какие Elasticsearch indices смотреть.

Например:

```text
logs-*
filebeat-*
app-*
```

Без Data View Kibana не знает, откуда брать данные.

---

### Discover

Discover — раздел Kibana для поиска и просмотра логов.

Там можно:

- выбрать time range;
    
- фильтровать fields;
    
- искать текст;
    
- смотреть documents.
    

---

### Search

Search — поиск по логам.

Например:

- `level: error`;
    
- `service: api`;
    
- `status >= 500`;
    
- текст внутри message.
    

---

### Filters

Filters ограничивают выборку.

Примеры:

- namespace = prod;
    
- level = error;
    
- service = backend;
    
- status >= 500.
    

Фильтры помогают быстро сузить проблему.

---

### Dashboards

Kibana dashboards показывают visualizations по логам.

Например:

- errors over time;
    
- top services by errors;
    
- status codes;
    
- latency distribution;
    
- failed logins.
    

---

### Visualizations

Visualizations — графики и таблицы по логам.

Примеры:

- bar chart;
    
- line chart;
    
- pie chart;
    
- data table;
    
- heatmap.
    

---

### KQL basics

KQL — Kibana Query Language.

Примеры:

```text
level: "error"
service: "api" and status >= 500
message: *timeout*
```

KQL удобен для поиска в Discover.

---

### Alerting basics

Kibana может создавать alerts по логам.

Например:

- ошибок больше 100 за 5 минут;
    
- появились failed logins;
    
- нет логов от сервиса;
    
- cluster health red.
    

---

## 11. Loki

### Что такое Loki

Loki — система хранения и поиска логов от Grafana Labs.

Она похожа на Prometheus по идее labels, но хранит logs.

Loki обычно используют вместе с Grafana.

---

### Loki architecture

Упрощенно:

```text
Promtail/Alloy/Fluent Bit -> Loki -> Grafana
```

В distributed mode есть отдельные компоненты:

- distributor;
    
- ingester;
    
- querier;
    
- query frontend.
    

---

### Log streams

Log stream — поток логов с одинаковым набором labels.

Пример:

```text
{namespace="prod", pod="api-123", container="app"}
```

Каждая уникальная комбинация labels — отдельный stream.

---

### Labels

Labels в Loki нужны для выбора log streams.

Примеры хороших labels:

- `cluster`;
    
- `namespace`;
    
- `app`;
    
- `pod`;
    
- `container`.
    

Плохие labels:

- `request_id`;
    
- `user_id`;
    
- `trace_id`.
    

---

### Chunks

Chunks — сжатые блоки логов внутри Loki.

Ingester собирает логи в chunks и отправляет их в storage.

---

### Index

Index в Loki хранит labels и ссылки на chunks.

Loki не индексирует каждое слово в сообщениях логов.

Это делает storage дешевле, но поиск по тексту работает иначе, чем в Elasticsearch.

---

### Loki vs Elasticsearch

Elasticsearch индексирует много полей и полный текст.

Loki индексирует labels, а сами строки логов хранит в chunks.

```text
Elasticsearch -> мощный full-text search, дороже
Loki          -> labels + cheaper log storage
```

---

### Почему Loki не индексирует полный текст логов

Loki не индексирует полный текст, чтобы:

- снизить стоимость хранения;
    
- уменьшить размер индекса;
    
- лучше масштабироваться для больших объемов логов.
    

Поиск по тексту есть, но он сканирует выбранные streams.

---

### Cardinality labels

Cardinality labels — проблема, когда labels имеют слишком много уникальных значений.

В Loki это особенно опасно, потому что каждый набор labels создает новый log stream.

---

### Хорошие и плохие labels

Хорошие labels:

- `namespace`;
    
- `app`;
    
- `container`;
    
- `cluster`;
    
- `environment`.
    

Плохие labels:

- `request_id`;
    
- `user_id`;
    
- `email`;
    
- `session_id`;
    
- `timestamp`.
    

Trace/request ID лучше хранить внутри строки лога, а не label.

---

## 12. Компоненты Loki

### Loki

Loki — backend для хранения и поиска логов.

Он принимает logs от agents, хранит их и отвечает на LogQL queries.

---

### Promtail

Promtail — старый агент для сбора логов в Loki.

Он читает файлы логов, добавляет labels и отправляет в Loki.

В новых setup'ах часто смотрят в сторону Grafana Alloy.

---

### Grafana Alloy basics

Grafana Alloy — agent для сбора telemetry.

Он может собирать:

- logs;
    
- metrics;
    
- traces.
    

Для Loki Alloy может заменить Promtail и отправлять logs в Loki.

---

### Distributor

Distributor принимает логи от clients.

Он валидирует streams и распределяет данные по ingesters.

В distributed mode это входная точка для write path.

---

### Ingester

Ingester получает logs, хранит их временно в памяти, формирует chunks и отправляет в object storage.

Также отвечает на queries по свежим данным.

---

### Querier

Querier выполняет LogQL-запросы.

Он читает index/chunks и возвращает данные пользователю или Grafana.

---

### Query frontend

Query frontend оптимизирует запросы.

Он может:

- делить большие queries на части;
    
- кешировать результаты;
    
- ставить queries в очередь;
    
- снижать нагрузку на queriers.
    

---

### Object Storage

Object Storage хранит chunks и index data.

Примеры:

- S3;
    
- GCS;
    
- Azure Blob;
    
- MinIO.
    

Для production Loki обычно использует object storage.

---

### Single binary mode

Single binary mode — Loki запускается одним процессом.

Подходит для:

- dev;
    
- small setup;
    
- тестов;
    
- простых инсталляций.
    

---

### Distributed mode

Distributed mode разделяет Loki на компоненты.

Подходит для production и больших объемов логов.

```text
distributor
ingester
querier
query-frontend
compactor
gateway
```

---

## 13. LogQL

### Что такое LogQL

LogQL — язык запросов Loki.

Он похож на PromQL, но для логов.

Позволяет:

- выбирать streams;
    
- фильтровать строки;
    
- парсить JSON/logfmt;
    
- строить метрики из логов.
    

---

### Log stream selector

Selector выбирает log streams по labels.

Пример:

```logql
{namespace="prod", app="api"}
```

Это первый шаг почти любого LogQL-запроса.

---

### Label filters

Label filters выбирают streams по labels.

Пример:

```logql
{namespace="prod", app=~"api|worker"}
```

Можно использовать exact match и regex.

---

### Line filters

Line filters фильтруют строки логов по содержимому.

Например:

```logql
{app="api"} |= "error"
```

Сначала выбираем stream, потом фильтруем строки.

---

### `|=`

`|=` оставляет строки, которые содержат текст.

```logql
{app="api"} |= "timeout"
```

---

### `!=`

`!=` исключает строки, которые содержат текст.

```logql
{app="api"} != "healthcheck"
```

Полезно убрать шум.

---

### Regex filters

Regex filter ищет строки по regex.

```logql
{app="api"} |~ "5[0-9][0-9]"
```

Regex мощный, но может быть тяжелее обычного `|=`.

---

### JSON parser

JSON parser разбирает JSON logs на поля.

```logql
{app="api"} | json | level="error"
```

Хорошо работает со structured JSON logs.

---

### Logfmt parser

Logfmt parser разбирает logfmt-строки.

Пример logfmt:

```text
level=error status=500 path=/api
```

Query:

```logql
{app="api"} | logfmt | status="500"
```

---

### `rate`

`rate` в LogQL может считать скорость появления log lines.

Пример:

```logql
rate({app="api"} |= "error" [5m])
```

Это errors per second по логам.

---

### `count_over_time`

`count_over_time` считает количество log lines за период.

```logql
count_over_time({app="api"} |= "error" [5m])
```

Удобно для alerting по логам.

---

### Метрики из логов

Из логов можно делать метрики.

Например:

- количество ошибок;
    
- rate 500;
    
- количество failed login;
    
- число timeout за 5 минут.
    

Но если есть возможность, лучше важные метрики экспортировать напрямую из приложения.

---

## 14. Grafana + Loki

### Loki как Data Source

В Grafana Loki подключается как Data Source.

Обычно URL:

```text
http://loki:3100
```

После этого можно писать LogQL в Explore и panels.

---

### Просмотр логов в Explore

Grafana Explore позволяет искать логи.

Пример:

```logql
{namespace="prod", app="api"} |= "error"
```

Explore удобен для troubleshooting.

---

### Logs panel

Logs panel показывает логи прямо на dashboard.

Например:

- последние ошибки сервиса;
    
- логи Pod;
    
- failed jobs;
    
- audit события.
    

---

### Фильтрация по namespace

Фильтр по namespace:

```logql
{namespace="prod"}
```

В dashboard обычно делают variable `$namespace`.

---

### Фильтрация по Pod

Фильтр по Pod:

```logql
{namespace="prod", pod=~"api-.*"}
```

Полезно при debug конкретной реплики.

---

### Связь логов и метрик

В Grafana можно рядом смотреть:

- метрику error rate;
    
- логи с `ERROR`;
    
- deploy annotations;
    
- traces.
    

Это помогает быстрее найти причину инцидента.

---

### Алерты на основе логов

Grafana может делать alert по LogQL.

Пример идеи:

```logql
count_over_time({app="api"} |= "panic" [5m]) > 0
```

Но лучше не строить весь monitoring только на логах.

---

## 15. Logs, Metrics и Traces

### Logs vs Metrics

Metrics — числовые значения во времени.

Logs — подробные события.

```text
metrics -> сколько ошибок
logs    -> какие именно ошибки
```

Обычно сначала смотрят метрику, потом идут в логи.

---

### Logs vs Traces

Logs показывают события внутри сервиса.

Traces показывают путь запроса между сервисами.

```text
logs   -> что писал сервис
traces -> где запрос проходил и сколько занял
```

---

### Correlation ID

Correlation ID — идентификатор, который связывает события одного бизнес-процесса или запроса.

Его пишут в логи, чтобы найти все связанные записи.

---

### Trace ID

Trace ID — ID distributed trace.

Он позволяет связать:

- logs;
    
- spans;
    
- запросы между сервисами;
    
- ошибки.
    

Trace ID должен попадать в structured logs.

---

### Связь логов с запросом пользователя

Чтобы связать логи с запросом пользователя, в логи добавляют:

- request_id;
    
- trace_id;
    
- user_id, если безопасно;
    
- session_id, если безопасно;
    
- route;
    
- service.
    

Sensitive данные нужно маскировать.

---

### Переход от метрики к логам

Типичный flow:

```text
error rate вырос
        ↓
открыть logs за тот же time range
        ↓
фильтр service + level=error
        ↓
найти common error
```

---

### Переход от логов к trace

Если в логе есть trace_id, можно открыть trace.

```text
log line -> trace_id -> distributed trace
```

Это помогает понять, в каком сервисе или dependency запрос затормозил.

---

## 16. Security

### Маскирование паролей

Пароли нельзя писать в логи.

Если пароль случайно попал в лог, его нужно маскировать:

```text
password=****
```

И потом ротировать secret.

---

### Маскирование токенов

Tokens тоже нельзя логировать.

Опасны:

- API tokens;
    
- JWT;
    
- OAuth tokens;
    
- cloud credentials;
    
- session cookies.
    

В логах должны быть только masked values.

---

### Маскирование персональных данных

Персональные данные нужно маскировать или минимизировать.

Например:

- email;
    
- phone;
    
- address;
    
- passport data;
    
- payment data.
    

Логи часто имеют широкий доступ, поэтому PII опасна.

---

### Доступ к логам

Доступ к логам должен быть ограничен.

Причины:

- там могут быть user data;
    
- internal errors;
    
- IP addresses;
    
- security events;
    
- business data.
    

Нужны RBAC, teams, audit и least privilege.

---

### Audit logs

Audit logs особенно чувствительные.

Они показывают действия пользователей и систем.

Их нужно:

- защищать от удаления;
    
- хранить дольше;
    
- ограничивать по доступу;
    
- использовать для расследований.
    

---

### Шифрование логов

Логи желательно шифровать:

- in transit;
    
- at rest.
    

Например:

- TLS между agent и storage;
    
- disk/object storage encryption;
    
- encrypted backups.
    

---

### Retention policy

Retention policy определяет, сколько хранить логи.

Важно балансировать:

- стоимость;
    
- compliance;
    
- security;
    
- usefulness для расследований.
    

---

### Почему нельзя логировать secrets

Secrets в логах опасны, потому что:

- логи читают многие люди;
    
- логи уходят в сторонние системы;
    
- логи долго хранятся;
    
- их сложно полностью удалить;
    
- backups тоже могут содержать secret.
    

Если secret попал в лог — его нужно считать скомпрометированным.

---

## 17. Troubleshooting

### Логи не собираются

Проверить:

- log agent запущен;
    
- есть права читать файлы;
    
- правильный path;
    
- DaemonSet работает на Node;
    
- container пишет в stdout/stderr;
    
- нет ошибок в agent logs.
    

---

### Логи не появляются в хранилище

Проверить:

- output config;
    
- network до storage;
    
- credentials;
    
- TLS;
    
- rate limits;
    
- ошибки в logs agent;
    
- storage health.
    

---

### Ошибка парсинга

Причины:

- формат лога изменился;
    
- regex не подходит;
    
- JSON невалидный;
    
- timestamp в неправильном формате;
    
- multiline logs не настроены.
    

Лучше использовать structured JSON logs вместо сложного regex.

---

### Неправильный timestamp

Если timestamp неправильный, логи могут появляться не в том времени.

Проверить:

- timezone;
    
- формат timestamp;
    
- clock sync;
    
- parser config;
    
- source field.
    

---

### Потеря логов

Причины:

- нет buffering;
    
- storage недоступен;
    
- agent restart;
    
- переполнен buffer;
    
- слишком высокий объем логов;
    
- Pod удалился до сбора логов.
    

Решение — buffering, backpressure, retries, capacity planning.

---

### Дублирование логов

Причины:

- два агента читают один файл;
    
- неправильный offset tracking;
    
- retry без deduplication;
    
- пересбор логов после restart;
    
- несколько pipelines отправляют в одно место.
    

---

### Log agent использует много CPU

Причины:

- тяжелые regex;
    
- слишком много логов;
    
- parsing на Node;
    
- multiline processing;
    
- compression;
    
- слишком много outputs.
    

Решение — упростить parsing, уменьшить volume, использовать JSON logs.

---

### Log agent использует много памяти

Причины:

- большой buffer;
    
- output недоступен;
    
- много multiline logs;
    
- слишком много files/streams;
    
- backpressure.
    

Проверить buffer settings и состояние storage.

---

### Elasticsearch cluster red/yellow

Yellow — replicas не размещены.

Red — часть primary shards недоступна.

Проверить:

- disk space;
    
- node health;
    
- shard allocation;
    
- cluster health;
    
- index settings;
    
- количество replicas.
    

---

### Заканчивается место на диске

Причины:

- слишком большой retention;
    
- много логов;
    
- много shards;
    
- нет ILM;
    
- debug logs включены;
    
- replicas слишком много.
    

Решения:

- уменьшить retention;
    
- включить ILM;
    
- удалить старые индексы;
    
- снизить объем логов;
    
- увеличить storage.
    

---

### Слишком много shards

Слишком много shards перегружает Elasticsearch.

Проблемы:

- много memory overhead;
    
- медленный cluster state;
    
- плохая производительность queries;
    
- долгие recovery.
    

Нужно укрупнять индексы и правильно настраивать rollover.

---

### Loki query работает медленно

Причины:

- слишком большой time range;
    
- слишком широкий selector;
    
- мало labels в selector;
    
- высокая cardinality;
    
- тяжелый regex;
    
- много log volume.
    

Решение — сузить labels и time range, избегать широких `{}` queries.

---

### Высокая cardinality labels

В Loki высокая cardinality labels создает много streams.

Плохие labels:

- request_id;
    
- user_id;
    
- trace_id;
    
- timestamp.
    

Решение — оставить эти данные в log line, а labels держать стабильными.

---

### Слишком большой объём логов

Причины:

- DEBUG включен в production;
    
- слишком подробные access logs;
    
- healthchecks логируются;
    
- повторяющиеся ошибки;
    
- noisy services.
    

Решения:

- уменьшить log level;
    
- sampling;
    
- drop filters;
    
- не логировать healthcheck;
    
- исправить spammy error.
    

---

## 18. Практика

### Посмотреть системные логи через `journalctl`

Команды:

```bash
journalctl -u nginx
journalctl -u kubelet -f
journalctl --since "30 minutes ago"
```

Практика: найти ошибки конкретного systemd service.

---

### Настроить `logrotate`

Создать config:

```text
/var/log/myapp/*.log {
  daily
  rotate 7
  compress
  missingok
  notifempty
}
```

Проверить:

```bash
logrotate -d /etc/logrotate.conf
```

---

### Посмотреть логи Docker-контейнера

```bash
docker ps
docker logs <container>
docker logs -f --tail 100 <container>
```

Практика: найти ошибку запуска контейнера.

---

### Посмотреть логи Kubernetes Pod

```bash
kubectl logs <pod>
kubectl logs -f <pod>
kubectl logs <pod> -c <container>
kubectl logs <pod> --previous
```

---

### Развернуть EFK stack

Практика:

- Elasticsearch/OpenSearch как storage;
    
- Fluent Bit как DaemonSet;
    
- Kibana/OpenSearch Dashboards как UI.
    

Проверить, что логи Pod'ов появились в UI.

---

### Собрать Kubernetes-логи через Fluent Bit

Fluent Bit обычно читает:

```text
/var/log/containers/*.log
```

И отправляет в Elasticsearch, Loki или другой backend.

---

### Найти ошибку в Kibana

Пример поиска:

```text
level: "error" and service: "api"
```

Сузить по time range и namespace.

---

### Развернуть Loki

В Kubernetes Loki часто ставят через Helm.

Практика:

- установить Loki;
    
- установить агент;
    
- подключить Grafana;
    
- проверить LogQL.
    

---

### Подключить Loki к Grafana

В Grafana добавить Data Source:

```text
Type: Loki
URL: http://loki:3100
```

Проверить запросом в Explore.

---

### Выполнить базовые LogQL-запросы

Примеры:

```logql
{namespace="default"}
```

```logql
{app="api"} |= "error"
```

```logql
count_over_time({app="api"} |= "error" [5m])
```

---

### Создать alert на основе логов

Пример идеи:

```logql
count_over_time({app="api"} |= "panic" [5m]) > 0
```

Alert должен быть важным и не шумным.

---

### Найти ошибку по correlation ID

Если в логах есть correlation ID:

```text
correlation_id=abc-123
```

Ищем:

```logql
{app="api"} |= "abc-123"
```

Или в Kibana:

```text
correlation_id: "abc-123"
```

---

## 19. Interview Questions

### Зачем нужно централизованное логирование

Чтобы искать логи из всех сервисов и серверов в одном месте.

Также это нужно, потому что Pod/VM может умереть, а локальные логи могут потеряться.

---

### ELK vs EFK

ELK:

```text
Elasticsearch + Logstash + Kibana
```

EFK:

```text
Elasticsearch + Fluentd/Fluent Bit + Kibana
```

В Kubernetes чаще используют Fluent Bit/Fluentd.

---

### Elasticsearch vs Loki

Elasticsearch индексирует полный текст и поля, дает мощный поиск, но требует больше ресурсов.

Loki индексирует labels и хранит log lines в chunks, обычно дешевле для больших объемов логов.

---

### Fluentd vs Fluent Bit

Fluentd гибче, но тяжелее.

Fluent Bit легче и чаще используется как агент на каждой Kubernetes Node.

---

### Что такое structured logging

Structured logging — логи в формате с полями, например JSON.

Плюсы:

- легче парсить;
    
- легче фильтровать;
    
- легче строить alerts/dashboards;
    
- лучше для distributed systems.
    

---

### Зачем писать логи в `stdout`

В контейнерах stdout/stderr — стандартный способ отдавать логи наружу.

Docker/Kubernetes автоматически собирают эти потоки.

Так не нужно лезть внутрь контейнера за файлами.

---

### Как собираются логи в Kubernetes

Обычно:

- приложение пишет в stdout/stderr;
    
- container runtime пишет логи на Node;
    
- log agent DaemonSet читает файлы логов;
    
- agent добавляет Kubernetes metadata;
    
- отправляет в Loki/Elasticsearch.
    

---

### Почему нельзя хранить логи только внутри Pod

Pod может быть удален или пересоздан.

Файлы внутри Pod исчезнут вместе с ним.

Для production логи нужно отправлять во внешнее хранилище.

---

### Что такое shard и replica

Shard — часть Elasticsearch index.

Replica — копия shard.

Shard нужен для распределения данных, replica — для отказоустойчивости и read scaling.

---

### Как Loki хранит логи

Loki группирует логи в streams по labels.

Сами log lines складываются в chunks, а index хранит labels и ссылки на chunks.

---

### Что такое LogQL

LogQL — язык запросов Loki.

Он позволяет выбирать log streams по labels, фильтровать строки и строить метрики из логов.

---

### Почему высокая cardinality опасна

Высокая cardinality создает слишком много уникальных series/streams/fields.

Это увеличивает RAM, CPU, disk usage и замедляет queries.

В Loki особенно опасны high-cardinality labels.

---

### Как найти ошибку конкретного запроса

Лучший способ — искать по request ID, correlation ID или trace ID.

Порядок:

- взять ID из ответа/header/user report;
    
- найти все логи по этому ID;
    
- посмотреть ошибки;
    
- перейти в trace, если есть tracing.
    

---

### Что нельзя записывать в логи

Нельзя логировать:

- passwords;
    
- tokens;
    
- private keys;
    
- cookies/session IDs;
    
- payment data;
    
- персональные данные без необходимости;
    
- secrets из env/config.
    

Если secret попал в лог, его нужно считать скомпрометированным.