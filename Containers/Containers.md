[[Containers. Конспект]]

## 1. Основы

- [x] Что такое Container
- [x] Containers vs Virtual Machines
- [x] Container lifecycle
- [x] OCI (Open Container Initiative)
- [x] Container runtime

---

## 2. Linux Containers

- [x] Namespaces
- [x] PID namespace
- [x] Network namespace
- [x] Mount namespace
- [x] IPC namespace
- [x] UTS namespace
- [x] User namespace
- [x] cgroups
- [x] Resource limits

---

## 3. Filesystem

- [x] Union filesystem
- [x] OverlayFS
- [x] Copy-on-Write
- [x] Writable layer
- [x] Read-only layers

---

## 4. Container Runtime

- [x] containerd
- [x] runc
- [x] CRI
- [x] CRI-O
- [x] Docker Engine
- [x] kubelet и runtime

---

## 5. Container Networking

- [x] Network namespace
- [x] veth pair
- [x] Linux bridge
- [x] Container IP
- [x] Port mapping
- [x] DNS внутри контейнера

---

## 6. Container Storage

- [x] Writable layer
- [x] Volumes
- [x] Bind mounts
- [x] Tmpfs
- [x] Persistent data

---

## 7. Безопасность

- [x] Root vs non-root container
- [x] User namespace
- [x] Linux Capabilities
- [x] seccomp basics
- [x] AppArmor basics
- [x] SELinux basics
- [x] Read-only filesystem
- [x] Privileged container

---

## 8. Troubleshooting

- [x] Container не запускается
- [x] OOMKilled
- [x] CrashLoopBackOff (концепция)
- [x] Permission denied
- [x] Network недоступна
- [x] DNS не работает
- [x] Container exited
- [x] Resource limits

---

## 9. Практика

- [x] Посмотреть namespaces процесса
- [x] Ограничить CPU контейнера
- [x] Ограничить память контейнера
- [x] Проверить cgroups
- [x] Проверить OverlayFS
- [x] Debug контейнера
- [x] Проверить network namespace
- [x] Проверить capabilities

---

## 10. Interview Questions

- [x] Что такое контейнер
- [x] Container vs VM
- [x] Что такое namespace
- [x] Что такое cgroups
- [x] Что такое OverlayFS
- [x] Что такое Copy-on-Write
- [x] Что такое OCI
- [x] Что такое containerd
- [x] Что такое runc
- [x] Что такое CRI
- [x] Как Kubernetes запускает контейнер
- [x] Почему контейнер изолирован
- [x] Почему контейнер не является виртуальной машиной
- [x] Что происходит при запуске контейнера