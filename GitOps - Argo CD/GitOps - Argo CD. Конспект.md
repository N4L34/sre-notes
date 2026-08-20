# GitOps / Argo CD

## 1. Основы GitOps

### Что такое GitOps

GitOps — подход, где состояние инфраструктуры и приложений описано в Git.

Изменения делаются через commit/PR, а специальный controller применяет их в cluster.

```text
Git -> Argo CD -> Kubernetes
```

---

### Git как Source of Truth

Git — главный источник правды.

То, что лежит в Git, считается правильным состоянием системы.

Если в cluster что-то отличается от Git — это drift.

---

### Desired State

Desired State — желаемое состояние.

Например, в Git написано:

```yaml
replicas: 3
image: myapp:1.2.0
```

Значит Kubernetes должен прийти именно к этому состоянию.

---

### Actual State

Actual State — реальное состояние в cluster прямо сейчас.

Например:

- реально работает 2 Pod;

- image другой версии;

- Service изменен вручную.


GitOps сравнивает actual state с desired state.

---

### Reconciliation Loop

Reconciliation Loop — постоянное сравнение Git и cluster.

```text
desired state != actual state
        ↓
controller исправляет cluster
```

Это основа GitOps.

---

### Pull Model vs Push Model

Push model — CI/CD сам пушит изменения в cluster.

```text
CI/CD -> kubectl/helm -> cluster
```

Pull model — controller внутри cluster сам забирает изменения из Git.

```text
Argo CD -> Git -> sync -> cluster
```

GitOps обычно использует pull model.

---

### Drift Detection

Drift Detection — обнаружение расхождения между Git и cluster.

Например, кто-то вручную сделал:

```bash
kubectl edit deployment myapp
```

Argo CD увидит, что cluster отличается от Git.

---

## 2. Argo CD Architecture

### Что такое Argo CD

Argo CD — GitOps controller для Kubernetes.

Он следит за Git repository и синхронизирует Kubernetes cluster с manifests из Git.

---

### Components

Основные компоненты Argo CD:

```text
argocd-server
repo-server
application-controller
dex-server
redis
```

Главный для sync — `application-controller`.

---

### Application

Application — основной объект Argo CD.

Он описывает:

- откуда брать manifests;

- куда деплоить;

- как синхронизировать.


```text
Git repo + path/chart -> Kubernetes namespace/cluster
```

---

### Project

Project группирует Applications и задает ограничения.

Например:

- какие repos разрешены;

- в какие clusters можно deploy;

- какие namespaces доступны;

- какие resources разрешены.


---

### Repository

Repository — Git repo или Helm repo, откуда Argo CD берет manifests.

Например:

```text
https://github.com/company/k8s-manifests
```

---

### Destination Cluster

Destination Cluster — cluster, куда Argo CD деплоит приложение.

Это может быть:

- тот же cluster, где стоит Argo CD;

- внешний Kubernetes cluster;

- несколько clusters.


---

## 3. Applications

### Plain YAML

Argo CD может деплоить обычные Kubernetes YAML.

Пример структуры:

```text
apps/myapp/
  deployment.yaml
  service.yaml
  ingress.yaml
```

Самый простой вариант.

---

### Helm

Argo CD умеет деплоить Helm charts.

Он сам делает render chart и применяет manifests в cluster.

Можно использовать:

- local chart из Git;

- chart из Helm repository;

- values files.


---

### Kustomize

Argo CD поддерживает Kustomize.

Типичная структура:

```text
base/
overlays/dev/
overlays/prod/
```

Удобно для разных окружений без Helm.

---

### Multiple Sources

Multiple Sources позволяют одной Argo CD Application брать данные из нескольких источников.

Например:

- Helm chart из одного repo;

- values.yaml из другого repo.


Полезно для разделения app chart и environment config.

---

### App of Apps

App of Apps — паттерн, где одна root Application создает другие Applications.

```text
root-app
  ├── app-1
  ├── app-2
  └── app-3
```

Удобно для управления большим количеством приложений.

---

### ApplicationSet

ApplicationSet автоматически создает много Applications по шаблону.

Например:

- для разных clusters;

- для разных namespaces;

- для разных environments;

- для всех директорий в Git repo.


---

### Multi-cluster basics

Argo CD может управлять несколькими Kubernetes clusters.

Один Argo CD instance может деплоить приложения в:

```text
dev cluster
stage cluster
prod cluster
```

Для этого clusters добавляются в Argo CD как destinations.

---

## 4. Sync

### Manual Sync

Manual Sync — синхронизация запускается руками.

Например, через UI или CLI:

```bash
argocd app sync myapp
```

Подходит, если нужен ручной контроль деплоя.

---

### Auto Sync

Auto Sync — Argo CD сам применяет изменения из Git.

После commit в Git Argo CD увидит изменения и выполнит sync.

Это ближе к полноценному GitOps.

---

### Self Heal

Self Heal автоматически исправляет ручные изменения в cluster.

Если кто-то поменял resource через `kubectl`, Argo CD вернет его к состоянию из Git.

---

### Prune

Prune удаляет resources, которые больше не описаны в Git.

Например, если удалить `service.yaml` из repo, Argo CD удалит Service из cluster.

Без prune старые resources могут остаться висеть.

---

### Sync Options

Sync Options меняют поведение sync.

Например:

- создавать namespace автоматически;

- применять server-side apply;

- отключать prune для отдельных ресурсов;

- задавать replace вместо apply.


---

### Sync Waves basics

Sync Waves управляют порядком применения ресурсов.

Например:

```text
wave 0 -> Namespace
wave 1 -> CRD
wave 2 -> Deployment
```

Используется через annotation.

---

### Health Status

Health Status показывает состояние приложения.

Примеры:

- `Healthy`;

- `Progressing`;

- `Degraded`;

- `Suspended`;

- `Missing`.


Health отвечает на вопрос: “приложение живое или нет?”

---

### Sync Status

Sync Status показывает, совпадает ли cluster с Git.

Основные статусы:

- `Synced`;

- `OutOfSync`;

- `Unknown`.


Sync отвечает на вопрос: “cluster соответствует Git или нет?”

---

## 5. Rollback и Deploy

### Git Commit → Deployment

В GitOps deploy начинается с Git commit.

```text
commit -> Argo CD detects change -> sync -> Kubernetes deploy
```

Не нужно вручную запускать `kubectl apply` из CI.

---

### Rollback через Git

Rollback делается через revert commit или возврат старой версии в Git.

Например:

- откатить image tag;

- вернуть старый values.yaml;

- сделать `git revert`.


Argo CD применит старое состояние в cluster.

---

### Drift Correction

Drift correction — автоматическое исправление расхождений между Git и cluster.

Если включен Self Heal, Argo CD сам вернет cluster к Git-состоянию.

---

### Environment Promotion

Environment Promotion — продвижение версии между окружениями.

Например:

```text
dev -> stage -> prod
```

Обычно это делается через изменение Git:

- обновить image tag;

- сделать PR в prod values;

- merge после approve.


---

## 6. Secrets

### Sealed Secrets basics

Sealed Secrets позволяют хранить encrypted secrets в Git.

В Git лежит зашифрованный объект, а controller в cluster расшифровывает его в Kubernetes Secret.

```text
SealedSecret in Git -> controller -> Secret in cluster
```

---

### External Secrets basics

External Secrets синхронизирует secrets из внешнего secret manager в Kubernetes.

Примеры:

- AWS Secrets Manager;

- HashiCorp Vault;

- GCP Secret Manager;

- Azure Key Vault.


В Git хранится только ссылка на secret, а не сам secret value.

---

### GitOps и Secrets

В GitOps нельзя хранить plain-text secrets в Git.

Нормальные варианты:

- External Secrets;

- Sealed Secrets;

- SOPS;

- Vault;

- cloud secret manager.


Главная идея: Git хранит декларацию, но не открытый secret.

---

## 7. Troubleshooting

### OutOfSync

OutOfSync значит cluster отличается от Git.

Причины:

- новый commit еще не применен;

- кто-то изменил resource вручную;

- sync failed;

- generated manifests отличаются.


Проверить:

```bash
argocd app diff myapp
argocd app sync myapp
```

---

### Degraded

Degraded значит приложение применилось, но работает плохо.

Например:

- Pod падает;

- Deployment не rollout'ится;

- Service без endpoints;

- Job failed.


Проверять нужно уже Kubernetes resources:

```bash
kubectl get pods
kubectl describe pod
kubectl logs
```

---

### Sync Failed

Sync Failed значит Argo CD не смог применить manifests.

Причины:

- YAML ошибка;

- нет RBAC прав;

- отсутствует CRD;

- conflict;

- invalid Kubernetes field.


Смотреть details в UI или:

```bash
argocd app get myapp
```

---

### Application не синхронизируется

Проверить:

- auto sync включен или нет;

- repo доступен;

- branch/path правильный;

- нет sync window;

- нет failed hook;

- есть ли права у Argo CD.


---

### Helm Chart не применяется

Причины:

- ошибка в values;

- chart не рендерится;

- dependency не скачалась;

- required value не задан;

- Kubernetes manifest invalid.


Проверить локально:

```bash
helm template myapp ./chart -f values.yaml
```

---

### Kustomize ошибка

Причины:

- неправильный path;

- сломан `kustomization.yaml`;

- resource не найден;

- patch не применяется;

- несовместимые имена resources.


Проверить локально:

```bash
kustomize build overlays/prod
```

---

### Repository недоступен

Причины:

- неправильный URL;

- нет SSH key/token;

- repo private;

- network issue;

- неправильный branch;

- истек credentials.


Проверить repository connection в Argo CD settings.

---

## 8. Практика

### Подключить Git Repository

Нужно добавить repo в Argo CD.

Через UI или CLI:

```bash
argocd repo add git@github.com:company/k8s-manifests.git
```

Для private repo нужны credentials.

---

### Создать Application

Application указывает source и destination.

Примерно:

```text
source: Git repo + path
destination: cluster + namespace
```

После создания Argo CD начнет отслеживать manifests.

---

### Задеплоить Helm Chart

Argo CD может деплоить Helm chart из Git или Helm repo.

Практика:

- создать Application;

- указать chart/path;

- указать values file;

- выполнить sync.


---

### Включить Auto Sync

Auto Sync включает автоматическое применение изменений.

Обычно вместе с ним можно включить:

- prune;

- selfHeal.


Так Git становится настоящим source of truth.

---

### Проверить Self Heal

Практика:

- включить selfHeal;

- вручную изменить Deployment через `kubectl edit`;

- посмотреть, как Argo CD вернет состояние из Git.


---

### Выполнить Rollback

GitOps rollback лучше делать через Git.

Например:

- вернуть старый image tag;

- сделать commit;

- дождаться sync.


Или использовать revert commit.

---

### Использовать App of Apps

Практика:

- создать root Application;

- в Git описать несколько child Applications;

- root Application применит остальные.


Удобно для bootstrap окружения.

---

## 9. Interview Questions

### Что такое GitOps

GitOps — подход, где Git является source of truth для инфраструктуры и приложений.

Controller сравнивает Git с cluster и приводит cluster к состоянию из Git.

---

### GitOps vs CI/CD

CI/CD обычно собирает, тестирует и доставляет приложение.

GitOps отвечает за применение desired state из Git в cluster.

```text
CI -> build/test/push image
GitOps -> deploy/sync from Git
```

Они часто используются вместе.

---

### Push vs Pull

Push model — pipeline сам пушит изменения в cluster.

Pull model — controller внутри cluster сам забирает изменения из Git.

GitOps обычно использует pull model.

---

### Что такое Desired State

Desired State — состояние, которое описано в Git.

Например:

- нужный image;

- replicas;

- config;

- Kubernetes manifests.


---

### Что такое Drift

Drift — расхождение между Git и реальным cluster.

Например, в Git `replicas: 3`, а в cluster вручную поставили `replicas: 5`.

---

### Как работает Argo CD

Argo CD:

- читает manifests из Git/Helm/Kustomize;

- сравнивает с cluster;

- показывает diff;

- выполняет sync;

- следит за health и sync status.


---

### Что делает Self Heal

Self Heal автоматически исправляет ручные изменения в cluster.

Если cluster отличается от Git, Argo CD вернет состояние из Git.

---

### Что делает Prune

Prune удаляет resources из cluster, если они были удалены из Git.

Без prune старые resources могут остаться в cluster.

---

### Что такое ApplicationSet

ApplicationSet — controller, который генерирует Argo CD Applications по шаблону.

Полезен для multi-cluster, multi-env и массового создания Applications.

---

### Почему Git считается Source of Truth

Потому что именно Git содержит желаемое состояние системы.

Плюсы:

- история изменений;

- code review;

- rollback через revert;

- audit trail;

- единая точка управления infrastructure/app configs.