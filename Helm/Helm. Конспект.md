# Helm

## 1. Основы

### Что такое Helm

Helm — package manager для Kubernetes.

Он позволяет устанавливать приложения в Kubernetes как пакеты.

```text
Helm chart -> Kubernetes manifests -> Cluster
```

---

### Зачем нужен Helm

Helm нужен, чтобы не писать и не поддерживать много почти одинаковых YAML вручную.

Он помогает:

- шаблонизировать манифесты;
    
- переиспользовать конфиги;
    
- делать install/upgrade/rollback;
    
- хранить версии приложений.
    

---

### Helm architecture

В Helm 3 архитектура простая:

```text
Helm CLI -> Kubernetes API
```

Helm рендерит templates в Kubernetes YAML и отправляет их в cluster.

---

### Helm Chart

Chart — пакет Kubernetes-манифестов.

Обычно внутри:

```text
Chart.yaml
values.yaml
templates/
```

Chart описывает, как развернуть приложение.

---

### Release

Release — установленный экземпляр Chart в Kubernetes.

Один Chart можно установить несколько раз:

```bash
helm install app-dev ./mychart
helm install app-prod ./mychart
```

Это будут разные releases.

---

### Repository

Helm repository — хранилище charts.

Пример:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

Из repo можно устанавливать готовые charts.

---

### Chart Museum basics

ChartMuseum — self-hosted Helm chart repository.

Используется, чтобы хранить свои charts внутри компании.

Сейчас часто заменяется OCI registry.

---

### OCI Registry basics

Helm умеет хранить charts в OCI registry.

Например:

- Docker Registry;
    
- GitHub Container Registry;
    
- GitLab Registry;
    
- AWS ECR.
    

Пример:

```bash
helm push mychart-1.0.0.tgz oci://registry.example.com/charts
```

---

## 2. Структура Chart

### Chart.yaml

`Chart.yaml` — metadata Helm Chart.

Пример:

```yaml
apiVersion: v2
name: myapp
version: 1.0.0
appVersion: "2.3.1"
```

`version` — версия chart, `appVersion` — версия приложения.

---

### values.yaml

`values.yaml` — дефолтные настройки chart.

Пример:

```yaml
replicaCount: 3
image:
  repository: nginx
  tag: "1.25"
```

Эти значения используются в templates.

---

### templates/

`templates/` — директория с Kubernetes YAML-шаблонами.

Например:

```text
templates/deployment.yaml
templates/service.yaml
templates/ingress.yaml
```

Helm рендерит их в обычные Kubernetes manifests.

---

### charts/

`charts/` — директория для зависимых charts.

Например, app chart может включать PostgreSQL subchart.

Обычно зависимости подтягиваются через:

```bash
helm dependency update
```

---

### .helmignore

`.helmignore` похож на `.gitignore`.

Он исключает файлы из Helm package.

Например:

```text
.git/
README.md
*.tmp
```

---

### NOTES.txt

`NOTES.txt` выводится после установки chart.

Туда часто пишут:

- как проверить приложение;
    
- какой URL открыть;
    
- какие команды выполнить.
    

---

### _helpers.tpl

`_helpers.tpl` хранит reusable template helpers.

Например:

- имя приложения;
    
- labels;
    
- fullname;
    
- common selectors.
    

Это помогает не копировать одинаковые куски YAML.

---

### CRDs directory

`crds/` — директория для Custom Resource Definitions.

Helm устанавливает CRDs до обычных templates.

Пример:

```text
crds/my-resource.yaml
```

---

## 3. Templates

### Go Templates basics

Helm templates используют Go template syntax.

Пример:

```yaml
name: {{ .Release.Name }}
```

Helm подставляет значения и генерирует обычный YAML.

---

### Variables

Переменные можно создавать внутри templates.

```yaml
{{- $name := .Release.Name }}
name: {{ $name }}
```

Удобно, чтобы не повторять длинные выражения.

---

### Pipelines

Pipeline передает результат одной функции в другую.

```yaml
name: {{ .Values.name | quote }}
```

Здесь значение сначала берется из values, потом оборачивается в кавычки.

---

### if

`if` добавляет условную логику.
 
```yaml
{{- if .Values.ingress.enabled }}
kind: Ingress
{{- end }}
```

Если значение false — блок не попадет в manifest.

---

### else

`else` выполняется, если `if` false.

```yaml
{{- if .Values.serviceAccount.create }}
name: custom-sa
{{- else }}
name: default
{{- end }}
```

---

### range

`range` используется для циклов.

```yaml
{{- range .Values.env }}
- name: {{ .name }}
  value: {{ .value | quote }}
{{- end }}
```

Удобно для списков env, ports, hosts.

---

### with

`with` меняет текущий context.

```yaml
{{- with .Values.resources }}
resources:
  {{- toYaml . | nindent 2 }}
{{- end }}
```

Если значение пустое, блок не выводится.

---

### include

`include` вызывает named template и возвращает строку.

```yaml
name: {{ include "myapp.fullname" . }}
```

Часто используется с helpers из `_helpers.tpl`.

---

### template

`template` тоже вызывает named template, но менее удобно в pipeline.

```yaml
{{ template "myapp.labels" . }}
```

На практике чаще используют `include`.

---

### tpl

`tpl` рендерит строку как template.

Полезно, если значение в `values.yaml` само содержит template.

```yaml
value: {{ tpl .Values.someTemplate . }}
```

Использовать аккуратно, чтобы не усложнять chart.

---

### default

`default` задает значение по умолчанию.

```yaml
replicas: {{ .Values.replicaCount | default 1 }}
```

Если value не задано, будет использовано `1`.

---

### required

`required` требует обязательное значение.

```yaml
image: {{ required "image.repository is required" .Values.image.repository }}
```

Если value нет, Helm завершится с ошибкой.

---

### quote

`quote` оборачивает значение в кавычки.

```yaml
env: {{ .Values.env | quote }}
```

Полезно для строк в YAML.

---

### toYaml

`toYaml` превращает объект в YAML.

```yaml
resources:
  {{- toYaml .Values.resources | nindent 2 }}
```

Часто используется для resources, tolerations, affinity.

---

### nindent

`nindent` добавляет новую строку и отступы.

```yaml
{{- toYaml .Values.resources | nindent 12 }}
```

Очень часто нужен, чтобы YAML был корректно отформатирован.

---

### indent

`indent` добавляет отступы без новой строки в начале.

```yaml
{{ toYaml .Values.labels | indent 4 }}
```

`nindent` в Helm обычно используется чаще.

---

## 4. Values

### values.yaml

`values.yaml` содержит дефолтные параметры chart.

Например:

```yaml
replicaCount: 2
service:
  type: ClusterIP
  port: 80
```

---

### Default values

Default values — значения по умолчанию из `values.yaml`.

Они применяются, если пользователь ничего не переопределил.

---

### Override values

Values можно переопределять при install/upgrade.

Например, для prod можно задать больше replicas.

---

### --set

`--set` переопределяет value из CLI.

```bash
helm install myapp ./chart --set replicaCount=5
```

Удобно для простых значений.

---

### --values (-f)

`-f` подключает отдельный values file.

```bash
helm install myapp ./chart -f values-prod.yaml
```

Так обычно разделяют dev/stage/prod настройки.

---

### Multiple values files

Можно передать несколько values files.

```bash
helm upgrade myapp ./chart \
  -f values.yaml \
  -f values-prod.yaml
```

Поздние файлы переопределяют ранние.

---

### Global values

`global` values доступны parent chart и subcharts.

```yaml
global:
  imageRegistry: registry.example.com
```

Используются для общих настроек.

---

### Environment-specific values

Для разных окружений часто делают разные файлы:

```text
values-dev.yaml
values-stage.yaml
values-prod.yaml
```

Это позволяет использовать один chart для разных environments.

---

## 5. Работа с Chart

### helm create

Создает skeleton нового chart.

```bash
helm create myapp
```

Helm создаст стандартную структуру chart.

---

### helm package

Упаковывает chart в `.tgz`.

```bash
helm package ./myapp
```

Результат:

```text
myapp-1.0.0.tgz
```

---

### helm lint

Проверяет chart на базовые ошибки.

```bash
helm lint ./myapp
```

Полезно запускать перед commit или CI.

---

### helm template

Рендерит templates локально.

```bash
helm template myapp ./myapp
```

Ничего не устанавливает в cluster.

Очень полезно для debug.

---

### helm install

Устанавливает chart в cluster.

```bash
helm install myapp ./myapp
```

С namespace:

```bash
helm install myapp ./myapp -n prod --create-namespace
```

---

### helm upgrade

Обновляет существующий release.

```bash
helm upgrade myapp ./myapp
```

Часто используют:

```bash
helm upgrade --install myapp ./myapp
```

Это install, если release еще нет, и upgrade, если он уже есть.

---

### helm uninstall

Удаляет release.

```bash
helm uninstall myapp
```

Kubernetes resources, созданные release, будут удалены.

---

### helm rollback

Откатывает release на старую revision.

```bash
helm rollback myapp 1
```

Посмотреть revisions можно через `helm history`.

---

### helm history

Показывает историю release.

```bash
helm history myapp
```

Видно revisions, даты, status и chart versions.

---

### helm list

Показывает releases.

```bash
helm list
```

Во всех namespaces:

```bash
helm list -A
```

---

### helm status

Показывает статус release.

```bash
helm status myapp
```

Полезно после install/upgrade.

---

### helm get

`helm get` показывает данные release.

Примеры:

```bash
helm get values myapp
helm get manifest myapp
helm get hooks myapp
```

Полезно для debug.

---

## 6. Dependencies

### Chart dependencies

Chart dependencies — зависимости одного chart от других charts.

Например:

- приложение зависит от PostgreSQL;
    
- monitoring stack зависит от Grafana.
    

---

### dependencies в Chart.yaml

Зависимости описываются в `Chart.yaml`.

```yaml
dependencies:
  - name: postgresql
    version: 15.5.0
    repository: https://charts.bitnami.com/bitnami
```

---

### helm dependency update

Скачивает зависимости и обновляет `Chart.lock`.

```bash
helm dependency update
```

Используется после изменения dependencies.

---

### helm dependency build

Собирает зависимости из `Chart.lock`.

```bash
helm dependency build
```

Полезно для воспроизводимой сборки.

---

### Subcharts

Subchart — chart-зависимость внутри parent chart.

Например:

```text
parent chart: myapp
subchart: postgresql
```

Subchart имеет свои templates и values.

---

### Parent chart

Parent chart — основной chart, который подключает subcharts.

Он может передавать настройки в subcharts через values.

---

### Global values

Global values доступны всем subcharts.

```yaml
global:
  storageClass: fast
```

Используются для общих настроек.

---

### Conditions

Condition позволяет включать/выключать dependency.

```yaml
dependencies:
  - name: postgresql
    condition: postgresql.enabled
```

В values:

```yaml
postgresql:
  enabled: true
```

---

### Tags

Tags позволяют включать/выключать группы dependencies.

```yaml
dependencies:
  - name: redis
    tags:
      - cache
```

В values:

```yaml
tags:
  cache: true
```

---

## 7. Hooks

### Helm Hooks

Helm hooks запускают Kubernetes resources на определенных этапах release lifecycle.

Например:

- до установки;
    
- после установки;
    
- перед upgrade;
    
- перед удалением.
    

---

### pre-install

`pre-install` запускается перед установкой release.

Часто используют для подготовительных Jobs.

```yaml
annotations:
  "helm.sh/hook": pre-install
```

---

### post-install

`post-install` запускается после установки release.

Например:

- smoke test;
    
- initialization job;
    
- notification.
    

---

### pre-upgrade

`pre-upgrade` запускается перед upgrade.

Часто используют для migrations или backup перед обновлением.

---

### post-upgrade

`post-upgrade` запускается после upgrade.

Например:

- проверка состояния;
    
- post-migration;
    
- smoke test.
    

---

### pre-delete

`pre-delete` запускается перед удалением release.

Может использоваться для cleanup.

---

### Hook weights

Hook weight задает порядок выполнения hooks.

```yaml
annotations:
  "helm.sh/hook-weight": "5"
```

Меньший weight выполняется раньше.

---

### Hook delete policy

Hook delete policy управляет удалением hook resources.

```yaml
annotations:
  "helm.sh/hook-delete-policy": before-hook-creation,hook-succeeded
```

Без этого старые hook Jobs могут оставаться в namespace.

---

## 8. Debugging

### helm lint

Первый шаг проверки chart:

```bash
helm lint ./chart
```

Ловит базовые ошибки структуры и templates.

---

### helm template

Показывает итоговые Kubernetes manifests.

```bash
helm template myapp ./chart -f values-prod.yaml
```

Если YAML сломан, часто видно именно здесь.

---

### helm install --dry-run

Проверяет install без реального создания ресурсов.

```bash
helm install myapp ./chart --dry-run
```

Полезно перед настоящей установкой.

---

### helm install --debug

`--debug` выводит больше информации.

```bash
helm install myapp ./chart --debug --dry-run
```

Часто используют вместе с `--dry-run`.

---

### helm get values

Показывает values установленного release.

```bash
helm get values myapp
```

Все values, включая computed:

```bash
helm get values myapp --all
```

---

### helm get manifest

Показывает rendered manifests установленного release.

```bash
helm get manifest myapp
```

Полезно понять, что реально было применено в cluster.

---

### helm get hooks

Показывает hooks release.

```bash
helm get hooks myapp
```

Полезно, если завис pre-install/pre-upgrade Job.

---

### Проверка rendered manifests

После render нужно проверять обычными Kubernetes tools:

```bash
helm template myapp ./chart | kubectl apply --dry-run=server -f -
```

Так можно поймать ошибки Kubernetes API schema.

---

## 9. Best Practices

### DRY templates

DRY значит не повторять одинаковый YAML много раз.

Повторяемые части лучше вынести в helpers.

Например:

- labels;
    
- names;
    
- selectors;
    
- annotations.
    

---

### Использование _helpers.tpl

`_helpers.tpl` используют для reusable template blocks.

Например:

```text
myapp.name
myapp.fullname
myapp.labels
myapp.selectorLabels
```

Это делает templates чище.

---

### Naming conventions

Имена ресурсов должны быть предсказуемыми.

Обычно используют:

```text
release-name + chart-name
```

Через helper:

```yaml
{{ include "myapp.fullname" . }}
```

---

### Labels

У Kubernetes resources должны быть стандартные labels.

Например:

```yaml
app.kubernetes.io/name: myapp
app.kubernetes.io/instance: myapp-prod
app.kubernetes.io/managed-by: Helm
```

Это помогает искать и группировать ресурсы.

---

### Annotations

Annotations используют для дополнительной metadata.

Например:

- ingress controller settings;
    
- checksum config;
    
- hook annotations;
    
- monitoring scrape config.
    

---

### Версионирование Chart

Chart version задается в `Chart.yaml`.

```yaml
version: 1.2.3
```

При изменении chart версию нужно повышать.

---

### Semantic Versioning

Semantic Versioning:

```text
MAJOR.MINOR.PATCH
```

Пример:

```text
1.4.2
```

Обычно:

- PATCH — bugfix;
    
- MINOR — новая совместимая функциональность;
    
- MAJOR — breaking changes.
    

---

### Не хранить секреты в values.yaml

`values.yaml` часто попадает в Git.

Не стоит хранить там:

- passwords;
    
- tokens;
    
- private keys;
    
- webhook URLs.
    

Лучше использовать External Secrets, Sealed Secrets или secret manager.

---

### Структура production Chart

Production chart обычно содержит:

- Deployment/StatefulSet;
    
- Service;
    
- Ingress;
    
- ConfigMap;
    
- Secret integration;
    
- ServiceAccount/RBAC;
    
- probes;
    
- resources;
    
- autoscaling;
    
- NetworkPolicy;
    
- PodDisruptionBudget.
    

---

## 10. Практика

### Создать свой Chart

```bash
helm create myapp
```

Потом удалить лишние templates и оставить только нужные ресурсы.

---

### Развернуть Deployment

Создать template `deployment.yaml`.

Вынести в values:

- image;
    
- tag;
    
- replicas;
    
- resources;
    
- env.
    

---

### Развернуть Service

Создать template `service.yaml`.

В values обычно выносят:

```yaml
service:
  type: ClusterIP
  port: 80
```

---

### Развернуть Ingress

Добавить template `ingress.yaml`.

В values:

```yaml
ingress:
  enabled: true
  host: app.example.com
```

---

### Использовать ConfigMap

Создать template `configmap.yaml`.

Например, хранить non-secret настройки приложения.

Подключить ConfigMap в Deployment через env или volume.

---

### Использовать Secret

Создать Secret template или подключить внешний Secret.

Важно: не хранить реальные secrets в обычном `values.yaml`.

---

### Использовать PVC

Добавить template `pvc.yaml`.

В values вынести:

```yaml
persistence:
  enabled: true
  size: 10Gi
  storageClass: fast
```

---

### Добавить values.yaml

Сделать параметры chart настраиваемыми.

Например:

```yaml
replicaCount: 2
image:
  repository: nginx
  tag: "1.25"
```

---

### Использовать template helpers

Вынести common names и labels в `_helpers.tpl`.

Потом использовать:

```yaml
name: {{ include "myapp.fullname" . }}
```

---

### Создать dependency

Добавить dependency в `Chart.yaml`.

Например PostgreSQL:

```yaml
dependencies:
  - name: postgresql
    version: 15.x.x
    repository: https://charts.bitnami.com/bitnami
```

Потом:

```bash
helm dependency update
```

---

### Выполнить upgrade

```bash
helm upgrade myapp ./chart -f values-prod.yaml
```

Проверить:

```bash
helm status myapp
kubectl rollout status deployment/myapp
```

---

### Выполнить rollback

Посмотреть историю:

```bash
helm history myapp
```

Откатиться:

```bash
helm rollback myapp 1
```

---

### Debug failed release

Базовый порядок:

```bash
helm status myapp
helm history myapp
helm get manifest myapp
kubectl get pods
kubectl describe pod <pod>
kubectl logs <pod>
```

Если проблема в templates:

```bash
helm template myapp ./chart --debug
```

---

## 11. Interview Questions

### Что такое Helm

Helm — package manager для Kubernetes.

Он устанавливает приложения через charts и управляет releases.

---

### Chart vs Release

Chart — пакет с templates и values.

Release — установленный экземпляр chart в Kubernetes.

```text
Chart -> helm install -> Release
```

---

### Что такое values.yaml

`values.yaml` — файл с параметрами chart по умолчанию.

Templates используют эти значения при рендеринге.

---

### Как работают шаблоны Helm

Helm берет templates и values, подставляет значения и генерирует Kubernetes YAML.

```text
templates + values -> rendered manifests
```

Потом эти manifests применяются в cluster.

---

### include vs template

`template` просто вставляет named template.

`include` возвращает результат как строку, поэтому его можно использовать в pipeline.

На практике чаще используют `include`.

---

### default vs required

`default` задает значение по умолчанию.

```yaml
{{ .Values.replicas | default 1 }}
```

`required` требует обязательное значение и падает с ошибкой, если его нет.

---

### helm install vs helm upgrade

`helm install` создает новый release.

`helm upgrade` обновляет существующий release.

Часто используют:

```bash
helm upgrade --install myapp ./chart
```

---

### Как работает rollback

Helm хранит revisions release.

`helm rollback` применяет старую revision.

```bash
helm rollback myapp 2
```

---

### Что такое Subchart

Subchart — зависимый chart внутри parent chart.

Например, app chart может подключать PostgreSQL chart.

---

### Как дебажить Helm Chart

Основные команды:

```bash
helm lint
helm template --debug
helm install --dry-run --debug
helm get manifest
helm get values
kubectl describe
kubectl logs
```

Сначала проверить rendered YAML, потом Kubernetes resources.

---

### Почему используют Helm вместо обычных YAML

Helm используют, потому что он дает:

- templates;
    
- values для разных окружений;
    
- install/upgrade/rollback;
    
- dependencies;
    
- versioned releases;
    
- переиспользование chart.
    

---

### Как организовать Helm Charts для production

Обычно:

- один chart на приложение;
    
- values files для dev/stage/prod;
    
- secrets отдельно от values;
    
- resources/probes/securityContext обязательны;
    
- templates DRY через helpers;
    
- chart versioning;
    
- CI с `helm lint` и `helm template`;
    
- GitOps/CI/CD для деплоя.