## 1. Основы

### Что такое Docker

Docker — платформа для запуска приложений в контейнерах.

Контейнер содержит приложение и всё, что нужно для его запуска:

```text
code
dependencies
runtime
config
```

---

### Контейнеры vs Virtual Machines

VM виртуализирует целую ОС.

Контейнер использует kernel хостовой ОС и изолирует процессы.

```text
VM:        app -> guest OS -> hypervisor -> host OS
Container: app -> container -> host kernel
```

Контейнеры обычно легче и быстрее VM.

---

### Docker architecture

Docker состоит из нескольких частей:

```text
Docker CLI -> Docker daemon -> Containers / Images / Networks / Volumes
```

Пользователь пишет команды через CLI, а daemon выполняет работу.

---

### Docker Engine

Docker Engine — основная часть Docker.

Включает:

- Docker daemon;

- Docker CLI;

- API для управления Docker.


---

### Docker daemon

Docker daemon — фоновый процесс Docker.

Обычно называется:

```bash
dockerd
```

Он создает контейнеры, скачивает images, управляет networks и volumes.

---

### Docker CLI

Docker CLI — командная строка для работы с Docker.

Примеры:

```bash
docker run nginx
docker ps
docker logs container_name
```

CLI отправляет команды Docker daemon.

---

### Docker Hub

Docker Hub — публичный registry с Docker images.

Например, оттуда можно скачать:

```bash
docker pull nginx
docker pull postgres
docker pull redis
```

---

### Docker Registry

Docker Registry — хранилище Docker images.

Может быть:

- публичным;

- приватным;

- self-hosted.


Примеры:

```text
Docker Hub
GitLab Container Registry
GitHub Container Registry
Amazon ECR
```

---

### Images

Image — шаблон для создания контейнера.

Например:

```bash
nginx:latest
postgres:16
redis:7
```

Из одного image можно создать много containers.

---

### Containers

Container — запущенный экземпляр image.

Можно думать так:

```text
image = шаблон
container = запущенный процесс из шаблона
```

---

## 2. Docker Images

### Что такое Image

Docker image — неизменяемый шаблон приложения.

Внутри обычно есть:

- filesystem;

- зависимости;

- runtime;

- настройки запуска.


---

### Layers

Image состоит из layers.

Каждая инструкция в Dockerfile обычно создает новый layer.

```dockerfile
FROM ubuntu
RUN apt update
COPY app.py /app/
```

Layers переиспользуются и кэшируются.

---

### Copy-on-Write

Copy-on-Write значит, что базовый image не изменяется.

Когда container что-то меняет, изменения пишутся в отдельный writable layer контейнера.

```text
image layers: read-only
container layer: writable
```

---

### Pull image

Скачать image из registry:

```bash
docker pull nginx:latest
```

Если tag не указан, Docker обычно использует `latest`.

---

### Build image

Собрать image из Dockerfile:

```bash
docker build -t myapp:1.0 .
```

`.` — build context, то есть директория с файлами для сборки.

---

### Tag image

Tag — имя и версия image.

```bash
docker tag myapp:1.0 myrepo/myapp:1.0
```

Обычно формат такой:

```text
registry/user/image:tag
```

---

### Push image

Отправить image в registry:

```bash
docker push myrepo/myapp:1.0
```

Перед этим обычно нужен login:

```bash
docker login
```

---

### Remove image

Удалить image локально:

```bash
docker rmi nginx:latest
```

Если от image есть containers, Docker может не дать удалить его.

---

### Inspect image

Посмотреть подробную информацию об image:

```bash
docker image inspect nginx
```

Показывает metadata, env, layers, command и т.д.

---

### Image history

Показать историю layers image:

```bash
docker history nginx
```

Полезно, чтобы понять, из каких шагов собран image.

---

## 3. Dockerfile

### Dockerfile basics

Dockerfile — файл с инструкциями для сборки image.

Пример:

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY app.py .
CMD ["python", "app.py"]
```

---

### FROM

`FROM` задает базовый image.

```dockerfile
FROM ubuntu:24.04
```

Или:

```dockerfile
FROM python:3.12-slim
```

С этой инструкции обычно начинается Dockerfile.

---

### RUN

`RUN` выполняет команду во время build.

```dockerfile
RUN apt update && apt install -y curl
```

Результат попадает в image layer.

---

### COPY

`COPY` копирует файлы из build context в image.

```dockerfile
COPY app.py /app/app.py
```

Обычно лучше использовать `COPY`, если не нужны дополнительные возможности `ADD`.

---

### ADD

`ADD` тоже копирует файлы, но умеет больше.

Например:

- распаковывать архивы;

- скачивать URL.


```dockerfile
ADD app.tar.gz /app/
```

В большинстве случаев лучше использовать `COPY`.

---

### WORKDIR

`WORKDIR` задает рабочую директорию внутри image.

```dockerfile
WORKDIR /app
```

После этого команды выполняются относительно `/app`.

---

### CMD

`CMD` задает команду по умолчанию при запуске контейнера.

```dockerfile
CMD ["python", "app.py"]
```

Ее можно переопределить в `docker run`.

---

### ENTRYPOINT

`ENTRYPOINT` задает основную команду контейнера.

```dockerfile
ENTRYPOINT ["nginx"]
```

Часто используется, если контейнер всегда должен запускать одну программу.

---

### ENV

`ENV` задает переменную окружения внутри image/container.

```dockerfile
ENV APP_ENV=production
```

Эту переменную увидит приложение.

---

### ARG

`ARG` задает build-time переменную.

```dockerfile
ARG VERSION=1.0
```

Используется во время сборки, но не всегда остается в runtime environment.

---

### EXPOSE

`EXPOSE` документирует порт, который слушает приложение.

```dockerfile
EXPOSE 8080
```

Важно: сам по себе `EXPOSE` не публикует порт наружу.

Для публикации нужен `-p`.

---

### USER

`USER` задает пользователя, от которого будет работать процесс.

```dockerfile
USER appuser
```

Хорошая практика — не запускать приложение от root.

---

### LABEL

`LABEL` добавляет metadata к image.

```dockerfile
LABEL maintainer="devops@example.com"
LABEL version="1.0"
```

Полезно для описания image.

---

### Multi-stage builds

Multi-stage build позволяет собирать приложение в одном image, а запускать в другом.

```dockerfile
FROM golang:1.22 AS build
WORKDIR /app
COPY . .
RUN go build -o app

FROM alpine
COPY --from=build /app/app /app
CMD ["/app"]
```

Так final image получается меньше.

---

### Best practices

Основные практики:

```text
использовать маленькие base images
не запускать от root
не хранить secrets в image
использовать .dockerignore
объединять RUN где нужно
чистить package cache
фиксировать версии images
```

---

## 4. Работа с контейнерами

### docker run

`docker run` создает и запускает container.

```bash
docker run nginx
```

С именем и портом:

```bash
docker run -d --name web -p 8080:80 nginx
```

---

### docker exec

`docker exec` запускает команду внутри уже работающего container.

```bash
docker exec -it web bash
```

Часто используют для диагностики.

---

### docker attach

`docker attach` подключается к основному процессу container.

```bash
docker attach web
```

Используется реже, чем `docker exec`.

---

### docker logs

Показывает logs container.

```bash
docker logs web
```

Следить в realtime:

```bash
docker logs -f web
```

---

### docker ps

Показывает запущенные containers.

```bash
docker ps
```

Показать все, включая остановленные:

```bash
docker ps -a
```

---

### docker inspect

Показывает подробную информацию о container.

```bash
docker inspect web
```

Можно увидеть:

- IP;

- mounts;

- env;

- network;

- command;

- state.


---

### docker stop

Останавливает container.

```bash
docker stop web
```

Docker отправляет процессу сигнал на завершение.

---

### docker start

Запускает уже существующий остановленный container.

```bash
docker start web
```

Это не создает новый container.

---

### docker restart

Перезапускает container.

```bash
docker restart web
```

По сути делает stop + start.

---

### docker rm

Удаляет остановленный container.

```bash
docker rm web
```

Принудительно удалить работающий:

```bash
docker rm -f web
```

---

### Exit codes

Container имеет exit code после завершения.

Посмотреть:

```bash
docker ps -a
```

Или:

```bash
docker inspect container_name
```

Обычно:

- `0` — успешное завершение;

- не `0` — ошибка.


---

## 5. Volumes

### Bind Mount

Bind mount монтирует директорию с хоста в container.

```bash
docker run -v /host/path:/container/path nginx
```

Полезно для dev-режима и конфигов.

---

### Named Volume

Named volume управляется Docker.

```bash
docker volume create pgdata
docker run -v pgdata:/var/lib/postgresql/data postgres
```

Лучше подходит для persistent data.

---

### Anonymous Volume

Anonymous volume создается без имени.

```bash
docker run -v /data nginx
```

Docker сам создаст volume, но его сложнее найти и обслуживать.

---

### Persistent data

Данные внутри container writable layer могут пропасть при удалении container.

Для важных данных используют volumes.

Например:

- базы данных;

- uploads;

- stateful data.


---

### docker volume

Команды для volume:

```bash
docker volume ls
docker volume inspect pgdata
docker volume rm pgdata
```

Volumes живут отдельно от containers.

---

### Mount options

Mount можно задавать через `--mount`.

```bash
docker run \
  --mount type=volume,source=pgdata,target=/data \
  nginx
```

`--mount` более явный, чем `-v`.

---

## 6. Networking

### Bridge network

Bridge — стандартная сеть Docker для containers.

Если запустить container без настроек, он обычно попадет в bridge network.

```bash
docker network ls
```

---

### Host network

Host network использует сеть хоста напрямую.

```bash
docker run --network host nginx
```

Container не получает отдельный network namespace.

Часто используют для performance или специфичных network-задач.

---

### None network

None network отключает сеть у container.

```bash
docker run --network none alpine
```

Контейнер будет без сетевого доступа.

---

### Custom network

Custom network — пользовательская Docker-сеть.

```bash
docker network create app-net
docker run --network app-net --name app myapp
docker run --network app-net --name db postgres
```

В custom network containers могут обращаться друг к другу по имени.

---

### Port mapping

Port mapping публикует порт container на host.

```bash
docker run -p 8080:80 nginx
```

Формат:

```text
host_port:container_port
```

Теперь nginx доступен на `localhost:8080`.

---

### Container DNS

В custom Docker network работает DNS по именам containers.

Например container `app` может обратиться к `db`:

```text
postgres://db:5432
```

Это удобно для связки сервисов.

---

### Container communication

Containers могут общаться:

- через custom bridge network;

- через published ports;

- через host network;

- через external network.


Лучше использовать custom network, а не связывать всё через localhost.

---

## 7. Environment

### Environment variables

Environment variables передают настройки в container.

```bash
docker run -e APP_ENV=prod myapp
```

Приложение внутри container увидит `APP_ENV`.

---

### .env

`.env` файл хранит переменные окружения.

Пример:

```text
APP_ENV=prod
DB_HOST=postgres
DB_PORT=5432
```

В Docker CLI можно передать так:

```bash
docker run --env-file .env myapp
```

---

### Secrets basics

Secrets — пароли, токены, ключи.

Их нельзя хранить в Dockerfile или image.

Плохо:

```dockerfile
ENV TOKEN=my-secret-token
```

Лучше использовать secret manager, CI/CD secrets или runtime env.

---

### Passing variables

Передать переменную можно через `-e`.

```bash
docker run -e DB_HOST=postgres -e DB_PORT=5432 myapp
```

Или через файл:

```bash
docker run --env-file .env myapp
```

---

### Docker inspect env

Посмотреть env container:

```bash
docker inspect container_name
```

Или точнее:

```bash
docker inspect container_name --format '{{json .Config.Env}}'
```

Важно: secrets в env могут быть видны через inspect.


## 8. Docker Compose

### `docker-compose.yml`

`docker-compose.yml` — файл, где описывают несколько контейнеров как один проект.

Пример:

```yaml
services:
  app:
    image: myapp:latest
  db:
    image: postgres:16
```

Удобно для локальной разработки и простых стендов.

---

### Services

Service — описание одного контейнера в Compose.

Например:

```yaml
services:
  nginx:
    image: nginx
```

Каждый service обычно соответствует одному приложению или компоненту.

---

### Networks

Networks в Compose задают сети для общения контейнеров.

```yaml
services:
  app:
    networks:
      - backend

networks:
  backend:
```

Контейнеры в одной сети могут обращаться друг к другу по имени service.

---

### Volumes

Volumes в Compose нужны для хранения данных.

```yaml
services:
  db:
    image: postgres
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

Так данные базы не пропадут после удаления контейнера.

---

### Environment

Environment задает переменные окружения.

```yaml
services:
  app:
    image: myapp
    environment:
      APP_ENV: production
      DB_HOST: db
```

Через env обычно передают настройки приложения.

---

### Depends_on

`depends_on` задает порядок запуска services.

```yaml
services:
  app:
    image: myapp
    depends_on:
      - db
```

Важно: `depends_on` не гарантирует, что база уже готова принимать подключения.

---

### Build

`build` говорит Compose собрать image из Dockerfile.

```yaml
services:
  app:
    build: .
```

Можно указать Dockerfile:

```yaml
services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
```

---

### Restart policy

Restart policy задает поведение при падении контейнера.

```yaml
services:
  app:
    image: myapp
    restart: unless-stopped
```

Частые варианты:

```text
no
always
on-failure
unless-stopped
```

---

### Healthcheck

Healthcheck проверяет здоровье контейнера.

```yaml
services:
  app:
    image: myapp
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
      interval: 30s
      timeout: 5s
      retries: 3
```

Полезно для диагностики и зависимостей.

---

### `docker compose up/down`

Запустить проект:

```bash
docker compose up -d
```

Остановить и удалить контейнеры:

```bash
docker compose down
```

Посмотреть логи:

```bash
docker compose logs -f
```

---

### Override files

Override files позволяют переопределять настройки Compose.

Например:

```text
docker-compose.yml
docker-compose.override.yml
```

Часто используют для dev/prod отличий.

---

## 9. Docker Registry

### Docker Hub

Docker Hub — публичный registry Docker images.

Примеры:

```bash
docker pull nginx
docker pull redis
docker pull postgres
```

Можно хранить свои public/private images.

---

### Private Registry

Private Registry — приватное хранилище Docker images.

Примеры:

```text
GitLab Container Registry
GitHub Container Registry
AWS ECR
GCP Artifact Registry
Harbor
```

Используется в компаниях для своих images.

---

### Login

Авторизация в registry:

```bash
docker login
```

Или для конкретного registry:

```bash
docker login registry.example.com
```

Нужна для push/pull приватных images.

---

### Push

Push отправляет image в registry.

```bash
docker push registry.example.com/myapp:1.0
```

Перед этим image должен иметь правильный tag.

---

### Pull

Pull скачивает image из registry.

```bash
docker pull nginx:latest
```

Или из private registry:

```bash
docker pull registry.example.com/myapp:1.0
```

---

### Image tags

Tag — версия или метка image.

Примеры:

```text
nginx:latest
postgres:16
myapp:1.0.3
myapp:prod
```

Лучше не полагаться на `latest` в production.

---

## 10. Безопасность

### Root vs non-root container

По умолчанию процесс в контейнере часто запускается от root.

Лучше запускать от отдельного пользователя.

```dockerfile
RUN useradd -m appuser
USER appuser
```

Это снижает последствия взлома контейнера.

---

### Image scanning basics

Image scanning ищет уязвимости в Docker image.

Проверяются:

- OS packages;

- зависимости приложения;

- известные CVE.


Примеры инструментов:

```text
Trivy
Grype
Docker Scout
GitLab/GitHub scanning
```

---

### Docker secrets basics

Secrets — пароли, токены, ключи.

Их нельзя хранить в Dockerfile или image.

Плохо:

```dockerfile
ENV DB_PASSWORD=supersecret
```

Лучше передавать secrets через secret manager или runtime-механизмы.

---

### Capabilities basics

Linux capabilities — отдельные привилегии процесса.

Docker позволяет добавлять или удалять capabilities.

Например, убрать лишнее:

```bash
docker run --cap-drop ALL nginx
```

Идея: контейнеру нужно давать минимум прав.

---

### Read-only filesystem

Можно запустить контейнер с read-only filesystem.

```bash
docker run --read-only nginx
```

Это усложняет запись вредных файлов внутрь контейнера.

Для временных файлов можно использовать tmpfs.

---

### Best security practices

Основные практики:

```text
не запускать от root
не хранить secrets в image
использовать минимальные base images
сканировать images
фиксировать версии
удалять лишние пакеты
не давать privileged без необходимости
ограничивать capabilities
использовать read-only filesystem где возможно
```

---

## 11. Troubleshooting

### Container exited

Контейнер завершился, если его главный процесс завершился.

Проверить:

```bash
docker ps -a
docker logs container_name
```

Часто причина — ошибка приложения или неправильный `CMD`.

---

### CrashLoop (Docker)

CrashLoop — контейнер постоянно падает и перезапускается.

Проверить:

```bash
docker logs container_name
docker inspect container_name
```

Причины:

- ошибка приложения;

- нет env;

- нет доступа к файлам;

- не готова база;

- неправильная команда запуска.


---

### Permission denied

`Permission denied` часто связан с правами.

Проверить:

- user внутри container;

- права на bind mount;

- владельца файлов;

- execute permissions.


```bash
docker exec -it container_name id
ls -la
```

---

### Port already allocated

Ошибка означает, что порт на host уже занят.

Например:

```text
port is already allocated
```

Проверить:

```bash
ss -tulpn | grep 8080
```

Решение — освободить порт или выбрать другой.

---

### Volume issues

Проблемы с volumes часто связаны с:

- неправильным путем;

- правами;

- владельцем файлов;

- пустым volume поверх данных image.


Проверить:

```bash
docker inspect container_name
docker volume ls
```

---

### DNS issues

Если контейнер не резолвит имена, проверить:

```bash
docker exec -it container_name cat /etc/resolv.conf
docker exec -it container_name nslookup google.com
```

В custom network сервисы должны резолвиться по имени service/container.

---

### Network issues

Проверить сети:

```bash
docker network ls
docker network inspect network_name
```

Внутри контейнера:

```bash
docker exec -it container_name ping other_container
docker exec -it container_name curl http://service:port
```

---

### Image pull failed

Image pull может падать из-за:

- неправильного имени image;

- неправильного tag;

- отсутствия доступа;

- проблем с registry;

- rate limit.


Проверить:

```bash
docker login
docker pull image:tag
```

---

### Out of disk space

Docker может занять много места images, containers, volumes и build cache.

Проверить:

```bash
docker system df
```

Очистка:

```bash
docker system prune
```

Осторожно с volumes.

---

### Out of memory

Контейнер может быть убит из-за нехватки памяти.

Проверить:

```bash
docker inspect container_name
docker stats
```

Признаки:

- exit code `137`;

- OOMKilled;

- резкое завершение процесса.


---

## 12. Практика

### Dockerize Python application

Обычно нужен `Dockerfile`.

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

Потом:

```bash
docker build -t python-app .
docker run python-app
```

---

### Dockerize Nginx

Простой Dockerfile для Nginx:

```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

Запуск:

```bash
docker build -t my-nginx .
docker run -p 8080:80 my-nginx
```

---

### Dockerize PostgreSQL

Обычно PostgreSQL запускают из готового image.

```bash
docker run -d \
  --name postgres \
  -e POSTGRES_PASSWORD=password \
  -v pgdata:/var/lib/postgresql/data \
  postgres:16
```

Для данных нужен volume.

---

### Docker Compose (App + DB)

Пример связки app + db:

```yaml
services:
  app:
    build: .
    environment:
      DB_HOST: db
    depends_on:
      - db

  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: password
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

---

### Backup Docker Volume

Один из вариантов — запустить временный контейнер и архивировать volume.

```bash
docker run --rm \
  -v pgdata:/data \
  -v "$PWD":/backup \
  alpine \
  tar -czf /backup/pgdata.tar.gz /data
```

Так можно сделать backup named volume.

---

### Просмотр логов контейнера

```bash
docker logs container_name
```

Follow mode:

```bash
docker logs -f container_name
```

Последние строки:

```bash
docker logs --tail 100 container_name
```

---

### Debug контейнера через exec

Зайти внутрь контейнера:

```bash
docker exec -it container_name bash
```

Если bash нет:

```bash
docker exec -it container_name sh
```

Дальше можно смотреть файлы, env, network.

---

### Создание собственного Dockerfile

Минимальный пример:

```dockerfile
FROM alpine
CMD ["echo", "hello"]
```

Сборка:

```bash
docker build -t myimage .
```

Запуск:

```bash
docker run myimage
```

---

### Multi-stage build

Multi-stage build уменьшает final image.

```dockerfile
FROM golang:1.22 AS build
WORKDIR /app
COPY . .
RUN go build -o app

FROM alpine
COPY --from=build /app/app /app
CMD ["/app"]
```

В final image не попадают build tools.

---

### Очистка Docker (`docker system prune`)

Показать использование места:

```bash
docker system df
```

Очистить неиспользуемое:

```bash
docker system prune
```

С volumes осторожно:

```bash
docker system prune --volumes
```

Эта команда может удалить важные данные.

---

## 13. Interview Questions

### Что такое Docker

Docker — платформа для запуска приложений в контейнерах.

Контейнеры позволяют упаковать приложение с зависимостями и запускать его одинаково в разных окружениях.

---

### Container vs VM

VM запускает полноценную гостевую ОС.

Container использует kernel хоста и изолирует процессы.

Контейнер обычно легче и быстрее VM.

---

### Image vs Container

Image — шаблон.

Container — запущенный экземпляр image.

```text
image -> docker run -> container
```

---

### Что такое Layer

Layer — слой Docker image.

Каждая инструкция Dockerfile обычно создает новый layer.

Layers кэшируются и переиспользуются.

---

### Что делает COPY

`COPY` копирует файлы из build context в image.

```dockerfile
COPY app.py /app/app.py
```

---

### COPY vs ADD

`COPY` просто копирует файлы.

`ADD` умеет дополнительно распаковывать архивы и работать с URL.

Обычно лучше использовать `COPY`.

---

### CMD vs ENTRYPOINT

`CMD` — команда по умолчанию, ее легко переопределить.

`ENTRYPOINT` — основная команда контейнера.

Часто используют вместе:

```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
```

---

### ENV vs ARG

`ARG` используется во время build.

`ENV` доступен внутри запущенного container.

```dockerfile
ARG VERSION=1.0
ENV APP_ENV=prod
```

---

### Что такое Volume

Volume — механизм хранения данных вне container writable layer.

Нужен для persistent data.

Например, для базы данных.

---

### Bind Mount vs Volume

Bind mount монтирует конкретный путь с хоста.

Volume управляется Docker.

```text
bind mount -> /host/path:/container/path
volume     -> volume_name:/container/path
```

Для production data обычно удобнее named volumes.

---

### Как работают сети Docker

Docker создает network namespace для контейнеров.

Контейнеры могут общаться через Docker networks.

В custom bridge network они могут обращаться друг к другу по имени.

---

### Bridge vs Host network

Bridge — контейнер получает отдельную Docker-сеть.

Host — контейнер использует сеть хоста напрямую.

```bash
docker run --network bridge nginx
docker run --network host nginx
```

---

### Как попасть внутрь контейнера

Через `docker exec`.

```bash
docker exec -it container_name bash
```

Если bash нет:

```bash
docker exec -it container_name sh
```

---

### Как посмотреть логи контейнера

```bash
docker logs container_name
```

Следить в реальном времени:

```bash
docker logs -f container_name
```

---

### Как уменьшить размер образа

Основные способы:

```text
использовать slim/alpine images
multi-stage build
удалять build dependencies
чистить package cache
использовать .dockerignore
не копировать лишние файлы
```

---

### Что такое Multi-stage build

Multi-stage build — сборка image в несколько этапов.

Например, в одном этапе собираем binary, во втором оставляем только готовый artifact.

Это уменьшает размер final image.

---

### Почему контейнер сразу завершился

Контейнер живет, пока жив его главный процесс.

Если команда завершилась — контейнер тоже завершится.

Проверить:

```bash
docker logs container_name
docker ps -a
```

---

### Как дебажить контейнер

Базовый порядок:

```bash
docker ps -a
docker logs container_name
docker inspect container_name
docker exec -it container_name sh
```

Проверить:

- env;

- command;

- ports;

- volumes;

- network;

- exit code;

- application logs.