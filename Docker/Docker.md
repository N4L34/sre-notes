[[Docker. Конспект ]]

## 1. Основы

- [ ] Что такое Docker
- [ ] Контейнеры vs Virtual Machines
- [ ] Docker architecture
- [ ] Docker Engine
- [ ] Docker daemon
- [ ] Docker CLI
- [ ] Docker Hub
- [ ] Docker Registry
- [ ] Images
- [ ] Containers

---

## 2. Docker Images

- [ ] Что такое Image
- [ ] Layers
- [ ] Copy-on-Write
- [ ] Pull image
- [ ] Build image
- [ ] Tag image
- [ ] Push image
- [ ] Remove image
- [ ] Inspect image
- [ ] Image history

---

## 3. Dockerfile

- [ ] Dockerfile basics
- [ ] FROM
- [ ] RUN
- [ ] COPY
- [ ] ADD
- [ ] WORKDIR
- [ ] CMD
- [ ] ENTRYPOINT
- [ ] ENV
- [ ] ARG
- [ ] EXPOSE
- [ ] USER
- [ ] LABEL
- [ ] Multi-stage builds
- [ ] Best practices

---

## 4. Работа с контейнерами

- [ ] docker run
- [ ] docker exec
- [ ] docker attach
- [ ] docker logs
- [ ] docker ps
- [ ] docker inspect
- [ ] docker stop
- [ ] docker start
- [ ] docker restart
- [ ] docker rm
- [ ] Exit codes

---

## 5. Volumes

- [ ] Bind Mount
- [ ] Named Volume
- [ ] Anonymous Volume
- [ ] Persistent data
- [ ] docker volume
- [ ] Mount options

---

## 6. Networking

- [ ] Bridge network
- [ ] Host network
- [ ] None network
- [ ] Custom network
- [ ] Port mapping
- [ ] Container DNS
- [ ] Container communication

---

## 7. Environment

- [ ] Environment variables
- [ ] .env
- [ ] Secrets basics
- [ ] Passing variables
- [ ] Docker inspect env

---

## 8. Docker Compose

- [ ] docker-compose.yml
- [ ] Services
- [ ] Networks
- [ ] Volumes
- [ ] Environment
- [ ] Depends_on
- [ ] Build
- [ ] Restart policy
- [ ] Healthcheck
- [ ] docker compose up/down
- [ ] Override files

---

## 9. Docker Registry

- [ ] Docker Hub
- [ ] Private Registry
- [ ] Login
- [ ] Push
- [ ] Pull
- [ ] Image tags

---

## 10. Безопасность

- [ ] Root vs non-root container
- [ ] Image scanning basics
- [ ] Docker secrets basics
- [ ] Capabilities basics
- [ ] Read-only filesystem
- [ ] Best security practices

---

## 11. Troubleshooting

- [ ] Container exited
- [ ] CrashLoop (Docker)
- [ ] Permission denied
- [ ] Port already allocated
- [ ] Volume issues
- [ ] DNS issues
- [ ] Network issues
- [ ] Image pull failed
- [ ] Out of disk space
- [ ] Out of memory

---

## 12. Практика

- [ ] Dockerize Python application
- [ ] Dockerize Nginx
- [ ] Dockerize PostgreSQL
- [ ] Docker Compose (App + DB)
- [ ] Backup Docker Volume
- [ ] Просмотр логов контейнера
- [ ] Debug контейнера через exec
- [ ] Создание собственного Dockerfile
- [ ] Multi-stage build
- [ ] Очистка Docker (`docker system prune`)

---

## 13. Interview Questions

- [ ] Что такое Docker
- [ ] Container vs VM
- [ ] Image vs Container
- [ ] Что такое Layer
- [ ] Что делает COPY
- [ ] COPY vs ADD
- [ ] CMD vs ENTRYPOINT
- [ ] ENV vs ARG
- [ ] Что такое Volume
- [ ] Bind Mount vs Volume
- [ ] Как работают сети Docker
- [ ] Bridge vs Host network
- [ ] Как попасть внутрь контейнера
- [ ] Как посмотреть логи контейнера
- [ ] Как уменьшить размер образа
- [ ] Что такое Multi-stage build
- [ ] Почему контейнер сразу завершился
- [ ] Как дебажить контейнер