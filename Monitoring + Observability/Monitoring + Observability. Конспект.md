## 1. Основы

### Что такое Monitoring

Monitoring — сбор и проверка данных о состоянии системы.

Обычно мониторят:

- CPU;
    
- memory;
    
- disk;
    
- network;
    
- errors;
    
- latency;
    
- availability.
    

Цель — понять, работает ли система нормально.

---

### Что такое Observability

Observability — способность понять внутреннее состояние системы по внешним сигналам.

Основные сигналы:

- metrics;
    
- logs;
    
- traces;
    
- events.
    

Observability помогает не только видеть проблему, но и искать причину.

---

### Monitoring vs Observability

Monitoring отвечает на вопрос:

```text
Что сломалось?
```

Observability помогает ответить:

```text
Почему это сломалось?
```

Monitoring больше про известные проблемы, observability — про расследование неизвестных проблем.

---

### Зачем нужен мониторинг

Мониторинг нужен, чтобы:

- быстро узнать о проблеме;
    
- видеть состояние production;
    
- находить bottlenecks;
    
- измерять reliability;
    
- строить alerting;
    
- анализировать инциденты.
    

---

### Reactive vs Proactive monitoring

Reactive monitoring — реагируем, когда уже что-то сломалось.

Proactive monitoring — заранее видим признаки проблемы.

Пример:

```text
disk 99% full  -> reactive
disk растет и будет full через 2 часа -> proactive
```

---

### Production monitoring

Production monitoring — мониторинг реальных сервисов с пользователями.

Важно мониторить:

- availability;
    
- latency;
    
- error rate;
    
- saturation;
    
- бизнес-метрики;
    
- инфраструктуру.
    

---

## 2. Основные сигналы Observability

### Metrics

Metrics — числовые показатели во времени.

Примеры:

- CPU usage;
    
- request count;
    
- error rate;
    
- latency;
    
- memory usage.
    

Хорошо подходят для dashboards и alerts.

---

### Logs

Logs — текстовые события от приложения или системы.

Примеры:

```text
user login failed
database connection timeout
payment request error
```

Logs помогают понять детали конкретной ошибки.

---

### Traces

Traces показывают путь запроса через несколько сервисов.

Например:

```text
frontend -> api -> auth -> database
```

Полезно для микросервисов и поиска latency bottleneck.

---

### Events

Events — важные события в системе.

Примеры:

- deploy;
    
- restart;
    
- node down;
    
- pod killed;
    
- config changed;
    
- autoscaling event.
    

Events помогают связать проблему с изменениями.

---

## 3. Metrics

### Что такое metric

Metric — числовое значение, которое измеряется во времени.

Пример:

```text
http_requests_total = 15234
cpu_usage = 73%
memory_used = 1.5GB
```

---

### Time series

Time series — последовательность значений metric во времени.

Например:

```text
cpu_usage{host="web1"} 70% at 10:00
cpu_usage{host="web1"} 75% at 10:01
cpu_usage{host="web1"} 80% at 10:02
```

---

### Labels

Labels — дополнительные метки у metrics.

Пример:

```text
http_requests_total{method="GET", status="500", service="api"}
```

Labels помогают фильтровать и группировать метрики.

---

### Counters

Counter — metric, которая только растет.

Примеры:

- количество HTTP-запросов;
    
- количество ошибок;
    
- количество обработанных jobs.
    

```text
http_requests_total
```

Для скорости роста используют rate.

---

### Gauges

Gauge — metric, которая может расти и падать.

Примеры:

- CPU usage;
    
- memory usage;
    
- queue size;
    
- active connections.
    

```text
node_memory_used_bytes
```

---

### Histograms

Histogram измеряет распределение значений по bucket'ам.

Часто используется для latency.

Пример:

```text
http_request_duration_seconds_bucket
```

По histogram можно считать percentiles типа p95/p99.

---

### Summaries

Summary тоже измеряет распределение значений.

Может считать quantiles на стороне приложения.

В Prometheus чаще используют histograms, потому что они лучше агрегируются.

---

## 4. Что обычно мониторят

### CPU

CPU показывает загрузку процессора.

Важно смотреть:

- общий usage;
    
- load average;
    
- throttling;
    
- CPU steal;
    
- per-process usage.
    

Высокий CPU может означать bottleneck или нормальную высокую нагрузку.

---

### Memory

Memory показывает использование RAM.

Важно смотреть:

- used memory;
    
- available memory;
    
- swap;
    
- OOM kills;
    
- memory leaks.
    

Не путать used memory и реально доступную память с учетом page cache.

---

### Disk usage

Disk usage показывает заполнение filesystem.

Команды:

```bash
df -h
df -i
```

Важно мониторить и место, и inodes.

---

### Disk I/O

Disk I/O показывает нагрузку на диск.

Смотреть:

- read/write throughput;
    
- IOPS;
    
- latency;
    
- disk utilization;
    
- queue.
    

Высокий disk latency может тормозить всё приложение.

---

### Network

Network metrics:

- traffic in/out;
    
- packet loss;
    
- errors;
    
- drops;
    
- latency;
    
- connections.
    

Полезно при сетевых проблемах и перегрузках.

---

### Processes

Мониторят процессы:

- жив ли процесс;
    
- сколько CPU/RAM ест;
    
- сколько файлов открыл;
    
- сколько threads/processes создал.
    

Для Linux полезны `ps`, `top`, `pidstat`.

---

### Containers

Для containers мониторят:

- CPU;
    
- memory;
    
- restarts;
    
- exit codes;
    
- network;
    
- filesystem usage;
    
- OOMKilled.
    

В Kubernetes важно смотреть Pod/container metrics вместе с events.

---

### Kubernetes resources

В Kubernetes мониторят:

- Pods;
    
- Deployments;
    
- Nodes;
    
- PVC;
    
- Services;
    
- restarts;
    
- readiness;
    
- scheduling issues;
    
- resource requests/limits.
    

---

### Application metrics

Application metrics — метрики самого приложения.

Примеры:

- request rate;
    
- error rate;
    
- latency;
    
- active users;
    
- queue length;
    
- DB query time;
    
- cache hit rate.
    

Они часто важнее голых CPU/RAM.

---

## 5. Golden Signals

### Latency

Latency — время обработки запроса.

Важно смотреть не только average, но и percentiles:

```text
p50
p95
p99
```

p99 часто показывает проблемы, которые average скрывает.

---

### Traffic

Traffic — объем нагрузки на сервис.

Примеры:

- requests per second;
    
- messages per second;
    
- jobs per minute;
    
- bytes per second.
    

Traffic помогает понять, выросла ли нагрузка.

---

### Errors

Errors — количество или процент неуспешных запросов.

Примеры:

- HTTP 5xx;
    
- failed jobs;
    
- exceptions;
    
- timeouts.
    

Error rate часто важнее абсолютного количества ошибок.

---

### Saturation

Saturation — насколько ресурс близок к пределу.

Примеры:

- CPU почти 100%;
    
- memory почти закончилась;
    
- disk full;
    
- queue растет;
    
- connection pool заполнен.
    

Saturation показывает, что система упирается в лимит.

---

## 6. USE Method

### Utilization

Utilization — насколько ресурс используется.

Примеры:

- CPU usage;
    
- memory usage;
    
- disk usage;
    
- network bandwidth usage.
    

Вопрос:

```text
Насколько ресурс занят?
```

---

### Saturation

Saturation — есть ли очередь или ожидание ресурса.

Примеры:

- run queue;
    
- disk queue;
    
- network queue;
    
- connection pool waiting.
    

Вопрос:

```text
Есть ли очередь на ресурс?
```

---

### Errors

Errors — ошибки при работе ресурса.

Примеры:

- disk errors;
    
- network drops;
    
- interface errors;
    
- failed operations.
    

Вопрос:

```text
Есть ли ошибки на уровне ресурса?
```

---

## 7. RED Method

### Rate

Rate — сколько запросов обрабатывает сервис.

Пример:

```text
requests per second
```

Это показывает нагрузку на сервис.

---

### Errors

Errors — сколько запросов завершается ошибкой.

Например:

- HTTP 500;
    
- timeout;
    
- failed operation;
    
- exception.
    

---

### Duration

Duration — сколько времени занимает обработка запроса.

Обычно смотрят latency percentiles:

```text
p50
p95
p99
```

RED хорошо подходит для HTTP/API сервисов.

---

## 8. Alerting

### Что такое Alert

Alert — уведомление о проблеме или риске проблемы.

Например:

```text
High error rate
Disk almost full
Pod restarts too often
Service is down
```

Alert должен требовать действия.

---

### Alert rules

Alert rule — условие, при котором срабатывает alert.

Пример:

```text
error_rate > 5% for 5 minutes
```

Хорошее правило учитывает время, чтобы не шуметь из-за коротких spikes.

---

### Threshold-based alerts

Threshold-based alert срабатывает при превышении порога.

Примеры:

- CPU > 90%;
    
- disk > 85%;
    
- errors > 5%;
    
- latency p95 > 500ms.
    

Просто настроить, но можно получить много ложных алертов.

---

### Anomaly detection basics

Anomaly detection ищет необычное поведение.

Например:

- traffic резко упал;
    
- latency нетипично выросла;
    
- error rate отличается от обычного паттерна.
    

Сложнее threshold alerts, но полезно для динамических систем.

---

### Alert fatigue

Alert fatigue — усталость от слишком большого количества алертов.

Если алертов много и они бесполезные, люди начинают их игнорировать.

Плохой alert хуже, чем отсутствие alert.

---

### Alert severity

Severity показывает важность alert.

Пример:

```text
info
warning
critical
```

Severity помогает понять, насколько срочно реагировать.

---

### Warning vs Critical

Warning — проблема может стать серьезной, но сервис еще работает.

Critical — уже есть сильное влияние на пользователей или production.

Пример:

```text
disk 80% -> warning
disk 95% -> critical
```

---

### Escalation

Escalation — передача alert на следующий уровень, если никто не отреагировал.

Например:

```text
on-call engineer -> team lead -> incident manager
```

Используется в production support.

---

## 9. SLI / SLO / SLA

### Что такое SLI

SLI — Service Level Indicator.

Это метрика качества сервиса.

Примеры:

- availability;
    
- latency;
    
- error rate;
    
- request success rate.
    

```text
SLI = что измеряем
```

---

### Что такое SLO

SLO — Service Level Objective.

Это цель по SLI.

Пример:

```text
99.9% successful requests за 30 дней
p95 latency < 300ms
```

```text
SLO = какой уровень хотим держать
```

---

### Что такое SLA

SLA — Service Level Agreement.

Это внешнее соглашение с пользователями/клиентами.

Если SLA нарушен, могут быть компенсации или юридические последствия.

```text
SLA = обещание клиенту
```

---

### Error Budget basics

Error Budget — допустимый объем ошибок.

Если SLO 99.9%, то error budget — 0.1%.

Идея:

```text
пока error budget есть -> можно быстрее релизить
error budget сгорел -> фокус на reliability
```

---

## 10. Monitoring Architecture

### Application

Application должна отдавать logs и metrics.

Например:

- `/metrics` для Prometheus;
    
- structured JSON logs;
    
- trace spans.
    

Без instrumented app observability будет слабой.

---

### Exporter

Exporter собирает metrics из системы и отдает их в нужном формате.

Примеры:

- Node Exporter;
    
- postgres_exporter;
    
- nginx_exporter;
    
- blackbox_exporter.
    

---

### Collector

Collector собирает telemetry data и отправляет дальше.

Пример — OpenTelemetry Collector.

Он может принимать:

- metrics;
    
- logs;
    
- traces.
    

---

### Storage

Storage хранит monitoring data.

Примеры:

- Prometheus TSDB;
    
- Elasticsearch;
    
- Loki;
    
- VictoriaMetrics;
    
- Thanos;
    
- Mimir.
    

Выбор зависит от типа данных и retention.

---

### Visualization

Visualization — dashboards и графики.

Самый популярный инструмент — Grafana.

Dashboards помогают быстро понять состояние системы.

---

### Alerting

Alerting проверяет rules и отправляет уведомления.

Примеры:

- Prometheus alert rules;
    
- Alertmanager;
    
- Grafana Alerting;
    
- cloud alerting.
    

---

## 11. Tools Overview

### Prometheus

Prometheus — система сбора и хранения metrics.

Особенности:

- pull model;
    
- PromQL;
    
- labels;
    
- alert rules;
    
- service discovery.
    

Очень часто используется в Kubernetes.

---

### Grafana

Grafana — инструмент для dashboards и visualization.

Может подключаться к:

- Prometheus;
    
- Loki;
    
- Elasticsearch;
    
- InfluxDB;
    
- cloud monitoring.
    

---

### Alertmanager

Alertmanager принимает alerts от Prometheus и управляет уведомлениями.

Он умеет:

- grouping;
    
- routing;
    
- silencing;
    
- deduplication;
    
- escalation.
    

---

### Elasticsearch

Elasticsearch — search engine, часто используется для логов.

Обычно в связке:

```text
Filebeat/Logstash -> Elasticsearch -> Kibana
```

Подходит для поиска и анализа логов.

---

### Loki

Loki — система хранения логов от Grafana Labs.

Идея похожа на Prometheus labels, но для logs.

Часто используется с Promtail или Grafana Agent.

---

### OpenTelemetry basics

OpenTelemetry — стандарт для сбора telemetry data.

Поддерживает:

- traces;
    
- metrics;
    
- logs.
    

Часто используется через OpenTelemetry SDK и Collector.

---

## 12. Troubleshooting

### Высокий CPU

Проверить:

- какой процесс ест CPU;
    
- вырос ли traffic;
    
- нет ли бесконечного цикла;
    
- нет ли throttling;
    
- не идет ли GC/компиляция.
    

Команды:

```bash
top
htop
pidstat
```

---

### Memory leak

Признаки memory leak:

- memory usage постоянно растет;
    
- после нагрузки память не освобождается;
    
- появляются OOMKilled;
    
- растет heap.
    

Проверять нужно app metrics, heap dump, container memory, GC metrics.

---

### Disk full

Проверить:

```bash
df -h
du -sh /*
df -i
```

Причины:

- логи;
    
- cache;
    
- backups;
    
- Docker images;
    
- временные файлы;
    
- закончились inodes.
    

---

### Network latency

Проверить:

- latency между сервисами;
    
- DNS;
    
- packet loss;
    
- saturation;
    
- retries;
    
- firewall;
    
- load balancer.
    

Инструменты:

```bash
ping
traceroute
mtr
curl -w
```

---

### Рост ошибок приложения

Смотреть:

- error rate;
    
- logs;
    
- recent deploys;
    
- dependency errors;
    
- database;
    
- external API;
    
- saturation.
    

Важно понять: ошибки из-за кода, зависимости или инфраструктуры.

---

### Рост latency

Причины:

- вырос traffic;
    
- медленная БД;
    
- CPU throttling;
    
- network latency;
    
- locks;
    
- slow external API;
    
- garbage collection;
    
- queue backlog.
    

Смотреть p95/p99, а не только average.

---

### Ложные алерты

Ложные алерты появляются, если rule плохо настроен.

Причины:

- слишком низкий threshold;
    
- нет условия `for`;
    
- alert не требует действия;
    
- не учтены maintenance/deploy;
    
- мониторится симптом без impact.
    

---

## 13. Interview Questions

### Monitoring vs Observability

Monitoring говорит, что проблема есть.

Observability помогает понять, почему проблема произошла.

```text
Monitoring -> detect
Observability -> investigate
```

---

### Metrics vs Logs vs Traces

Metrics — числа во времени.

Logs — подробные события.

Traces — путь запроса через сервисы.

```text
metrics -> что происходит
logs    -> детали события
traces  -> где запрос тормозит/падает
```

---

### Что такое SLI/SLO/SLA

SLI — что измеряем.

SLO — какую цель хотим держать.

SLA — что обещаем клиенту.

Пример:

```text
SLI: availability
SLO: 99.9%
SLA: 99.5% в контракте
```

---

### Что такое Golden Signals

Golden Signals:

- Latency;
    
- Traffic;
    
- Errors;
    
- Saturation.
    

Это базовый набор сигналов для мониторинга сервиса.

---

### Что мониторить у сервиса

Минимум:

- availability;
    
- request rate;
    
- error rate;
    
- latency p95/p99;
    
- saturation;
    
- CPU/memory;
    
- dependency errors;
    
- restarts;
    
- business-critical metrics.
    

---

### Как понять причину инцидента по метрикам

Идти от симптома к причине:

```text
ошибки выросли
  ↓
latency выросла?
  ↓
traffic вырос?
  ↓
ресурсы уперлись?
  ↓
dependency деградировала?
  ↓
был deploy/change?
```

Метрики нужно сопоставлять с logs, traces и events.

---

### Почему много алертов — это плохо

Много алертов приводит к alert fatigue.

Люди начинают игнорировать уведомления.

Хороший alert должен быть:

- actionable;
    
- понятный;
    
- с правильной severity;
    
- связан с impact;
    
- без постоянного шума.