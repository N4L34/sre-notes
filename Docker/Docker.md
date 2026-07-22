[[Docker. Конспект ]]

## 1. Основы

- [x] Что такое Docker
- [x] Контейнеры vs Virtual Machines
- [x] Docker architecture
- [x] Docker Engine
- [x] Docker daemon
- [x] Docker CLI
- [x] Docker Hub
- [x] Docker Registry
- [x] Images
- [x] Containers

---

## 2. Docker Images

- [x] Что такое Image
- [x] Layers
- [x] Copy-on-Write
- [x] Pull image
- [x] Build image
- [x] Tag image
- [x] Push image
- [x] Remove image
- [x] Inspect image
- [x] Image history

---

## 3. Dockerfile

- [x] Dockerfile basics
- [x] FROM
- [x] RUN
- [x] COPY
- [x] ADD
- [x] WORKDIR
- [x] CMD
- [x] ENTRYPOINT
- [x] ENV
- [x] ARG
- [x] EXPOSE
- [x] USER
- [x] LABEL
- [x] Multi-stage builds
- [x] Best practices

---

## 4. Работа с контейнерами

- [x] docker run
- [x] docker exec
- [x] docker attach
- [x] docker logs
- [x] docker ps
- [x] docker inspect
- [x] docker stop
- [x] docker start
- [x] docker restart
- [x] docker rm
- [x] Exit codes

---

## 5. Volumes

- [x] Bind Mount
- [x] Named Volume
- [x] Anonymous Volume
- [x] Persistent data
- [x] docker volume
- [x] Mount options

---

## 6. Networking

- [x] Bridge network
- [x] Host network
- [x] None network
- [x] Custom network
- [x] Port mapping
- [x] Container DNS
- [x] Container communication

---

## 7. Environment

- [x] Environment variables
- [x] .env
- [x] Secrets basics
- [x] Passing variables
- [x] Docker inspect env

---

## 8. Docker Compose

- [x] docker-compose.yml
- [x] Services
- [x] Networks
- [x] Volumes
- [x] Environment
- [x] Depends_on
- [x] Build
- [x] Restart policy
- [x] Healthcheck
- [x] docker compose up/down
- [x] Override files

---

## 9. Docker Registry

- [x] Docker Hub
- [x] Private Registry
- [x] Login
- [x] Push
- [x] Pull
- [x] Image tags

---

## 10. Безопасность

- [x] Root vs non-root container
- [x] Image scanning basics
- [x] Docker secrets basics
- [x] Capabilities basics
- [x] Read-only filesystem
- [x] Best security practices

---

## 11. Troubleshooting

- [x] Container exited
- [x] CrashLoop (Docker)
- [x] Permission denied
- [x] Port already allocated
- [x] Volume issues
- [x] DNS issues
- [x] Network issues
- [x] Image pull failed
- [x] Out of disk space
- [x] Out of memory

---

## 12. Практика

- [x] Dockerize Python application
- [x] Dockerize Nginx
- [x] Dockerize PostgreSQL
- [x] Docker Compose (App + DB)
- [x] Backup Docker Volume
- [x] Просмотр логов контейнера
- [x] Debug контейнера через exec
- [x] Создание собственного Dockerfile
- [x] Multi-stage build
- [x] Очистка Docker (`docker system prune`)

---

## 13. Interview Questions

- [x] Что такое Docker
- [x] Container vs VM
- [x] Image vs Container
- [x] Что такое Layer
- [x] Что делает COPY
- [x] COPY vs ADD
- [x] CMD vs ENTRYPOINT
- [x] ENV vs ARG
- [x] Что такое Volume
- [x] Bind Mount vs Volume
- [x] Как работают сети Docker
- [x] Bridge vs Host network
- [x] Как попасть внутрь контейнера
- [x] Как посмотреть логи контейнера
- [x] Как уменьшить размер образа
- [x] Что такое Multi-stage build
- [x] Почему контейнер сразу завершился
- [x] Как дебажить контейнер