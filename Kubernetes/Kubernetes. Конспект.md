# Kubernetes

## 1. Основы

### Что такое Kubernetes

Kubernetes — система оркестрации контейнеров.

Он управляет:

- запуском контейнеров;

- масштабированием;

- сетью;

- конфигурацией;

- обновлениями;

- self-healing.


---

### Зачем нужен Kubernetes

Kubernetes нужен, чтобы управлять приложениями в production.

Он решает задачи:

- где запустить контейнер;

- что делать, если контейнер упал;

- как обновить приложение без downtime;

- как дать сервису постоянный DNS/IP;

- как масштабировать приложение.


---

### Kubernetes architecture

Упрощенно:

```text
Kubernetes Cluster
├── Control Plane
│   ├── kube-apiserver
│   ├── etcd
│   ├── scheduler
│   └── controllers
└── Worker Nodes
    ├── kubelet
    ├── kube-proxy
    └── container runtime
```

Control Plane управляет кластером, Worker Nodes запускают workload.

---

### Control Plane

Control Plane — управляющая часть Kubernetes.

Она принимает API-запросы, хранит состояние кластера и принимает решения.

Например:


- куда поставить Pod;

- сколько реплик должно работать;

- что пересоздать после падения.


---

### Worker Node

Worker Node — сервер, на котором реально запускаются Pods.

На node работают:

- kubelet;

- kube-proxy;

- container runtime.


---

### Cluster

Cluster — набор Control Plane и Worker Nodes.

```text
Cluster = Control Plane + Worker Nodes
```

Внутри кластера запускаются приложения и Kubernetes objects.

---

### Declarative configuration

Declarative configuration — ты описываешь желаемое состояние, а Kubernetes сам его достигает.

Например:

```yaml
replicas: 3
```

Ты не говоришь “запусти 3 Pod вручную”, ты говоришь “должно быть 3 Pod”.

---

### Desired state

Desired state — желаемое состояние системы.

Например:

- должно быть 3 реплики;

- image должен быть `nginx:1.25`;

- Service должен слушать порт 80.


Kubernetes постоянно сравнивает desired state с actual state.

---

### Reconciliation loop

Reconciliation loop — постоянный цикл сверки состояния.

```text
desired state != actual state
        ↓
Kubernetes исправляет
        ↓
actual state становится ближе к desired state
```

Это основа self-healing в Kubernetes.

---

### Kubernetes objects

Kubernetes objects — сущности, которыми управляет Kubernetes.

Примеры:

- Pod;

- Deployment;

- Service;

- ConfigMap;

- Secret;

- Ingress;

- StatefulSet;

- Job.


---

### Namespaces

Namespace — логическое разделение ресурсов внутри кластера.

Примеры:

```text
default
kube-system
monitoring
dev
prod
```

Namespaces помогают разделять окружения, команды и системные компоненты.

---

## 2. Control Plane Components

### kube-apiserver

`kube-apiserver` — главный вход в Kubernetes API.

Через него проходят:

- `kubectl`;

- controllers;

- scheduler;

- kubelet;

- external integrations.


Почти все компоненты общаются через API Server.

---

### etcd

`etcd` — key-value база данных Kubernetes.

В ней хранится состояние кластера:

- Pods;

- Deployments;

- Services;

- Secrets;

- ConfigMaps;

- Nodes.


Если потерять etcd без backup — можно потерять состояние кластера.

---

### kube-scheduler

`kube-scheduler` выбирает Node для нового Pod.

Он учитывает:

- requests/limits;

- nodeSelector;

- taints/tolerations;

- affinity;

- доступные ресурсы.


Scheduler не запускает Pod сам, он только назначает Node.

---

### kube-controller-manager

`kube-controller-manager` запускает controllers.

Controllers следят, чтобы actual state совпадал с desired state.

Например:

- Deployment controller;

- ReplicaSet controller;

- Node controller;

- Job controller.


---

### cloud-controller-manager

`cloud-controller-manager` интегрирует Kubernetes с cloud provider.

Например:

- создает cloud LoadBalancer;

- работает с cloud routes;

- управляет cloud volumes;

- обновляет информацию о nodes.


Обычно нужен в AWS/GCP/Azure и других cloud-средах.

---

### Как компоненты Control Plane взаимодействуют между собой

Упрощенно:

```text
kubectl -> kube-apiserver -> etcd
scheduler -> kube-apiserver
controllers -> kube-apiserver
kubelet -> kube-apiserver
```

Компоненты почти всегда общаются через API Server, а не напрямую друг с другом.

---

### High Availability Control Plane

HA Control Plane — отказоустойчивый Control Plane.

Обычно делают:

- несколько API Servers;

- несколько control plane nodes;

- etcd cluster;

- LoadBalancer перед API Server.


Цель — чтобы кластер продолжал работать при падении одной control plane node.

---

## 3. Node Components

### kubelet

`kubelet` — агент на каждой Worker Node.

Он:

- получает PodSpec от API Server;

- просит runtime запустить контейнеры;

- следит за состоянием Pods;

- отправляет статус обратно в Kubernetes.


---

### kube-proxy

`kube-proxy` отвечает за сетевые правила для Services.

Он настраивает правила, чтобы traffic на Service попадал к нужным Pods.

Обычно работает через:

- iptables;

- IPVS;

- nftables в некоторых реализациях.


---

### Container Runtime

Container Runtime запускает контейнеры.

Примеры:

- containerd;

- CRI-O.


Kubernetes через kubelet просит runtime создать container.

---

### CRI

CRI — Container Runtime Interface.

Это интерфейс между kubelet и container runtime.

```text
kubelet -> CRI -> containerd/CRI-O
```

Благодаря CRI Kubernetes может работать с разными runtimes.

---

### CNI

CNI — Container Network Interface.

Отвечает за сеть Pods.

CNI plugin создает:

- Pod IP;

- routes;

- network interfaces;

- network policies, если поддерживает.


Примеры:

- Calico;

- Cilium;

- Flannel.


---

### CSI

CSI — Container Storage Interface.

Отвечает за подключение storage к Kubernetes.

Через CSI Kubernetes работает с:

- cloud disks;

- network storage;

- block volumes;

- persistent volumes.


---

### Node status

Node status показывает состояние Node.

Посмотреть:

```bash
kubectl get nodes
kubectl describe node <node-name>
```

Там видно:

- Ready/NotReady;

- capacity;

- allocatable;

- conditions;

- running pods.


---

### Node conditions

Node conditions — состояния node.

Примеры:

- `Ready`;

- `MemoryPressure`;

- `DiskPressure`;

- `PIDPressure`;

- `NetworkUnavailable`.


Если Node `NotReady`, Kubernetes перестает нормально размещать на ней workload.

---

## 4. Pods

### Что такое Pod

Pod — минимальная единица запуска в Kubernetes.

Обычно в Pod находится один основной контейнер.

```text
Pod -> Container
```

Но Pod может содержать несколько контейнеров.

---

### Pod lifecycle

Жизненный цикл Pod:

```text
Pending -> Running -> Succeeded/Failed
```

Pod создается, планируется на Node, запускает контейнеры и затем завершается или работает постоянно.

---

### Pod phases

Основные phases:

- `Pending`;

- `Running`;

- `Succeeded`;

- `Failed`;

- `Unknown`.


Phase показывает общее состояние Pod.

---

### Containers в одном Pod

В одном Pod может быть несколько контейнеров.

Они:

- запускаются вместе;

- живут на одной Node;

- делят сеть;

- могут делить volumes.


Используется для sidecar-паттернов.

---

### Shared network namespace

Контейнеры внутри одного Pod имеют общую сеть.

Это значит:

- один IP на весь Pod;

- общий localhost;

- общие порты.


Например, sidecar может обращаться к app через `localhost`.

---

### Shared volumes

Контейнеры в одном Pod могут использовать общие volumes.

Например:

- app пишет файл;

- sidecar читает этот файл и отправляет куда-то дальше.


---

### Init Containers

Init Containers запускаются до основных контейнеров.

Они должны успешно завершиться, прежде чем стартует main container.

Примеры:

- дождаться базы;

- подготовить config;

- скачать файлы;

- выполнить миграции.


---

### Sidecar containers

Sidecar container — вспомогательный контейнер рядом с основным.

Примеры:

- log shipper;

- proxy;

- metrics exporter;

- service mesh proxy.


Он расширяет поведение основного приложения.

---

### Multi-container Pod

Multi-container Pod — Pod с несколькими контейнерами.

Используется, когда контейнеры тесно связаны и должны жить вместе.

Если сервисы независимы — лучше разные Pods.

---

### Static Pods

Static Pods создаются напрямую kubelet, без API Server.

Обычно манифесты лежат на node в специальной директории.

Часто control plane компоненты запускаются как static Pods.

---

### Pod restartPolicy

`restartPolicy` определяет, когда перезапускать контейнеры в Pod.

Варианты:

- `Always`;

- `OnFailure`;

- `Never`.


Для Deployment обычно используется `Always`.

---

### Pod termination

Pod termination — процесс остановки Pod.

Kubernetes:

- отправляет SIGTERM;

- ждет grace period;

- если процесс не завершился — отправляет SIGKILL.


---

### Graceful shutdown

Graceful shutdown — аккуратное завершение приложения.

Приложение должно:

- принять SIGTERM;

- перестать принимать новые запросы;

- завершить текущие запросы;

- закрыть соединения;

- выйти.


---

### Termination grace period

`terminationGracePeriodSeconds` — сколько Kubernetes ждет перед SIGKILL.

Пример:

```yaml
terminationGracePeriodSeconds: 30
```

Если приложение не завершилось за это время, его убьют принудительно.

---

## 5. Labels, Selectors и Annotations

### Labels

Labels — key-value метки на Kubernetes objects.

Пример:

```yaml
labels:
  app: nginx
  env: prod
```

Labels используются для выбора и группировки объектов.

---

### Label selectors

Label selector выбирает objects по labels.

Например:

```yaml
selector:
  app: nginx
```

Так Service или Deployment понимают, с какими Pods работать.

---

### MatchLabels

`matchLabels` — простой selector по точному совпадению labels.

```yaml
selector:
  matchLabels:
    app: nginx
```

Pod подходит, если у него есть `app: nginx`.

---

### MatchExpressions

`matchExpressions` — более гибкий selector.

Пример:

```yaml
matchExpressions:
  - key: env
    operator: In
    values:
      - prod
      - staging
```

Можно использовать операторы `In`, `NotIn`, `Exists`, `DoesNotExist`.

---

### Annotations

Annotations — metadata для объектов.

Они похожи на labels, но не используются для выбора objects.

Примеры:

- описание;

- настройки ingress controller;

- данные для external tools;

- checksum config.


---

### Как Service находит Pods

Service находит Pods через selector.

```yaml
selector:
  app: nginx
```

Все Pods с label `app=nginx` становятся endpoints этого Service.

---

### Как Deployment управляет Pods

Deployment управляет Pods через ReplicaSet.

Схема:

```text
Deployment -> ReplicaSet -> Pods
```

Deployment создает ReplicaSet, а ReplicaSet поддерживает нужное количество Pods.

---

## 6. ReplicaSet и Deployment

### ReplicaSet

ReplicaSet следит, чтобы работало нужное количество Pod replicas.

Например, если нужно 3 Pod, а один упал — ReplicaSet создаст новый.

---

### Deployment

Deployment — объект для управления stateless-приложениями.

Он управляет:

- ReplicaSet;

- replicas;

- rolling updates;

- rollback;

- rollout history.


Обычно приложения запускают именно через Deployment, а не напрямую через Pod.

---

### Replicas

`replicas` — количество Pod copies.

```yaml
replicas: 3
```

Kubernetes будет поддерживать 3 работающих Pod.

---

### Rolling Update

Rolling Update обновляет приложение постепенно.

Старые Pods заменяются новыми без полной остановки сервиса.

Это стандартная стратегия Deployment.

---

### Recreate strategy

Recreate strategy сначала удаляет старые Pods, потом создает новые.

Минус — возможен downtime.

Применяется, когда нельзя запускать старую и новую версию одновременно.

---

### maxSurge

`maxSurge` — сколько дополнительных Pods можно создать сверх desired replicas во время update.

Например:

```yaml
maxSurge: 1
```

Если replicas 3, временно может быть 4 Pods.

---

### maxUnavailable

`maxUnavailable` — сколько Pods может быть недоступно во время update.

Например:

```yaml
maxUnavailable: 1
```

Если replicas 3, минимум 2 должны оставаться доступными.

---

### Rollout status

Проверить статус rollout:

```bash
kubectl rollout status deployment/myapp
```

Показывает, завершилось ли обновление Deployment.

---

### Rollout history

Посмотреть историю rollout:

```bash
kubectl rollout history deployment/myapp
```

Полезно для анализа изменений и rollback.

---

### Rollback

Откатить Deployment:

```bash
kubectl rollout undo deployment/myapp
```

Можно откатиться на предыдущую revision.

---

### Revision

Revision — версия Deployment rollout.

Каждое изменение Pod template создает новую revision.

Например, изменение image создает новую revision.

---

### Pause и resume rollout

Можно поставить rollout на паузу:

```bash
kubectl rollout pause deployment/myapp
```

Продолжить:

```bash
kubectl rollout resume deployment/myapp
```

Полезно, если нужно внести несколько изменений перед rollout.

---

## 7. StatefulSet

### Что такое StatefulSet

StatefulSet — controller для stateful-приложений.

Он дает Pods:

- стабильные имена;

- стабильную сетевую identity;

- отдельные volumes;

- упорядоченный запуск и остановку.


---

### StatefulSet vs Deployment

Deployment подходит для stateless-приложений.

StatefulSet нужен, когда важна идентичность Pod.

Например:

- базы данных;

- кластеры;

- очереди;

- системы с persistent state.


---

### Stable Pod names

Pods в StatefulSet имеют стабильные имена.

Пример:

```text
postgres-0
postgres-1
postgres-2
```

Если Pod пересоздать, имя сохранится.

---

### Stable network identity

StatefulSet дает стабильную DNS identity через Headless Service.

Пример:

```text
postgres-0.postgres.default.svc.cluster.local
```

Это важно для кластеров баз данных.

---

### Ordered deployment

StatefulSet запускает Pods по порядку.

Например:

```text
app-0 -> app-1 -> app-2
```

Следующий Pod обычно создается после готовности предыдущего.

---

### Ordered termination

StatefulSet останавливает Pods в обратном порядке.

```text
app-2 -> app-1 -> app-0
```

Это важно для некоторых stateful-систем.

---

### volumeClaimTemplates

`volumeClaimTemplates` автоматически создает PVC для каждого Pod.

Например:

- `postgres-0` получает свой volume;

- `postgres-1` получает свой volume.


Volumes сохраняются даже после пересоздания Pod.

---

### Headless Service

Headless Service — Service без ClusterIP.

```yaml
clusterIP: None
```

Он возвращает DNS-записи конкретных Pods, а не один общий Service IP.

---

### Stateful applications

Stateful applications хранят состояние.

Примеры:

- PostgreSQL;

- MySQL;

- Kafka;

- Redis cluster;

- Elasticsearch.


Для них важны disk, identity и порядок запуска.

---

### Update strategies

StatefulSet поддерживает update strategies.

Чаще всего:

- `RollingUpdate`;

- `OnDelete`.


`OnDelete` обновляет Pod только после ручного удаления старого Pod.

---

## 8. DaemonSet и Jobs

### DaemonSet

DaemonSet запускает Pod на каждой подходящей Node.

Если в cluster добавляется новая Node, DaemonSet создаст Pod и на ней.

---

### По одному Pod на Node

Главная идея DaemonSet:

```text
1 Node = 1 Pod
```

Или один Pod на каждую Node, которая подходит по selector/tolerations.

---

### Типичные применения DaemonSet

DaemonSet используют для node-level компонентов.

Примеры:

- log collector;

- monitoring agent;

- CNI plugin;

- storage agent;

- security agent.


---

### Job

Job запускает задачу до успешного завершения.

Примеры:

- миграция базы;

- batch processing;

- one-time script;

- backup.


Job не предназначен для постоянно работающего сервиса.

---

### CronJob

CronJob запускает Job по расписанию.

Пример:

```yaml
schedule: "0 2 * * *"
```

Подходит для backup, cleanup, reports.

---

### completions

`completions` — сколько успешных завершений нужно Job.

Например:

```yaml
completions: 5
```

Job считается выполненным, когда 5 Pods завершились успешно.

---

### parallelism

`parallelism` — сколько Pods Job может запускать одновременно.

Например:

```yaml
parallelism: 2
```

Значит одновременно могут работать 2 Pod.

---

### backoffLimit

`backoffLimit` — сколько раз Kubernetes будет повторять Job при ошибках.

```yaml
backoffLimit: 3
```

Если попытки закончились, Job считается failed.

---

### restartPolicy для Job

Для Job обычно используют:

```yaml
restartPolicy: OnFailure
```

Или:

```yaml
restartPolicy: Never
```

`Always` для Job не используется.

---

### CronJob concurrencyPolicy

`concurrencyPolicy` определяет, что делать, если предыдущий Job еще не завершился.

Варианты:

- `Allow`;

- `Forbid`;

- `Replace`.


---

### successfulJobsHistoryLimit

`successfulJobsHistoryLimit` — сколько успешных Jobs хранить в истории.

Например:

```yaml
successfulJobsHistoryLimit: 3
```

---

### failedJobsHistoryLimit

`failedJobsHistoryLimit` — сколько failed Jobs хранить в истории.

Например:

```yaml
failedJobsHistoryLimit: 1
```

Помогает не засорять namespace старыми Jobs.

---

## 9. Services

### Что такое Service

Service — стабильная точка доступа к Pods.

Pods могут пересоздаваться и менять IP, а Service остается постоянным.

```text
client -> Service -> Pods
```

---

### ClusterIP

ClusterIP — Service только внутри кластера.

Это тип Service по умолчанию.

```yaml
type: ClusterIP
```

Используется для внутреннего общения сервисов.

---

### NodePort

NodePort открывает порт на каждой Node.

```text
NodeIP:NodePort -> Service -> Pods
```

Диапазон обычно около `30000-32767`.

Используется редко напрямую, чаще через LoadBalancer/Ingress.

---

### LoadBalancer

LoadBalancer создает внешний cloud load balancer.

Типично для cloud provider.

```text
External LB -> Service -> Pods
```

Удобно для внешнего доступа к сервису.

---

### ExternalName

ExternalName создает DNS alias на внешний адрес.

Пример:

```yaml
type: ExternalName
externalName: database.example.com
```

Не создает proxy, просто возвращает DNS CNAME.

---

### Headless Service

Headless Service — Service без ClusterIP.

```yaml
clusterIP: None
```

DNS возвращает IP конкретных Pods.

Часто используется со StatefulSet.

---

### Service selector

Service selector выбирает Pods по labels.

```yaml
selector:
  app: nginx
```

Если selector не совпадает с labels Pod — Service не будет направлять traffic.

---

### Endpoints

Endpoints — список Pod IP и ports, куда Service может отправлять traffic.

Раньше это был основной объект для backend-адресов Service.

---

### EndpointSlice

EndpointSlice — современный способ хранить endpoints Service.

Он лучше масштабируется, чем старый Endpoints object.

Service использует EndpointSlices для понимания, куда направлять traffic.

---

### targetPort

`targetPort` — порт внутри Pod/container, куда Service отправляет traffic.

```yaml
targetPort: 8080
```

---

### port

`port` — порт самого Service.

```yaml
port: 80
targetPort: 8080
```

Клиент обращается к Service на `80`, а Pod получает traffic на `8080`.

---

### nodePort

`nodePort` — порт на Node для Service типа NodePort или LoadBalancer.

```yaml
nodePort: 30080
```

Доступ:

```text
<NodeIP>:30080
```

---

### Session affinity

Session affinity позволяет направлять запросы одного клиента на один и тот же backend Pod.

Пример:

```yaml
sessionAffinity: ClientIP
```

Используется редко, потому что лучше делать приложения stateless.

---

### externalTrafficPolicy

`externalTrafficPolicy` управляет обработкой внешнего traffic.

Варианты:

- `Cluster`;

- `Local`.


`Local` сохраняет original client IP, но отправляет traffic только на Pods на этой Node.

---

### internalTrafficPolicy

`internalTrafficPolicy` управляет внутренним traffic внутри кластера.

Варианты:

- `Cluster`;

- `Local`.


`Local` старается направлять traffic только на endpoints на той же Node.

## 10. Kubernetes Networking

### Kubernetes network model

В Kubernetes каждый Pod получает свой IP.

Базовая идея:

```text
Pod может общаться с Pod без NAT
Node может общаться с Pod
Pod может общаться с Service
```

Сеть реализуется через CNI plugin.

---

### Pod-to-Pod communication

Pod-to-Pod communication — общение Pod'ов напрямую по Pod IP.

Даже если Pods находятся на разных Nodes, CNI должен обеспечить связность.

```text
pod-a -> pod-b
```

---

### Pod-to-Service communication

Pod обычно обращается не к другому Pod напрямую, а к Service.

```text
Pod -> Service -> backend Pods
```

Service дает стабильный IP/DNS, даже если Pods пересоздаются.

---

### Node-to-Pod communication

Node должна иметь возможность достучаться до Pod IP.

Это нужно для:

- kubelet;

- probes;

- сетевой диагностики;

- traffic routing.


---

### CNI

CNI — Container Network Interface.

Это стандартный интерфейс для настройки сети контейнеров/Pod'ов.

Примеры CNI:

- Calico;

- Cilium;

- Flannel;

- Weave Net.


---

### Container network namespace

У каждого Pod есть свой network namespace.

Контейнеры внутри одного Pod делят:

- IP;

- localhost;

- network interfaces;

- ports.


---

### Pod IP

Pod IP — IP-адрес конкретного Pod.

Посмотреть:

```bash
kubectl get pods -o wide
```

Pod IP может измениться при пересоздании Pod.

---

### Service IP

Service IP — стабильный виртуальный IP Service.

Он не принадлежит конкретному Pod.

```text
Service IP -> kube-proxy/CNI -> Pod IPs
```

---

### ClusterIP

ClusterIP — внутренний IP Service внутри кластера.

Пример:

```yaml
type: ClusterIP
```

Используется для общения сервисов внутри Kubernetes.

---

### kube-proxy

`kube-proxy` настраивает правила, чтобы Service traffic попадал в backend Pods.

Он следит за Services и EndpointSlices.

---

### iptables mode

В iptables mode `kube-proxy` создает iptables-правила для Services.

Traffic на ClusterIP перенаправляется на один из Pod endpoints.

---

### IPVS mode

IPVS mode использует Linux IPVS для load balancing.

Обычно лучше масштабируется на большом количестве Services.

---

### eBPF basics

eBPF позволяет выполнять безопасные программы внутри Linux kernel.

В Kubernetes eBPF может использоваться для:

- networking;

- observability;

- security;

- замены kube-proxy.


Пример CNI с eBPF — Cilium.

---

### CoreDNS

CoreDNS — DNS-сервер внутри Kubernetes.

Он резолвит имена Services и Pods.

Обычно работает в namespace:

```text
kube-system
```

---

### Service DNS

Service получает DNS-имя.

Пример:

```text
my-service.default.svc.cluster.local
```

Из того же namespace часто достаточно:

```text
my-service
```

---

### Pod DNS

Pod тоже может иметь DNS-запись, но обычно обращаются не к Pod напрямую, а к Service.

Для StatefulSet важны стабильные DNS-имена Pod'ов через Headless Service.

---

### NetworkPolicy

NetworkPolicy ограничивает входящий и исходящий traffic между Pods.

Примерно как firewall внутри Kubernetes.

Важно: NetworkPolicy работает только если CNI plugin ее поддерживает.

---

### Ingress traffic

Ingress traffic — входящий traffic снаружи в кластер.

Обычно путь такой:

```text
Client -> LoadBalancer -> Ingress Controller -> Service -> Pod
```

---

### Egress traffic

Egress traffic — исходящий traffic из Pod наружу.

Например:

```text
Pod -> external API
Pod -> database outside cluster
Pod -> internet
```

Egress можно ограничивать через NetworkPolicy или CNI-функции.

---

## 11. Ingress

### Ingress

Ingress — Kubernetes object для HTTP/HTTPS-доступа к сервисам.

Он описывает правила маршрутизации traffic.

```text
host/path -> Service
```

---

### Ingress Controller

Ingress Controller — компонент, который реально обрабатывает Ingress.

Без Ingress Controller сам Ingress resource ничего не сделает.

Примеры:

- Nginx Ingress Controller;

- Traefik;

- HAProxy;

- AWS ALB Controller.


---

### Ingress resource

Ingress resource — YAML-объект с правилами маршрутизации.

Пример:

```yaml
rules:
  - host: app.example.com
    http:
      paths:
        - path: /
          backend:
            service:
              name: app
              port:
                number: 80
```

---

### Host-based routing

Host-based routing направляет traffic по доменному имени.

```text
app.example.com -> app-service
api.example.com -> api-service
```

---

### Path-based routing

Path-based routing направляет traffic по URL path.

```text
/app -> app-service
/api -> api-service
```

Удобно, когда несколько сервисов живут на одном домене.

---

### TLS termination

TLS termination — расшифровка HTTPS на Ingress Controller.

```text
Client HTTPS -> Ingress -> HTTP/HTTPS -> Service
```

Сертификат обычно хранится в Kubernetes Secret.

---

### Default backend

Default backend получает traffic, который не подошел ни под одно правило Ingress.

Например, можно отдавать 404 page.

---

### Nginx Ingress Controller

Nginx Ingress Controller — популярный Ingress Controller на базе Nginx.

Он читает Ingress objects и генерирует Nginx config.

---

### Ingress vs Service LoadBalancer

`Service LoadBalancer` обычно дает один внешний LB на Service.

Ingress позволяет через один внешний endpoint маршрутизировать много HTTP-сервисов.

```text
LoadBalancer -> один Service
Ingress -> много Services по host/path
```

---

### Gateway API basics

Gateway API — более современная и гибкая замена Ingress.

Основные идеи:

- Gateway;

- HTTPRoute;

- TLSRoute;

- разделение ролей infra/app teams.


---

### Troubleshooting Ingress

Проверять по порядку:

```bash
kubectl get ingress
kubectl describe ingress
kubectl get svc
kubectl get endpoints
kubectl logs -n ingress-nginx <controller-pod>
```

Частые проблемы:

- неправильный host;

- неправильный path;

- Service без endpoints;

- TLS Secret не найден;

- DNS не указывает на LB.


---

## 12. ConfigMap и Secret

### ConfigMap

ConfigMap хранит обычную конфигурацию приложения.

Например:

- URLs;

- feature flags;

- config files;

- non-sensitive settings.


---

### Secret

Secret хранит чувствительные данные.

Например:

- passwords;

- tokens;

- certificates;

- private keys.


Но Secret сам по себе не равен полноценному secret manager.

---

### Secret не является шифрованием

Kubernetes Secret по умолчанию хранится в base64.

Base64 — это не шифрование.

Для безопасности нужны:

- RBAC;

- encryption at rest;

- external secret manager;

- ограничение доступа.


---

### Environment variables

ConfigMap и Secret можно передавать как env variables.

```yaml
env:
  - name: APP_ENV
    valueFrom:
      configMapKeyRef:
        name: app-config
        key: APP_ENV
```

---

### envFrom

`envFrom` импортирует все ключи ConfigMap/Secret как env variables.

```yaml
envFrom:
  - configMapRef:
      name: app-config
  - secretRef:
      name: app-secret
```

---

### Volume mounts

ConfigMap и Secret можно монтировать как файлы.

```text
/etc/config/app.yaml
/etc/secrets/password
```

Удобно для приложений, которые читают конфиги из файлов.

---

### Projected volumes

Projected volume объединяет несколько источников в один volume.

Например:

- ConfigMap;

- Secret;

- Downward API;

- ServiceAccount token.


---

### Immutable ConfigMap и Secret

Immutable ConfigMap/Secret нельзя изменить после создания.

```yaml
immutable: true
```

Плюсы:

- меньше случайных изменений;

- лучше производительность kube-apiserver;

- предсказуемость конфигурации.


---

### Обновление конфигурации

При изменении ConfigMap env variables в уже запущенном Pod не обновятся.

Обычно нужен restart Pod'ов.

Для mounted files обновление возможно, но приложение должно уметь перечитывать config.

---

### Secret rotation basics

Secret rotation — регулярная замена secret.

Обычно нужно:

- создать новый secret;

- обновить workload;

- перезапустить Pods;

- отключить старый secret.


---

### External Secrets basics

External Secrets — подход, когда Kubernetes Secret синхронизируется из внешнего secret manager.

Примеры:

- AWS Secrets Manager;

- HashiCorp Vault;

- GCP Secret Manager;

- Azure Key Vault.


---

### Sealed Secrets basics

Sealed Secrets позволяют хранить зашифрованные secrets в Git.

В Git лежит encrypted object, а в cluster controller расшифровывает его в обычный Secret.

---

## 13. Storage

### Volumes

Volume в Kubernetes — storage, подключенный к Pod.

Используется для:

- временных файлов;

- конфигов;

- persistent data;

- обмена файлами между containers в Pod.


---

### emptyDir

`emptyDir` создается при запуске Pod и удаляется вместе с Pod.

```yaml
volumes:
  - name: cache
    emptyDir: {}
```

Подходит для cache/temp данных.

---

### hostPath

`hostPath` монтирует путь с Node внутрь Pod.

```yaml
hostPath:
  path: /var/log
```

Опасен, потому что дает доступ к filesystem Node.

---

### PersistentVolume

PersistentVolume, или PV, — storage resource в кластере.

Это может быть:

- cloud disk;

- NFS;

- Ceph;

- локальный диск;

- CSI volume.


---

### PersistentVolumeClaim

PersistentVolumeClaim, или PVC, — запрос на storage.

Pod обычно использует PVC, а не PV напрямую.

```text
Pod -> PVC -> PV -> real storage
```

---

### StorageClass

StorageClass описывает тип storage и provisioner.

Например:

- fast SSD;

- standard HDD;

- encrypted disk;

- regional disk.


---

### Dynamic provisioning

Dynamic provisioning автоматически создает PV по PVC.

```text
PVC created -> CSI provisioner creates disk -> PV bound
```

Это стандартный cloud-native вариант.

---

### Static provisioning

Static provisioning — админ заранее создает PV вручную.

Потом PVC привязывается к подходящему PV.

---

### Access Modes

Access Modes описывают, как volume может быть подключен.

Основные:

- ReadWriteOnce;

- ReadOnlyMany;

- ReadWriteMany.


---

### ReadWriteOnce

ReadWriteOnce, или RWO, означает volume доступен на запись одной Node.

Часто используется для cloud disks.

---

### ReadOnlyMany

ReadOnlyMany, или ROX, означает volume можно подключить read-only на много Nodes.

Подходит для общих readonly данных.

---

### ReadWriteMany

ReadWriteMany, или RWX, означает volume можно подключить на запись нескольким Nodes.

Обычно нужен shared storage типа NFS/CephFS.

---

### Volume Binding Mode

Volume Binding Mode определяет, когда PVC привязывается к PV.

Варианты:

- `Immediate`;

- `WaitForFirstConsumer`.


`WaitForFirstConsumer` полезен, когда storage зависит от зоны Node.

---

### Reclaim Policy

Reclaim Policy определяет, что делать с PV после удаления PVC.

Основные варианты:

- `Retain`;

- `Delete`.


---

### Retain

`Retain` сохраняет PV и данные после удаления PVC.

Полезно, если данные нельзя случайно удалить.

---

### Delete

`Delete` удаляет PV и реальный storage после удаления PVC.

Часто используется для динамически созданных cloud disks.

---

### CSI

CSI — Container Storage Interface.

Через CSI Kubernetes подключает разные storage backend'ы.

Примеры:

- AWS EBS CSI;

- GCE PD CSI;

- Azure Disk CSI;

- Ceph CSI.


---

### Volume expansion

Volume expansion позволяет увеличить размер PVC.

```yaml
resources:
  requests:
    storage: 100Gi
```

Нужно, чтобы StorageClass поддерживал expansion.

---

### Volume snapshots basics

VolumeSnapshot — snapshot persistent volume.

Используется для:

- backup;

- restore;

- clone данных;

- миграций.


Работает через CSI snapshot support.

---

## 14. Resource Management

### CPU requests

CPU request — сколько CPU Kubernetes резервирует для Pod при scheduling.

```yaml
requests:
  cpu: "500m"
```

`500m` = половина CPU core.

---

### CPU limits

CPU limit — максимум CPU, который может использовать container.

```yaml
limits:
  cpu: "1"
```

При превышении CPU limit будет throttling, а не kill.

---

### Memory requests

Memory request — сколько памяти Kubernetes учитывает при scheduling.

```yaml
requests:
  memory: "512Mi"
```

Scheduler использует requests, чтобы выбрать Node.

---

### Memory limits

Memory limit — максимум памяти для container.

```yaml
limits:
  memory: "1Gi"
```

Если container превысит limit, возможен OOMKilled.

---

### Scheduling по requests

Scheduler размещает Pods по requests, а не по фактическому usage.

Если requests завышены — cluster используется плохо.

Если занижены — возможны перегрузки Node.

---

### CPU throttling

CPU throttling — ограничение CPU при достижении CPU limit.

Приложение не убивается, но может начать тормозить.

Часто проблема для latency-sensitive сервисов.

---

### OOMKilled

OOMKilled означает, что container превысил memory limit и был убит.

Проверить:

```bash
kubectl describe pod <pod>
```

Там будет reason `OOMKilled`.

---

### QoS classes

QoS class — класс качества обслуживания Pod.

Kubernetes использует QoS при нехватке ресурсов.

Основные:

- Guaranteed;

- Burstable;

- BestEffort.


---

### Guaranteed

Guaranteed — у всех containers заданы requests и limits, и они равны.

```text
request == limit
```

Такой Pod имеет самый высокий приоритет при eviction.

---

### Burstable

Burstable — requests/limits заданы, но не полностью или не равны.

Это самый частый класс для приложений.

---

### BestEffort

BestEffort — requests и limits не заданы.

Такие Pods первыми кандидаты на eviction при нехватке ресурсов.

---

### LimitRange

LimitRange задает дефолтные и максимальные requests/limits в namespace.

Помогает не запускать Pods без ограничений.

---

### ResourceQuota

ResourceQuota ограничивает суммарные ресурсы namespace.

Например:

- сколько CPU;

- сколько memory;

- сколько PVC;

- сколько Pods.


---

### Ephemeral storage requests и limits

Ephemeral storage — временное дисковое место container'а.

Можно задавать requests/limits:

```yaml
requests:
  ephemeral-storage: "1Gi"
limits:
  ephemeral-storage: "2Gi"
```

При превышении возможен eviction Pod.

---

## 15. Probes и Health Checks

### Liveness probe

Liveness probe проверяет, живо ли приложение.

Если liveness probe падает, Kubernetes перезапускает container.

Используется для зависших процессов.

---

### Readiness probe

Readiness probe проверяет, готов ли Pod принимать traffic.

Если readiness падает, Pod убирается из Service endpoints.

Container при этом не перезапускается.

---

### Startup probe

Startup probe используется для приложений с долгим стартом.

Пока startup probe не прошла, liveness/readiness могут не мешать запуску.

---

### HTTP probe

HTTP probe делает HTTP-запрос.

```yaml
httpGet:
  path: /health
  port: 8080
```

Подходит для web/API сервисов.

---

### TCP probe

TCP probe проверяет, открыт ли TCP port.

```yaml
tcpSocket:
  port: 5432
```

Проверяет только возможность соединения, не бизнес-логику.

---

### Exec probe

Exec probe выполняет команду внутри container.

```yaml
exec:
  command:
    - cat
    - /tmp/healthy
```

Если command возвращает 0 — probe успешна.

---

### initialDelaySeconds

`initialDelaySeconds` — задержка перед первой probe.

```yaml
initialDelaySeconds: 30
```

Нужно, чтобы приложение успело стартовать.

---

### periodSeconds

`periodSeconds` — как часто выполнять probe.

```yaml
periodSeconds: 10
```

Например, каждые 10 секунд.

---

### timeoutSeconds

`timeoutSeconds` — сколько ждать ответ probe.

```yaml
timeoutSeconds: 3
```

Если приложение отвечает дольше — probe считается failed.

---

### failureThreshold

`failureThreshold` — сколько failed probes подряд нужно для действия.

Для liveness после этого container будет перезапущен.

---

### successThreshold

`successThreshold` — сколько успешных probes подряд нужно, чтобы считать container healthy/ready.

Для readiness может быть полезно после временного сбоя.

---

### Неправильные probes

Плохие probes могут ломать production.

Примеры:

- слишком короткий timeout;

- liveness проверяет зависимость от БД;

- readiness всегда возвращает OK;

- endpoint слишком тяжелый.


---

### Probe-induced restart loop

Probe-induced restart loop — когда liveness probe сама вызывает постоянные рестарты.

Например, приложение стартует 60 секунд, а probe убивает его через 10 секунд.

Решение: настроить startup probe и нормальные delays.

---

## 16. Scheduling

### Как работает kube-scheduler

kube-scheduler выбирает Node для Pod.

Упрощенно:

- фильтрует неподходящие Nodes;

- оценивает подходящие Nodes;

- выбирает лучшую;

- записывает binding через API Server.


---

### nodeSelector

`nodeSelector` — простой выбор Node по labels.

```yaml
nodeSelector:
  disk: ssd
```

Pod попадет только на Node с label `disk=ssd`.

---

### Node affinity

Node affinity — более гибкий выбор Node.

Можно задавать обязательные и предпочтительные правила.

Например, запускать Pods только в определенной зоне.

---

### Pod affinity

Pod affinity просит scheduler размещать Pod рядом с другими Pods.

Например, app рядом с cache.

Используется редко, потому что может усложнить scheduling.

---

### Pod anti-affinity

Pod anti-affinity просит не размещать Pods рядом.

Например, не класть две реплики одного сервиса на одну Node.

Полезно для отказоустойчивости.

---

### Taints

Taint ставится на Node и отталкивает Pods.

```bash
kubectl taint nodes node1 dedicated=infra:NoSchedule
```

Без toleration Pod не попадет на такую Node.

---

### Tolerations

Toleration позволяет Pod запускаться на Node с taint.

```yaml
tolerations:
  - key: dedicated
    operator: Equal
    value: infra
    effect: NoSchedule
```

---

### Topology spread constraints

Topology spread constraints распределяют Pods по зонам, Nodes или другим доменам.

Например, равномерно по availability zones.

---

### nodeName

`nodeName` напрямую указывает Node для Pod.

```yaml
nodeName: worker-1
```

Обычно так делать не стоит, потому что это обходит scheduler.

---

### PriorityClass

PriorityClass задает приоритет Pod.

Высокоприоритетные Pods важнее при scheduling и preemption.

---

### Pod priority

Pod priority — числовой приоритет Pod из PriorityClass.

Чем выше значение, тем важнее Pod.

---

### Preemption

Preemption — вытеснение менее важных Pods ради более важных.

Если для high-priority Pod нет места, Kubernetes может удалить lower-priority Pods.

---

### Scheduler predicates и scoring на базовом уровне

Сначала scheduler фильтрует Nodes, где Pod не может запуститься.

Потом выставляет score подходящим Nodes.

```text
filter -> score -> bind
```

Так выбирается Node для Pod.

---

## 17. Autoscaling

### Horizontal Pod Autoscaler

HPA автоматически меняет количество replicas.

```text
нагрузка выросла -> replicas больше
нагрузка упала -> replicas меньше
```

Работает с Deployment, StatefulSet и похожими ресурсами.

---

### HPA по CPU

HPA может масштабировать Pods по CPU usage.

Пример:

```bash
kubectl autoscale deployment app --cpu-percent=70 --min=2 --max=10
```

Важно: для CPU HPA нужны CPU requests.

---

### HPA по memory

HPA может масштабировать по memory.

Но memory не всегда хороший сигнал для autoscaling, потому что память часто не уменьшается быстро.

---

### Custom metrics

Custom metrics — метрики приложения.

Например:

- requests per second;

- queue length;

- active users;

- latency.


Используются через metrics adapter.

---

### External metrics

External metrics приходят из внешних систем.

Например:

- длина очереди в cloud queue;

- Kafka lag;

- cloud monitoring metric.


---

### Metrics Server

Metrics Server собирает CPU/memory usage с kubelet.

Нужен для:

```bash
kubectl top pods
kubectl top nodes
```

И для HPA по CPU/memory.

---

### Vertical Pod Autoscaler basics

VPA подбирает requests/limits для Pod.

Он может рекомендовать или автоматически менять ресурсы.

Осторожно: изменение ресурсов может требовать restart Pod.

---

### Cluster Autoscaler

Cluster Autoscaler добавляет или удаляет Nodes.

Если Pods не могут запланироваться из-за нехватки ресурсов — он может увеличить cluster.

---

### KEDA basics

KEDA масштабирует workloads по event-driven метрикам.

Например:

- Kafka lag;

- RabbitMQ queue;

- Prometheus query;

- cloud queues.


Может масштабировать даже до нуля.

---

### HPA и requests

Для HPA по CPU важны CPU requests.

HPA считает utilization относительно request.

Если requests неправильные, autoscaling будет работать плохо.

---

### Scale-up и scale-down behavior

Scale-up обычно должен быть быстрым, чтобы пережить рост нагрузки.

Scale-down часто делают медленнее, чтобы избежать постоянного дергания replicas.

---

## 18. Security

### Authentication

Authentication отвечает на вопрос: “кто ты?”

В Kubernetes это может быть:

- certificate;

- token;

- OIDC;

- ServiceAccount token.


---

### Authorization

Authorization отвечает на вопрос: “что тебе можно делать?”

Например:

- можно ли читать Pods;

- можно ли создавать Secrets;

- можно ли удалять Deployments.


---

### RBAC

RBAC — Role-Based Access Control.

Он управляет доступами через:

- Role;

- ClusterRole;

- RoleBinding;

- ClusterRoleBinding.


---

### Role

Role задает права внутри одного namespace.

Например, разрешить читать Pods в namespace `dev`.

---

### ClusterRole

ClusterRole задает права на уровне всего кластера или для cluster-wide ресурсов.

Например:

- Nodes;

- PersistentVolumes;

- Namespaces.


---

### RoleBinding

RoleBinding привязывает Role или ClusterRole к пользователю/группе/ServiceAccount внутри namespace.

---

### ClusterRoleBinding

ClusterRoleBinding привязывает ClusterRole на уровне всего кластера.

С ним нужно быть осторожным.

---

### ServiceAccount

ServiceAccount — identity для Pod внутри Kubernetes.

Pod использует ServiceAccount для обращения к Kubernetes API.

---

### SecurityContext

SecurityContext задает security-настройки Pod или container.

Например:

- user;

- group;

- capabilities;

- read-only filesystem;

- privilege escalation.


---

### runAsUser

`runAsUser` задает UID, от которого будет работать container process.

```yaml
securityContext:
  runAsUser: 1000
```

---

### runAsNonRoot

`runAsNonRoot` запрещает запуск container от root.

```yaml
securityContext:
  runAsNonRoot: true
```

Хорошая базовая практика.

---

### fsGroup

`fsGroup` задает group ID для доступа к mounted volumes.

Полезно, когда приложение не root, но должно писать в volume.

---

### Linux capabilities

Capabilities — отдельные root-привилегии Linux.

Лучше удалять лишние capabilities.

```yaml
capabilities:
  drop:
    - ALL
```

---

### privileged container

Privileged container получает очень широкие права на host.

```yaml
securityContext:
  privileged: true
```

Это опасно и должно использоваться только при необходимости.

---

### allowPrivilegeEscalation

`allowPrivilegeEscalation` управляет возможностью повысить привилегии внутри container.

Лучше ставить:

```yaml
allowPrivilegeEscalation: false
```

---

### readOnlyRootFilesystem

`readOnlyRootFilesystem` делает root filesystem container read-only.

```yaml
readOnlyRootFilesystem: true
```

Это снижает риск изменения файлов приложения при компрометации.

---

### seccomp

seccomp ограничивает Linux syscalls.

В Kubernetes часто используют профиль:

```yaml
seccompProfile:
  type: RuntimeDefault
```

---

### Pod Security Standards

Pod Security Standards — набор уровней безопасности для Pods.

Основные уровни:

- Privileged;

- Baseline;

- Restricted.


`Restricted` — самый строгий стандарт.

---

### NetworkPolicy

NetworkPolicy ограничивает сетевой traffic между Pods.

Можно запретить все и разрешить только нужные направления.

Это принцип least privilege для сети.

---

### Admission Controllers

Admission Controllers проверяют или изменяют requests к Kubernetes API.

Например, могут запретить:

- privileged containers;

- images без trusted registry;

- Pods без limits;

- root containers.


---

### Image security basics

Базовая безопасность images:

- использовать trusted base images;

- сканировать на CVE;

- не хранить secrets;

- фиксировать версии;

- использовать минимальные images;

- подписывать images, если нужно.


---

### Secrets security

Secrets нужно защищать через:

- RBAC;

- encryption at rest;

- external secret manager;

- rotation;

- запрет лишнего доступа;

- не логировать secret values.


---

### Least privilege

Least privilege — давать минимально необходимые права.

В Kubernetes это касается:

- RBAC;

- ServiceAccounts;

- NetworkPolicies;

- Linux capabilities;

- access к Secrets;

- cloud IAM permissions.

## 19. kubectl

### `kubectl get`

Показывает список ресурсов.

```bash
kubectl get pods
kubectl get svc
kubectl get deploy
```

С namespace:

```bash
kubectl get pods -n prod
```

---

### `kubectl describe`

Показывает подробную информацию о ресурсе.

```bash
kubectl describe pod my-pod
```

Особенно полезно для troubleshooting, потому что показывает Events.

---

### `kubectl logs`

Показывает логи контейнера.

```bash
kubectl logs pod-name
```

Follow mode:

```bash
kubectl logs -f pod-name
```

Если в Pod несколько контейнеров:

```bash
kubectl logs pod-name -c container-name
```

---

### `kubectl exec`

Запускает команду внутри контейнера.

```bash
kubectl exec -it pod-name -- sh
```

Или:

```bash
kubectl exec -it pod-name -- bash
```

Полезно для debug.

---

### `kubectl apply`

Применяет YAML-манифест.

```bash
kubectl apply -f deployment.yaml
```

Это declarative-подход: Kubernetes приводит объект к описанному состоянию.

---

### `kubectl delete`

Удаляет ресурс.

```bash
kubectl delete pod my-pod
kubectl delete -f deployment.yaml
```

Удаление Deployment удалит связанные ReplicaSet/Pods через ownerReferences.

---

### `kubectl edit`

Открывает ресурс в редакторе и позволяет изменить его live.

```bash
kubectl edit deployment myapp
```

Удобно для быстрых правок, но в production лучше менять YAML в Git.

---

### `kubectl patch`

Меняет часть объекта без полного YAML.

```bash
kubectl patch deployment myapp -p '{"spec":{"replicas":5}}'
```

Полезно для точечных изменений и automation.

---

### `kubectl scale`

Меняет количество replicas.

```bash
kubectl scale deployment myapp --replicas=5
```

Работает с Deployment, ReplicaSet, StatefulSet.

---

### `kubectl rollout`

Управляет rollout Deployment.

```bash
kubectl rollout status deployment/myapp
kubectl rollout history deployment/myapp
kubectl rollout undo deployment/myapp
```

Используется для проверки обновлений и rollback.

---

### `kubectl top`

Показывает CPU/memory usage.

```bash
kubectl top pods
kubectl top nodes
```

Требует установленный Metrics Server.

---

### `kubectl explain`

Показывает документацию по Kubernetes fields.

```bash
kubectl explain deployment.spec
kubectl explain pod.spec.containers
```

Очень полезно при написании YAML.

---

### `kubectl config`

Управляет kubeconfig.

```bash
kubectl config get-contexts
kubectl config current-context
kubectl config use-context dev
```

Через него переключаются clusters/users/namespaces.

---

### `kubectl context`

Context — связка cluster + user + namespace.

Посмотреть contexts:

```bash
kubectl config get-contexts
```

Переключиться:

```bash
kubectl config use-context prod
```

---

### `kubectl port-forward`

Пробрасывает локальный порт к Pod/Service.

```bash
kubectl port-forward pod/my-pod 8080:80
```

Или к Service:

```bash
kubectl port-forward svc/my-service 8080:80
```

Удобно для локальной диагностики.

---

### `kubectl cp`

Копирует файлы между локальной машиной и Pod.

```bash
kubectl cp file.txt pod-name:/tmp/file.txt
kubectl cp pod-name:/tmp/log.txt ./log.txt
```

Часто требует `tar` внутри контейнера.

---

### `kubectl auth can-i`

Проверяет права доступа.

```bash
kubectl auth can-i get pods
kubectl auth can-i delete secrets -n prod
```

Для ServiceAccount:

```bash
kubectl auth can-i get pods --as system:serviceaccount:dev:my-sa
```

---

### JSONPath

JSONPath позволяет доставать конкретные поля из объектов.

```bash
kubectl get pod my-pod -o jsonpath='{.status.podIP}'
```

Полезно для скриптов.

---

### Custom columns

Custom columns выводят нужные поля таблицей.

```bash
kubectl get pods -o custom-columns=NAME:.metadata.name,IP:.status.podIP,NODE:.spec.nodeName
```

Удобно для быстрых отчетов.

---

### Field selectors

Field selector фильтрует объекты по полям.

```bash
kubectl get pods --field-selector status.phase=Running
```

Например, можно найти Pods на конкретной Node:

```bash
kubectl get pods --field-selector spec.nodeName=worker-1
```

---

### Label selectors

Label selector фильтрует объекты по labels.

```bash
kubectl get pods -l app=nginx
kubectl get pods -l env=prod,tier=backend
```

Очень часто используется для Service, Deployment и debug.

---

### Dry run

Dry run показывает результат без реального создания объекта.

```bash
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml
```

Server-side dry run:

```bash
kubectl apply -f app.yaml --dry-run=server
```

Полезно для проверки YAML.

---

## 20. YAML и API Objects

### apiVersion

`apiVersion` указывает версию Kubernetes API для объекта.

```yaml
apiVersion: apps/v1
```

Например:

- `v1`;

- `apps/v1`;

- `batch/v1`;

- `networking.k8s.io/v1`.


---

### kind

`kind` указывает тип объекта.

```yaml
kind: Deployment
```

Примеры:

- Pod;

- Service;

- Deployment;

- ConfigMap;

- Secret.


---

### metadata

`metadata` содержит служебную информацию об объекте.

```yaml
metadata:
  name: myapp
  namespace: prod
  labels:
    app: myapp
```

Там лежат name, namespace, labels, annotations.

---

### spec

`spec` описывает желаемое состояние объекта.

Например, для Deployment:

```yaml
spec:
  replicas: 3
```

Это то, что пользователь хочет получить.

---

### status

`status` описывает текущее фактическое состояние объекта.

Например:

- сколько replicas ready;

- какой Pod phase;

- IP Pod;

- conditions.


Обычно `status` обновляет сам Kubernetes.

---

### Kubernetes API groups

API groups группируют Kubernetes resources.

Примеры:

```text
core/v1
apps/v1
batch/v1
networking.k8s.io/v1
rbac.authorization.k8s.io/v1
```

Например, Deployment живет в `apps/v1`.

---

### Object names

Object name — имя Kubernetes resource.

```yaml
metadata:
  name: nginx
```

В пределах одного namespace имена объектов одного типа должны быть уникальны.

---

### UID

UID — уникальный идентификатор объекта.

Даже если удалить объект и создать новый с тем же name, UID будет другим.

UID нужен Kubernetes для внутреннего отслеживания объектов.

---

### ownerReferences

`ownerReferences` показывает, кто владеет объектом.

Например:

```text
Deployment -> ReplicaSet -> Pod
```

Если удалить owner, Kubernetes может удалить dependent objects через garbage collection.

---

### finalizers

Finalizers блокируют удаление объекта, пока не выполнится cleanup.

Например:

- удалить внешний cloud resource;

- освободить volume;

- убрать external DNS record.


Если finalizer завис, объект может долго висеть в `Terminating`.

---

### Generation

`generation` увеличивается, когда меняется `spec`.

Контроллеры используют это, чтобы понять, что desired state изменился.

---

### ResourceVersion

`resourceVersion` — версия объекта в Kubernetes API/etcd.

Используется для watch, optimistic concurrency и внутренней синхронизации.

Обычно руками его не меняют.

---

### Declarative vs imperative management

Imperative — вручную сказать, что сделать:

```bash
kubectl create deployment nginx --image=nginx
```

Declarative — описать желаемое состояние в YAML:

```bash
kubectl apply -f deployment.yaml
```

Для production лучше declarative + Git.

---

## 21. Helm и Package Management

### Helm Chart

Helm Chart — пакет Kubernetes-манифестов.

Внутри обычно:

```text
Chart.yaml
values.yaml
templates/
```

Chart позволяет переиспользовать и параметризовать YAML.

---

### Release

Release — установленный экземпляр Helm Chart в кластере.

Один и тот же Chart можно установить несколько раз с разными values.

```bash
helm install myapp ./chart
```

`myapp` — имя release.

---

### values.yaml

`values.yaml` содержит настройки для Chart.

Например:

```yaml
replicaCount: 3
image:
  repository: nginx
  tag: "1.25"
```

Эти values подставляются в templates.

---

### Templates

Templates — YAML-шаблоны Helm.

Пример:

```yaml
replicas: {{ .Values.replicaCount }}
```

Helm рендерит templates в обычные Kubernetes manifests.

---

### `helm install`

Устанавливает release.

```bash
helm install myapp ./chart
```

С values:

```bash
helm install myapp ./chart -f values-prod.yaml
```

---

### `helm upgrade`

Обновляет существующий release.

```bash
helm upgrade myapp ./chart
```

Часто используют так:

```bash
helm upgrade --install myapp ./chart
```

Если release нет — установит, если есть — обновит.

---

### `helm rollback`

Откатывает release на прошлую revision.

```bash
helm rollback myapp 1
```

Посмотреть revisions:

```bash
helm history myapp
```

---

### `helm template`

Рендерит chart локально без установки.

```bash
helm template myapp ./chart
```

Полезно для проверки итогового YAML.

---

### `helm lint`

Проверяет chart на ошибки.

```bash
helm lint ./chart
```

Не гарантирует, что приложение заработает, но ловит базовые проблемы.

---

### Helm hooks basics

Helm hooks позволяют запускать ресурсы на этапах release lifecycle.

Например:

- pre-install;

- post-install;

- pre-upgrade;

- post-upgrade.


Часто используют для migrations или one-time Jobs.

---

### Helm dependencies

Chart может зависеть от других charts.

Зависимости описываются в `Chart.yaml`.

Пример:

- app chart зависит от PostgreSQL chart;

- monitoring chart зависит от Grafana/Prometheus charts.


---

### Debugging Helm release

Базовые команды:

```bash
helm status myapp
helm history myapp
helm get values myapp
helm get manifest myapp
kubectl describe pod ...
kubectl logs ...
```

Если проблема в template — сначала смотреть `helm template`.

---

## 22. Cluster Administration

### kubeconfig

`kubeconfig` — файл с настройками доступа к Kubernetes clusters.

Обычно:

```bash
~/.kube/config
```

В нем хранятся:

- clusters;

- users;

- contexts;

- certificates/tokens.


---

### Contexts

Context — связка cluster + user + namespace.

```bash
kubectl config get-contexts
kubectl config use-context dev
```

Позволяет быстро переключаться между кластерами.

---

### Certificates

Certificates используются для TLS и authentication.

В Kubernetes сертификаты могут использовать:

- kube-apiserver;

- kubelet;

- etcd;

- users/admins;

- control plane components.


---

### Cluster certificates

Cluster certificates защищают общение компонентов Kubernetes.

Например:

- API Server ↔ kubelet;

- API Server ↔ etcd;

- controller/scheduler ↔ API Server.


Истекшие сертификаты могут сломать cluster.

---

### Certificate rotation basics

Certificate rotation — замена сертификатов до истечения срока.

Важно следить за сроками:

```bash
kubeadm certs check-expiration
```

Для kubeadm-кластеров есть команды обновления certs.

---

### etcd backup

etcd backup сохраняет состояние Kubernetes cluster.

Примерно:

```bash
ETCDCTL_API=3 etcdctl snapshot save snapshot.db
```

Backup etcd критичен для восстановления control plane.

---

### etcd restore

etcd restore восстанавливает состояние из snapshot.

```bash
etcdctl snapshot restore snapshot.db
```

Обычно это делается аккуратно на control plane nodes.

Неправильный restore может сломать кластер.

---

### Node drain

`drain` выселяет Pods с Node перед maintenance.

```bash
kubectl drain node1 --ignore-daemonsets
```

Используется перед reboot, upgrade или ремонтом Node.

---

### Node cordon

`cordon` запрещает scheduling новых Pods на Node.

```bash
kubectl cordon node1
```

Старые Pods остаются работать.

---

### Node uncordon

`uncordon` снова разрешает scheduling на Node.

```bash
kubectl uncordon node1
```

Используется после maintenance.

---

### Node maintenance

Типичный порядок maintenance:

```text
cordon -> drain -> работы на node -> uncordon
```

Например:

- обновить OS;

- перезагрузить;

- обновить kubelet/container runtime.


---

### Kubernetes upgrades basics

Upgrade Kubernetes обычно делают поэтапно:

```text
control plane -> worker nodes -> addons
```

Важно читать release notes и проверять compatibility.

---

### Version skew basics

Version skew — допустимая разница версий компонентов Kubernetes.

Например, kubelet обычно может быть немного старше kube-apiserver, но не должен быть новее.

Перед upgrade нужно проверять правила version skew.

---

### Control Plane HA

Control Plane HA означает несколько control plane nodes.

Обычно есть:

- несколько kube-apiserver;

- etcd cluster;

- load balancer перед API;

- отказоустойчивые controller/scheduler.


---

### Worker node lifecycle

Worker node lifecycle:

```text
join cluster -> run Pods -> maintenance -> drain -> upgrade/remove
```

Node может быть Ready, NotReady, cordoned, drained.

---

## 23. Observability

### Kubernetes events

Events показывают важные события по объектам.

```bash
kubectl get events
kubectl describe pod my-pod
```

Events часто объясняют:

- почему Pod Pending;

- почему image не скачался;

- почему probe failed.


---

### Container logs

Container logs — stdout/stderr контейнера.

Kubernetes сам не хранит их вечно.

Для production обычно нужен log collector:

- Fluent Bit;

- Vector;

- Promtail;

- Filebeat.


---

### `kubectl logs`

Посмотреть логи Pod:

```bash
kubectl logs pod-name
```

С предыдущего контейнера после restart:

```bash
kubectl logs pod-name --previous
```

---

### Previous container logs

`--previous` показывает логи прошлого crashed container.

```bash
kubectl logs pod-name --previous
```

Очень полезно при CrashLoopBackOff.

---

### Metrics Server

Metrics Server собирает базовые CPU/memory metrics.

Нужен для:

```bash
kubectl top pods
kubectl top nodes
```

И для HPA по CPU/memory.

---

### Prometheus

Prometheus собирает и хранит метрики.

В Kubernetes часто собирает:

- app metrics;

- node metrics;

- kubelet metrics;

- Kubernetes object metrics.


---

### kube-state-metrics

`kube-state-metrics` экспортирует состояние Kubernetes objects.

Например:

- replicas;

- pod phases;

- deployment status;

- node conditions;

- resource requests/limits.


---

### Node Exporter

Node Exporter собирает Linux host metrics.

Например:

- CPU;

- memory;

- disk;

- filesystem;

- network.


Обычно запускается как DaemonSet.

---

### Kubernetes dashboards basics

Dashboards визуализируют состояние кластера.

Примеры:

- Kubernetes Dashboard;

- Grafana dashboards;

- Lens/OpenLens;

- cloud provider dashboards.


Dashboard не заменяет понимание `kubectl`.

---

### Application metrics

Application metrics — метрики самого приложения.

Примеры:

- request rate;

- error rate;

- latency;

- queue size;

- active users.


Лучше экспортировать их в Prometheus format.

---

### Cluster metrics

Cluster metrics показывают состояние Kubernetes и Nodes.

Примеры:

- CPU/memory usage;

- Pod count;

- Node readiness;

- restarts;

- disk pressure;

- network traffic.


---

### Audit logs basics

Audit logs показывают, кто и что делал через Kubernetes API.

Например:

- кто удалил Secret;

- кто создал Pod;

- кто изменил RoleBinding.


Очень важно для security и расследований.

---

### Distributed tracing basics

Distributed tracing помогает отследить запрос через несколько сервисов.

Примеры инструментов:

- Jaeger;

- Tempo;

- Zipkin;

- OpenTelemetry.


Полезно для микросервисов и поиска latency bottleneck.

---

## 24. Troubleshooting Pods

### Pod Pending

`Pending` значит Pod еще не запущен на Node.

Частые причины:

- нет ресурсов;

- PVC Pending;

- taints/tolerations;

- nodeSelector/affinity;

- image еще скачивается.


Проверить:

```bash
kubectl describe pod <pod>
```

---

### CrashLoopBackOff

CrashLoopBackOff значит контейнер постоянно падает и Kubernetes его перезапускает.

Проверить:

```bash
kubectl logs <pod> --previous
kubectl describe pod <pod>
```

Частые причины:

- ошибка приложения;

- неправильный env;

- нет Secret/ConfigMap;

- неправильный command.


---

### ImagePullBackOff

ImagePullBackOff значит Kubernetes не может скачать image и делает backoff.

Причины:

- неправильный image/tag;

- нет доступа к registry;

- нет imagePullSecret;

- registry недоступен.


---

### ErrImagePull

ErrImagePull — начальная ошибка скачивания image.

Потом часто переходит в ImagePullBackOff.

Проверить:

```bash
kubectl describe pod <pod>
```

---

### CreateContainerConfigError

CreateContainerConfigError возникает до запуска контейнера.

Частые причины:

- отсутствует ConfigMap;

- отсутствует Secret;

- неправильный volume;

- ошибка в envFrom/env.


---

### ContainerCreating

ContainerCreating значит контейнер еще создается.

Если долго висит, проверить:

- image pull;

- volume mount;

- CNI;

- events;

- kubelet на Node.


---

### OOMKilled

OOMKilled значит контейнер превысил memory limit.

Проверить:

```bash
kubectl describe pod <pod>
kubectl top pod <pod>
```

Решения:

- увеличить memory limit;

- найти memory leak;

- настроить requests/limits.


---

### Evicted

Evicted значит kubelet выселил Pod с Node.

Причины:

- DiskPressure;

- MemoryPressure;

- Ephemeral storage;

- Node pressure.


Проверить:

```bash
kubectl describe pod <pod>
kubectl describe node <node>
```

---

### Completed

Completed значит контейнер успешно завершился.

Для Job это нормально.

Для Deployment это проблема: приложение не должно завершаться сразу.

---

### Terminating Pod

Pod в `Terminating` удаляется.

Если завис:

- finalizers;

- volume detach;

- node недоступна;

- процесс не завершается;

- долгий terminationGracePeriod.


---

### Init Container failed

Если init container failed, основные контейнеры не стартуют.

Проверить:

```bash
kubectl logs <pod> -c <init-container>
kubectl describe pod <pod>
```

---

### Readiness probe failed

Readiness failed значит Pod не готов принимать traffic.

Он убирается из Service endpoints.

Проверить:

- endpoint readiness;

- порт;

- path;

- timeout;

- зависимости приложения.


---

### Liveness probe failed

Liveness failed приводит к restart контейнера.

Проверить:

- не слишком ли строгий timeout;

- не проверяет ли liveness внешнюю зависимость;

- хватает ли initialDelay/startupProbe.


---

### Wrong command или entrypoint

Неправильный command/entrypoint может сразу завершать контейнер.

Проверить:

```bash
kubectl describe pod <pod>
kubectl logs <pod> --previous
```

И image Dockerfile: `CMD`/`ENTRYPOINT`.

---

### Missing ConfigMap или Secret

Если Pod ссылается на несуществующий ConfigMap/Secret, он не стартует.

В Events будет ошибка.

Проверить:

```bash
kubectl get configmap
kubectl get secret
kubectl describe pod <pod>
```

---

### Permission denied

Permission denied может быть из-за:

- non-root user;

- прав на volume;

- fsGroup;

- readOnlyRootFilesystem;

- SELinux/AppArmor;

- отсутствия execute bit.


Проверить logs и securityContext.

---

## 25. Troubleshooting Networking

### Service недоступен

Проверить:

```bash
kubectl get svc
kubectl get endpoints
kubectl describe svc <service>
```

Частые причины:

- нет endpoints;

- неправильный selector;

- неправильный targetPort;

- Pods не Ready.


---

### Pod не видит другой Pod

Проверить:

- Pod IP;

- CNI;

- NetworkPolicy;

- firewall;

- Node routing.


Команды:

```bash
kubectl get pods -o wide
kubectl exec -it pod-a -- ping <pod-b-ip>
```

---

### Pod не видит Service

Проверить:

- Service существует;

- endpoints есть;

- DNS работает;

- targetPort правильный;

- NetworkPolicy не блокирует.


```bash
kubectl get svc
kubectl get endpoints
```

---

### DNS не работает

Проверить из debug Pod:

```bash
nslookup kubernetes.default
nslookup my-service.my-namespace
```

Также проверить CoreDNS Pods.

---

### CoreDNS проблемы

Проверить:

```bash
kubectl get pods -n kube-system -l k8s-app=kube-dns
kubectl logs -n kube-system -l k8s-app=kube-dns
```

Проблемы CoreDNS ломают service discovery внутри кластера.

---

### Нет Endpoints

Если у Service нет endpoints, значит он не нашел Ready Pods.

Проверить:

- selector Service;

- labels Pods;

- readiness probe;

- namespace.


```bash
kubectl get endpoints <service>
```

---

### Неправильный selector

Service выбирает Pods по labels.

Если selector не совпадает с labels Pod, endpoints будут пустые.

Проверить:

```bash
kubectl describe svc <service>
kubectl get pods --show-labels
```

---

### Неправильный targetPort

`targetPort` должен совпадать с портом приложения в Pod.

Если Service port 80, а приложение слушает 8080:

```yaml
port: 80
targetPort: 8080
```

---

### Ingress возвращает 404

Частые причины:

- host не совпадает;

- path не совпадает;

- Ingress class неправильный;

- нет default backend;

- запрос идет не в тот Ingress Controller.


---

### Ingress возвращает 502

502 часто значит Ingress не может достучаться до backend Service/Pod.

Проверить:

- Service endpoints;

- targetPort;

- readiness;

- app port;

- логи Ingress Controller.


---

### NetworkPolicy блокирует трафик

Если есть NetworkPolicy, traffic может быть запрещен.

Проверить:

```bash
kubectl get networkpolicy
kubectl describe networkpolicy <name>
```

Важно помнить: NetworkPolicy работает только при поддержке CNI.

---

### NodePort недоступен

Проверить:

- Node IP;

- nodePort;

- firewall/security groups;

- kube-proxy;

- есть ли endpoints;

- externalTrafficPolicy.


```bash
kubectl get svc
```

---

### LoadBalancer Pending

LoadBalancer может зависнуть в Pending, если cloud provider не создал LB.

Причины:

- нет cloud-controller-manager;

- нет permissions;

- unsupported environment;

- закончились quotas;

- bare-metal без MetalLB.


---

### Проверка через curl, nslookup, dig и nc

Полезные команды внутри debug Pod:

```bash
curl -v http://service:port
nslookup service.namespace
dig service.namespace
nc -vz service port
```

Они помогают отделить DNS-проблему от TCP/HTTP-проблемы.

---

### Debug Pod

Debug Pod — временный Pod с сетевыми инструментами.

Например:

```bash
kubectl run debug --rm -it --image=busybox -- sh
```

Или образ с большим набором tools:

```bash
nicolaka/netshoot
```

---

## 26. Troubleshooting Storage

### PVC Pending

PVC Pending значит claim не смог получить volume.

Проверить:

```bash
kubectl describe pvc <pvc>
```

Причины:

- нет StorageClass;

- provisioner не работает;

- неподходящий AccessMode;

- нет ресурсов storage.


---

### PV не создается

При dynamic provisioning PV должен создать CSI provisioner.

Проверить:

- StorageClass;

- CSI controller logs;

- cloud permissions;

- quotas;

- events PVC.


---

### StorageClass отсутствует

Если PVC указывает несуществующий StorageClass, volume не создастся.

Проверить:

```bash
kubectl get storageclass
kubectl describe pvc <pvc>
```

---

### Неподходящий Access Mode

PVC может запросить AccessMode, который storage не поддерживает.

Например, cloud disk часто поддерживает RWO, но не RWX.

---

### Volume не монтируется

Проверить:

```bash
kubectl describe pod <pod>
kubectl describe pvc <pvc>
```

Причины:

- CSI issue;

- Node проблема;

- permission denied;

- volume attach timeout;

- неправильный mount path.


---

### Multi-Attach error

Multi-Attach возникает, когда volume нельзя подключить к нескольким Nodes одновременно.

Часто с RWO cloud disks.

Решение:

- дождаться detach;

- перенести Pod;

- использовать RWX storage, если нужно много Nodes.


---

### Permission denied на volume

Причины:

- container работает не root;

- volume принадлежит другому UID/GID;

- не задан fsGroup;

- SELinux/AppArmor;

- readonly mount.


Проверить securityContext и права внутри Pod.

---

### Node affinity conflict у PV

PV может быть привязан к zone/Node.

Если Pod scheduled в другую zone, volume не подключится.

Решение:

- использовать `WaitForFirstConsumer`;

- проверить Node affinity у PV;

- проверить topology constraints.


---

### Volume заполнен

Если volume заполнен, приложение может падать или писать ошибки.

Проверить внутри Pod:

```bash
df -h
du -sh /path/*
```

Решения:

- cleanup;

- expansion PVC;

- увеличить storage;

- настроить retention.


---

### StatefulSet не получает PVC

Проверить:

- `volumeClaimTemplates`;

- StorageClass;

- PVC status;

- events StatefulSet/Pod;

- permissions provisioner.


```bash
kubectl get pvc
kubectl describe sts <name>
```

---

## 27. Troubleshooting Scheduling и Nodes

### FailedScheduling

FailedScheduling значит scheduler не смог найти подходящую Node.

Проверить:

```bash
kubectl describe pod <pod>
```

В Events будет причина.

---

### Insufficient CPU

На Nodes не хватает CPU по requests.

Решения:

- уменьшить CPU requests;

- добавить Nodes;

- освободить ресурсы;

- проверить overprovisioning.


---

### Insufficient memory

На Nodes не хватает memory по requests.

Решения:

- уменьшить memory requests;

- добавить Nodes;

- пересмотреть limits/requests;

- найти тяжелые Pods.


---

### Untolerated taint

Pod не имеет toleration для taint на Node.

В Events будет что-то вроде:

```text
had untolerated taint
```

Решение — добавить toleration или убрать taint.

---

### NodeSelector mismatch

Pod требует label через `nodeSelector`, но подходящих Nodes нет.

Проверить:

```bash
kubectl get nodes --show-labels
kubectl describe pod <pod>
```

---

### Affinity mismatch

Node affinity или Pod affinity/anti-affinity не позволяют запланировать Pod.

Проверить rules в Pod spec и labels Nodes/Pods.

---

### Node NotReady

Node NotReady значит Kubernetes считает Node нездоровой.

Проверить:

```bash
kubectl describe node <node>
systemctl status kubelet
```

Причины:

- kubelet down;

- network issue;

- container runtime down;

- disk/memory pressure.


---

### DiskPressure

DiskPressure значит на Node проблема с диском.

Причины:

- заполнен filesystem;

- много images;

- много logs;

- ephemeral storage.


Проверить на Node:

```bash
df -h
docker system df
crictl images
```

---

### MemoryPressure

MemoryPressure значит на Node нехватка памяти.

Kubelet может начать evict Pods.

Проверить:

```bash
kubectl describe node <node>
free -m
```

---

### PIDPressure

PIDPressure значит на Node заканчиваются process IDs.

Причины:

- fork bomb;

- слишком много процессов;

- утечки процессов.


Проверить:

```bash
ps aux
```

---

### Pod eviction

Eviction — выселение Pod из-за давления ресурсов.

Причины:

- memory pressure;

- disk pressure;

- ephemeral storage;

- node condition.


Проверить `kubectl describe pod`.

---

### Node unreachable

Node unreachable значит control plane не может связаться с Node.

Причины:

- network issue;

- Node down;

- kubelet down;

- firewall;

- cloud/network problem.


---

### kubelet не работает

Если kubelet упал, Node станет NotReady.

Проверить на Node:

```bash
systemctl status kubelet
journalctl -u kubelet
```

---

### Container runtime не работает

Если container runtime упал, kubelet не сможет запускать контейнеры.

Проверить:

```bash
systemctl status containerd
journalctl -u containerd
```

Или для CRI-O:

```bash
systemctl status crio
```

---

## 28. Практика

### Создать Deployment

```bash
kubectl create deployment nginx --image=nginx --replicas=3
```

Или через YAML.

Deployment нужен для stateless-приложений.

---

### Создать Service

```bash
kubectl expose deployment nginx --port=80 --target-port=80
```

Service даст стабильный доступ к Pods.

---

### Настроить Ingress

Ingress маршрутизирует HTTP/HTTPS traffic к Service.

Минимально нужно:

- Ingress Controller;

- Ingress resource;

- DNS на внешний IP/LB.


---

### Передать конфигурацию через ConfigMap

```bash
kubectl create configmap app-config --from-literal=APP_ENV=prod
```

Потом подключить как env или volume.

---

### Передать Secret

```bash
kubectl create secret generic app-secret --from-literal=DB_PASSWORD='password'
```

Потом подключить через `secretKeyRef` или volume.

---

### Подключить PVC

Pod подключает PVC как volume.

```text
Pod -> PVC -> PV -> storage
```

Практика: создать PVC, смонтировать его в `/data`.

---

### Создать StatefulSet

StatefulSet нужен для stateful-приложений.

Практика:

- создать Headless Service;

- создать StatefulSet;

- добавить `volumeClaimTemplates`.


---

### Создать DaemonSet

DaemonSet запускает Pod на каждой Node.

Практика:

- создать DaemonSet с `busybox` или log agent;

- проверить, что Pod появился на каждой Node.


---

### Создать Job и CronJob

Job запускает одноразовую задачу.

CronJob запускает Job по расписанию.

Практика:

- Job с `echo hello`;

- CronJob каждую минуту.


---

### Настроить probes

Добавить:

- readinessProbe;

- livenessProbe;

- startupProbe.


Проверить, как Pod выходит из endpoints при failed readiness.

---

### Настроить requests и limits

Добавить ресурсы:

```yaml
resources:
  requests:
    cpu: 100m
    memory: 128Mi
  limits:
    cpu: 500m
    memory: 256Mi
```

Проверить QoS class.

---

### Настроить HPA

```bash
kubectl autoscale deployment nginx --cpu-percent=70 --min=2 --max=10
```

Нужны Metrics Server и CPU requests.

---

### Настроить RBAC

Практика:

- создать ServiceAccount;

- создать Role;

- создать RoleBinding;

- проверить через `kubectl auth can-i`.


---

### Настроить NetworkPolicy

Практика:

- запретить весь ingress traffic;

- разрешить traffic только от конкретного Pod label.


Важно: нужен CNI с поддержкой NetworkPolicy.

---

### Выполнить rolling update

Обновить image:

```bash
kubectl set image deployment/nginx nginx=nginx:1.25
kubectl rollout status deployment/nginx
```

Посмотреть, как Pods заменяются постепенно.

---

### Выполнить rollback

```bash
kubectl rollout undo deployment/nginx
```

Проверить историю:

```bash
kubectl rollout history deployment/nginx
```

---

### Сломать selector Service и найти проблему

Практика:

- поменять Service selector на неправильный;

- проверить `kubectl get endpoints`;

- вернуть правильный selector.


Главный симптом — Service без endpoints.

---

### Сломать readiness probe и найти проблему

Практика:

- указать неправильный path/port;

- Pod будет Running, но не Ready;

- Service не будет отправлять на него traffic.


Проверить через `describe pod`.

---

### Создать CrashLoopBackOff и исправить

Например, запустить контейнер с неправильной командой.

Потом:

- посмотреть logs;

- посмотреть `--previous`;

- исправить command/image/env.


---

### Создать OOMKilled и исправить

Поставить маленький memory limit и запустить процесс, который ест память.

Потом:

- увидеть `OOMKilled`;

- увеличить limit;

- исправить приложение.


---

### Создать PVC Pending и исправить

Например, указать несуществующий StorageClass.

Потом:

- посмотреть `describe pvc`;

- исправить StorageClass;

- проверить создание PV.


---

### Выполнить cordon, drain и uncordon Node

```bash
kubectl cordon node1
kubectl drain node1 --ignore-daemonsets
kubectl uncordon node1
```

Это базовая практика node maintenance.

---

## 29. Interview Questions

### Что происходит после `kubectl apply`

`kubectl apply` отправляет YAML в kube-apiserver.

Дальше:

- API Server валидирует объект;

- сохраняет состояние в etcd;

- controllers видят изменения;

- scheduler назначает Pods на Nodes;

- kubelet запускает containers через runtime.


---

### Как Kubernetes создает Pod

Упрощенно:

```text
API Server -> etcd -> scheduler -> kubelet -> container runtime -> container
```

Scheduler выбирает Node, kubelet на этой Node запускает Pod.

---

### Deployment vs StatefulSet

Deployment — для stateless-приложений.

StatefulSet — для stateful-приложений, где нужны:

- стабильные имена;

- стабильные volumes;

- порядок запуска;

- stable network identity.


---

### Deployment vs DaemonSet

Deployment поддерживает нужное количество replicas.

DaemonSet запускает Pod на каждой подходящей Node.

Примеры DaemonSet:

- log agent;

- monitoring agent;

- CNI plugin.


---

### Pod vs Container

Container — запущенный процесс приложения.

Pod — Kubernetes-обертка вокруг одного или нескольких containers.

Pod имеет общий IP, volumes и lifecycle для своих containers.

---

### Service vs Ingress

Service дает стабильный доступ к Pods внутри или снаружи кластера.

Ingress управляет HTTP/HTTPS маршрутизацией по host/path.

```text
Ingress -> Service -> Pods
```

---

### ClusterIP vs NodePort vs LoadBalancer

ClusterIP — доступ только внутри кластера.

NodePort — порт на каждой Node.

LoadBalancer — внешний cloud load balancer перед Service.

---

### ConfigMap vs Secret

ConfigMap — обычная конфигурация.

Secret — чувствительные данные.

Но Secret по умолчанию это base64, не полноценное шифрование.

---

### PV vs PVC vs StorageClass

PV — реальный storage resource.

PVC — запрос на storage.

StorageClass — класс/provisioner для динамического создания storage.

```text
Pod -> PVC -> PV -> StorageClass/provisioner
```

---

### Requests vs limits

Requests используются scheduler для размещения Pod.

Limits ограничивают максимальное потребление ресурсов.

CPU limit вызывает throttling, memory limit может привести к OOMKilled.

---

### Liveness vs readiness vs startup probe

Liveness проверяет, жив ли container. При fail — restart.

Readiness проверяет, готов ли Pod принимать traffic. При fail — убирается из Service endpoints.

Startup probe помогает приложениям с долгим стартом.

---

### Taints vs tolerations

Taint ставится на Node и отталкивает Pods.

Toleration ставится на Pod и разрешает запуск на Node с taint.

```text
taint на Node + toleration у Pod = Pod может scheduled
```

---

### Node affinity vs Pod affinity

Node affinity выбирает Nodes по labels.

Pod affinity/anti-affinity размещает Pod относительно других Pods.

Например, не класть две реплики на одну Node.

---

### Role vs ClusterRole

Role действует внутри namespace.

ClusterRole действует на уровне cluster или для cluster-wide ресурсов.

Например, Nodes требуют ClusterRole.

---

### Как Service находит Pods

Service использует selector по labels.

```yaml
selector:
  app: nginx
```

Pods с подходящими labels становятся endpoints Service.

---

### Как работает DNS внутри Kubernetes

CoreDNS резолвит имена Services и некоторых Pods.

Например:

```text
my-service.default.svc.cluster.local
```

Обычно Pod обращается к Service по DNS-имени.

---

### Как работает rolling update

Deployment постепенно заменяет старые Pods новыми.

Параметры:

- `maxSurge`;

- `maxUnavailable`.


Цель — обновить приложение без полной остановки.

---

### Почему Pod находится в Pending

Причины:

- не хватает CPU/memory;

- PVC Pending;

- taints без tolerations;

- nodeSelector/affinity mismatch;

- scheduler не нашел Node.


Смотреть `kubectl describe pod`.

---

### Почему возникает CrashLoopBackOff

Контейнер стартует, падает, Kubernetes пытается перезапустить, потом увеличивает delay.

Причины:

- ошибка приложения;

- неправильный command;

- нет env/secret/config;

- dependency недоступна;

- probe убивает контейнер.


---

### Почему возникает OOMKilled

Container превысил memory limit.

Kernel/Kubernetes убивает процесс.

Проверять:

- memory limits;

- usage;

- memory leaks;

- размер heap/cache.


---

### Как диагностировать недоступный Service

Порядок:

```bash
kubectl get svc
kubectl get endpoints
kubectl describe svc
kubectl get pods --show-labels
kubectl exec -it debug -- curl service:port
```

Чаще всего проблема в selector, endpoints или targetPort.

---

### Как диагностировать Node NotReady

Проверить:

```bash
kubectl describe node <node>
systemctl status kubelet
journalctl -u kubelet
systemctl status containerd
```

Частые причины:

- kubelet down;

- container runtime down;

- network issue;

- disk/memory pressure;

- Node unreachable.