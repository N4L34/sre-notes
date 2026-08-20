[[GitLab CI - GitHub Actions - Jenkins. Конспект]]
# GitLab CI / GitHub Actions / Jenkins

## 1. Основы

### Архитектура

У всех CI/CD-систем идея похожая:

```text
Repository -> CI/CD system -> Runner/Agent -> Jobs -> Result
```

Система читает pipeline config из репозитория и запускает задачи на runner/agent.

---

### Runner / Agent

Runner или Agent — машина, которая выполняет pipeline jobs.

Это может быть:

- VM;

- Docker container;

- Kubernetes Pod;

- bare-metal server.


В GitLab чаще говорят **Runner**, в Jenkins — **Agent**, в GitHub Actions — **Runner**.

---

### Pipeline Configuration File

Pipeline configuration file — файл, где описан pipeline.

Примеры:

```text
GitLab CI       -> .gitlab-ci.yml
GitHub Actions  -> .github/workflows/*.yml
Jenkins         -> Jenkinsfile
```

В нем описывают jobs, stages, variables, conditions и deploy.

---

### Pipeline Execution

Pipeline execution — процесс выполнения pipeline.

Обычно:

```text
trigger -> checkout code -> run jobs -> save artifacts -> deploy/report
```

Pipeline может запускаться по push, PR/MR, tag, schedule или вручную.

---

## 2. Pipeline Configuration

### Stages

Stages — крупные этапы pipeline.

Пример:

```text
build -> test -> scan -> deploy
```

Обычно следующий stage стартует только после успешного завершения предыдущего.

---

### Jobs

Job — конкретная задача внутри pipeline.

Например:

```text
build-image
unit-tests
helm-deploy
```

Jobs могут выполняться параллельно, если они в одном stage или явно настроены через dependencies.

---

### Steps

Steps — отдельные команды внутри job.

Например в GitHub Actions:

```yaml
steps:
  - uses: actions/checkout@v4
  - run: npm test
```

В GitLab CI аналогом чаще является список команд в `script`.

---

### Variables

Variables — переменные для pipeline.

Примеры:

```text
IMAGE_TAG
REGISTRY_URL
APP_ENV
KUBE_NAMESPACE
```

Используются для настройки build, test и deploy.

---

### Conditions

Conditions определяют, когда запускать job.

Например:

- только для `main`;

- только для tags;

- только для pull/merge request;

- только вручную;

- если изменились конкретные файлы.


---

### Matrix Builds

Matrix builds запускают одну job в разных вариантах.

Например:

```text
Python 3.10
Python 3.11
Python 3.12
```

Или разные OS:

```text
ubuntu
windows
macos
```

---

### Docker Images

CI job может выполняться внутри Docker image.

Пример:

```text
python:3.12
node:22
golang:1.22
docker:latest
```

Это делает окружение pipeline более воспроизводимым.

---

### Services

Services — дополнительные контейнеры рядом с job.

Например:

```text
postgres
redis
mysql
docker:dind
```

Используются для integration tests или сборки Docker images.

---

## 3. Deploy Pipeline

### Build Docker Image

Pipeline собирает Docker image из Dockerfile.

```bash
docker build -t registry.example.com/myapp:$CI_COMMIT_SHA .
```

Обычно tag делают по commit SHA, branch или release tag.

---

### Push Docker Image

После сборки image отправляют в registry.

```bash
docker push registry.example.com/myapp:$CI_COMMIT_SHA
```

Перед push нужен login в registry.

---

### Deploy Kubernetes

Deploy в Kubernetes часто делают через:

```bash
kubectl apply -f manifests/
```

Или через Helm:

```bash
helm upgrade --install myapp ./chart
```

В production чаще используют Helm или GitOps.

---

### Manual Deploy

Manual deploy — ручное подтверждение перед деплоем.

Используется для:

- production;

- database migrations;

- risky changes;

- rollback;

- infrastructure changes.


Это защита от случайного deploy.

---

### Rollback

Rollback — откат на предыдущую версию.

Kubernetes:

```bash
kubectl rollout undo deployment/myapp
```

Helm:

```bash
helm rollback myapp 3
```

Хороший pipeline должен иметь понятный rollback path.

---

## 4. Практика

### Собрать Docker Image

Минимальный шаг:

```bash
docker build -t myapp:latest .
```

В CI лучше использовать уникальный tag:

```bash
docker build -t myapp:$COMMIT_SHA .
```

---

### Запустить тесты

Пример:

```bash
pytest
npm test
go test ./...
mvn test
```

Tests должны запускаться до build/deploy.

---

### Запушить образ в Registry

Пример:

```bash
docker login registry.example.com
docker push registry.example.com/myapp:$COMMIT_SHA
```

Credentials лучше хранить в CI/CD secrets.

---

### Задеплоить в Kubernetes

Пример через Helm:

```bash
helm upgrade --install myapp ./chart \
  --namespace prod \
  --set image.tag=$COMMIT_SHA
```

После deploy нужно проверить rollout status.

---

### Использовать Secrets

Secrets нужны для:

- registry login;

- kubeconfig;

- cloud credentials;

- tokens;

- webhook URLs.


Их нельзя хранить в репозитории.

---

### Настроить Multi-stage Pipeline

Пример структуры:

```text
build
test
security
package
deploy-dev
deploy-prod
```

Production deploy часто делают manual.

---

## 5. Особенности инструментов

## GitLab CI

### `.gitlab-ci.yml`

Главный файл GitLab CI.

Пример:

```yaml
stages:
  - test
  - deploy

test:
  stage: test
  script:
    - pytest
```

Лежит в корне репозитория.

---

### GitLab Runner

GitLab Runner выполняет jobs.

Runner может работать через executors:

```text
shell
docker
kubernetes
ssh
```

В DevOps часто используют Docker или Kubernetes executor.

---

### rules

`rules` управляют запуском jobs.

Пример:

```yaml
rules:
  - if: '$CI_COMMIT_BRANCH == "main"'
```

Через `rules` можно запускать jobs только для main, tags или merge requests.

---

### artifacts

Artifacts сохраняют результат job.

Пример:

```yaml
artifacts:
  paths:
    - dist/
```

Их можно передавать в следующие jobs или скачать из UI.

---

### cache

Cache ускоряет pipeline.

Пример:

```yaml
cache:
  paths:
    - node_modules/
```

Cache не должен быть единственным местом хранения важных данных.

---

## GitHub Actions

### Workflow

Workflow — pipeline в GitHub Actions.

Файлы лежат здесь:

```text
.github/workflows/
```

Пример:

```yaml
name: CI
on: [push]
```

---

### Event

Event запускает workflow.

Примеры:

```text
push
pull_request
workflow_dispatch
schedule
release
```

`workflow_dispatch` — ручной запуск.

---

### Marketplace Actions

Marketplace Actions — готовые reusable actions.

Например:

```yaml
- uses: actions/checkout@v4
- uses: docker/login-action@v3
```

Они помогают не писать всё вручную.

---

### GitHub Runners

GitHub Runners выполняют jobs.

Бывают:

- GitHub-hosted;

- self-hosted.


GitHub-hosted runners часто используют `ubuntu-latest`.

---

### GitHub Secrets

GitHub Secrets хранят sensitive values.

Например:

```text
DOCKER_PASSWORD
KUBE_CONFIG
AWS_ACCESS_KEY_ID
```

В workflow они доступны через:

```yaml
${{ secrets.DOCKER_PASSWORD }}
```

---

## Jenkins

### Jenkinsfile

`Jenkinsfile` описывает pipeline Jenkins.

Обычно лежит в репозитории.

Пример:

```groovy
pipeline {
  agent any

  stages {
    stage('Test') {
      steps {
        sh 'pytest'
      }
    }
  }
}
```

---

### Declarative Pipeline

Declarative Pipeline — более структурированный формат Jenkinsfile.

Пример:

```groovy
pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'make build'
      }
    }
  }
}
```

Обычно проще для поддержки.

---

### Scripted Pipeline

Scripted Pipeline — более гибкий Groovy-код.

Пример:

```groovy
node {
  stage('Build') {
    sh 'make build'
  }
}
```

Гибче, но сложнее читать и поддерживать.

---

### Plugins

Jenkins сильно зависит от plugins.

Плагины нужны для:

- Git;

- Docker;

- Kubernetes;

- credentials;

- Slack;

- Blue Ocean;

- cloud integrations.


Минус — plugins нужно обновлять и поддерживать.

---

### Jenkins Agents

Jenkins Agent — node, где выполняются jobs.

Controller управляет pipeline, agents делают работу.

```text
Jenkins Controller -> Jenkins Agent -> build/test/deploy
```

Agents могут быть статическими или динамическими, например в Kubernetes.

---

## 6. Interview Questions

### Runner vs Agent

Runner/Agent — исполнитель pipeline jobs.

Разные системы называют это по-разному:

```text
GitLab CI       -> Runner
GitHub Actions  -> Runner
Jenkins         -> Agent
```

Смысл один: машина, где выполняются команды pipeline.

---

### GitLab CI vs GitHub Actions

GitLab CI тесно встроен в GitLab и хорошо подходит для полного DevOps-процесса в одном месте.

GitHub Actions тесно встроен в GitHub и имеет большой Marketplace actions.

Оба умеют:

- build;

- test;

- artifacts;

- cache;

- secrets;

- deploy;

- self-hosted runners.


---

### Declarative vs Scripted Pipeline

Declarative Pipeline — структурированный и более читаемый формат Jenkins.

Scripted Pipeline — гибкий Groovy-код.

```text
Declarative -> проще и стандартизированнее
Scripted    -> гибче, но сложнее
```

Для большинства команд лучше Declarative.

---

### Как работает Pipeline

Упрощенно:

```text
trigger -> CI/CD system reads config -> runner starts job -> commands run -> artifacts/logs saved -> result shown
```

Pipeline автоматизирует путь от commit до проверки, сборки и деплоя.