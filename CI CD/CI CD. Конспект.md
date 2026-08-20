# CI/CD

## 1. Основы

### Что такое CI

CI — Continuous Integration.

Это практика, когда изменения кода регулярно вливаются в общий репозиторий и автоматически проверяются.

Обычно CI включает:

- build;

- tests;

- lint;

- security checks.


---

### Что такое CD

CD — Continuous Delivery или Continuous Deployment.

Это автоматизация доставки приложения после CI.

То есть код не просто проверяется, а готовится к выкладке или сразу деплоится.

---

### Continuous Delivery vs Continuous Deployment

Continuous Delivery — приложение автоматически готовится к deploy, но запуск deploy часто требует ручного approve.

Continuous Deployment — каждый успешный pipeline автоматически деплоит изменения в окружение.

```text
Delivery   -> готово к деплою, нужен approve
Deployment -> деплой автоматически
```

---

### Pipeline

Pipeline — цепочка автоматических шагов.

Пример:

```text
build -> test -> scan -> push image -> deploy
```

Pipeline запускается при commit, merge request, tag или вручную.

---

### Stages

Stages — крупные этапы pipeline.

Например:

```text
build
test
security
deploy
```

Обычно stages выполняются по порядку.

---

### Jobs

Job — конкретная задача внутри stage.

Например:

```text
unit-tests
docker-build
helm-deploy
```

В одном stage может быть несколько jobs.

---

### Runners / Agents

Runner или agent — машина, которая выполняет jobs.

Это может быть:

- VM;

- контейнер;

- Kubernetes Pod;

- self-hosted server;

- cloud runner.


---

### Triggers

Triggers — события, которые запускают pipeline.

Например:

- push в branch;

- pull/merge request;

- tag;

- schedule;

- manual run;

- API call.


---

## 2. Pipeline

### Build

Build — сборка приложения.

Например:

```bash
go build
npm run build
mvn package
```

Цель — получить готовый artifact или проверить, что проект собирается.

---

### Tests

Tests проверяют, что код работает правильно.

Обычно бывают:

- unit tests;

- integration tests;

- e2e tests.


Если tests падают, deploy обычно запрещается.

---

### Lint

Lint проверяет стиль и базовые ошибки кода.

Примеры:

```bash
eslint
flake8
golangci-lint
shellcheck
yamllint
```

Lint помогает лов ить ошибки до runtime.

---

### Security Scan

Security scan ищет уязвимости и проблемы безопасности.

Может проверять:

- dependencies;

- Docker images;

- secrets;

- IaC configs;

- SAST.


Примеры инструментов:

```text
Trivy
Snyk
Gitleaks
Checkov
Semgrep
```

---

### Build Docker Image

Pipeline собирает Docker image.

```bash
docker build -t myapp:1.0.0 .
```

Обычно tag берут из commit SHA, branch или release tag.

---

### Push Image

После сборки image отправляют в registry.

```bash
docker push registry.example.com/myapp:1.0.0
```

Registry может быть:

- Docker Hub;

- GitLab Registry;

- GitHub Container Registry;

- AWS ECR;

- Harbor.


---

### Deploy

Deploy — выкладка приложения в окружение.

Например:

```bash
kubectl apply -f manifests/
helm upgrade --install myapp ./chart
```

Deploy может быть в dev, stage или prod.

---

### Rollback

Rollback — откат на предыдущую рабочую версию.

Примеры:

```bash
kubectl rollout undo deployment/myapp
helm rollback myapp 3
```

Rollback нужен, если новая версия сломала production.

---

## 3. Artifacts и Cache

### Artifacts

Artifacts — файлы, созданные pipeline и передаваемые между jobs или сохраняемые после выполнения.

Примеры:

```text
binary
test report
coverage report
docker image metadata
compiled frontend
```

Artifacts — это результат конкретного pipeline.

---

### Cache

Cache — временное хранилище для ускорения pipeline.

Примеры:

```text
npm cache
pip cache
maven cache
go build cache
docker layers
```

Cache можно пересоздать, он не должен быть единственным источником важных данных.

---

### Docker Registry

Docker Registry хранит Docker images.

Pipeline обычно делает:

```text
build image -> tag image -> push image -> deploy image
```

Registry нужен, чтобы Kubernetes или сервер мог скачать image.

---

### Package Registry

Package Registry хранит пакеты приложения или библиотек.

Примеры:

```text
npm packages
Python wheels
Maven artifacts
Helm charts
Generic packages
```

Используется для versioned delivery зависимостей и artifacts.

---

## 4. Variables и Secrets

### Environment Variables

Environment variables — переменные окружения для pipeline.

Например:

```text
APP_ENV=prod
REGISTRY_URL=registry.example.com
IMAGE_TAG=abc123
```

Их используют для настройки jobs.

---

### Secrets

Secrets — чувствительные данные.

Например:

- tokens;

- passwords;

- SSH keys;

- cloud credentials;

- registry credentials.


Secrets нельзя хранить прямо в репозитории.

---

### Credentials

Credentials — данные для авторизации.

Например:

```text
Docker registry login
Kubernetes kubeconfig
AWS access key
GitHub token
```

В CI/CD их хранят в protected secrets/variables.

---

### Protected Variables

Protected variables доступны только для защищенных веток или tags.

Например:

```text
main
master
release/*
v1.0.0
```

Так production secrets не попадут в pipeline из обычной feature-ветки.

---

## 5. Pipeline Best Practices

### Parallel Jobs

Parallel jobs выполняются одновременно.

Например:

```text
unit-tests
lint
security-scan
```

Это ускоряет pipeline.

---

### Reusable Pipelines

Reusable pipelines позволяют не копировать одинаковую логику.

Например:

- общий job для Docker build;

- общий deploy template;

- общий security scan;

- общий Helm deploy.


Это упрощает поддержку CI/CD.

---

### Manual Approval

Manual approval — ручное подтверждение перед важным этапом.

Например:

```text
deploy to production
destroy infrastructure
database migration
```

Часто используется в Continuous Delivery.

---

### Retry

Retry повторяет job при временной ошибке.

Полезно для нестабильных внешних зависимостей:

- registry;

- package mirror;

- cloud API;

- network.


Но retry не должен скрывать настоящие баги.

---

### Notifications

Notifications сообщают о статусе pipeline.

Например:

- Slack;

- Telegram;

- email;

- Microsoft Teams.


Обычно уведомляют о failed deploy, failed tests или successful production release.

---

### Multi-stage Pipeline

Multi-stage pipeline разделяет процесс на этапы.

Пример:

```text
build -> test -> scan -> package -> deploy dev -> deploy prod
```

Так проще контролировать качество и безопасность релиза.

---

## 6. Troubleshooting

### Failed Build

Failed build значит приложение не собралось.

Проверить:

- build logs;

- версии зависимостей;

- Dockerfile;

- package manager;

- переменные окружения.


Часто причина — разница между local environment и CI runner.

---

### Failed Tests

Failed tests значит проверки не прошли.

Проверить:

- test logs;

- flaky tests;

- test data;

- environment variables;

- dependencies;

- network calls.


Если тест нестабильный, его надо чинить, а не просто rerun бесконечно.

---

### Failed Deploy

Failed deploy значит приложение не выкатилось.

Проверить:

```bash
kubectl get pods
kubectl describe pod
kubectl logs
kubectl rollout status deployment/myapp
```

Частые причины:

- неправильный image tag;

- нет secret;

- ошибка Helm chart;

- readiness probe failed;

- нет ресурсов в cluster.


---

### Missing Artifacts

Missing artifacts значит следующий job не нашел нужные файлы.

Причины:

- artifact не был сохранен;

- неправильный path;

- artifact expired;

- job dependency не настроена;

- build job упал.


---

### Cache Problems

Cache problems могут давать странные ошибки.

Например:

- старая зависимость;

- битый cache;

- несовместимые версии;

- cache key слишком общий.


Иногда помогает очистить cache и пересобрать pipeline.

---

### Rollback

Rollback нужен, если deploy сломал окружение.

В Kubernetes:

```bash
kubectl rollout undo deployment/myapp
```

В Helm:

```bash
helm rollback myapp <revision>
```

Важно после rollback понять root cause, а не просто откатиться и забыть.

---

## 7. Interview Questions

### CI vs CD

CI — автоматическая проверка изменений кода.

CD — автоматическая доставка или деплой приложения после успешных проверок.

```text
CI -> build/test/scan
CD -> release/deploy
```

---

### Delivery vs Deployment

Continuous Delivery — система готовит релиз, но deploy в production обычно подтверждается вручную.

Continuous Deployment — deploy в production происходит автоматически после успешного pipeline.

---

### Cache vs Artifact

Artifact — результат pipeline, который нужен дальше или сохраняется.

Cache — временное хранилище для ускорения pipeline.

```text
artifact -> важный результат
cache    -> ускорение
```

---

### Blue-Green vs Canary

Blue-Green — есть две версии окружения: старая и новая.

Traffic переключается сразу с blue на green.

Canary — новая версия получает маленькую часть traffic, потом доля постепенно увеличивается.

```text
Blue-Green -> быстрое переключение
Canary     -> постепенная раскатка
```

---

### Как выглядит хороший Pipeline

Хороший pipeline:

- быстрый;

- понятный;

- воспроизводимый;

- не хранит secrets в коде;

- запускает tests и security checks;

- собирает immutable artifacts/images;

- поддерживает rollback;

- разделяет dev/stage/prod;

- имеет manual approval для production;

- дает понятные logs и notifications.