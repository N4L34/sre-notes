[[Containers. Конспект]]

## 1. Основы

- [ ] Что такое Container
- [ ] Containers vs Virtual Machines
- [ ] Container lifecycle
- [ ] OCI (Open Container Initiative)
- [ ] Container runtime

---

## 2. Linux Containers

- [ ] Namespaces
- [ ] PID namespace
- [ ] Network namespace
- [ ] Mount namespace
- [ ] IPC namespace
- [ ] UTS namespace
- [ ] User namespace
- [ ] cgroups
- [ ] Resource limits

---

## 3. Filesystem

- [ ] Union filesystem
- [ ] OverlayFS
- [ ] Copy-on-Write
- [ ] Writable layer
- [ ] Read-only layers

---

## 4. Container Runtime

- [ ] containerd
- [ ] runc
- [ ] CRI
- [ ] CRI-O
- [ ] Docker Engine
- [ ] kubelet и runtime

---

## 5. Container Networking

- [ ] Network namespace
- [ ] veth pair
- [ ] Linux bridge
- [ ] Container IP
- [ ] Port mapping
- [ ] DNS внутри контейнера

---

## 6. Container Storage

- [ ] Writable layer
- [ ] Volumes
- [ ] Bind mounts
- [ ] Tmpfs
- [ ] Persistent data

---

## 7. Безопасность

- [ ] Root vs non-root container
- [ ] User namespace
- [ ] Linux Capabilities
- [ ] seccomp basics
- [ ] AppArmor basics
- [ ] SELinux basics
- [ ] Read-only filesystem
- [ ] Privileged container

---

## 8. Troubleshooting

- [ ] Container не запускается
- [ ] OOMKilled
- [ ] CrashLoopBackOff (концепция)
- [ ] Permission denied
- [ ] Network недоступна
- [ ] DNS не работает
- [ ] Container exited
- [ ] Resource limits

---

## 9. Практика

- [ ] Посмотреть namespaces процесса
- [ ] Ограничить CPU контейнера
- [ ] Ограничить память контейнера
- [ ] Проверить cgroups
- [ ] Проверить OverlayFS
- [ ] Debug контейнера
- [ ] Проверить network namespace
- [ ] Проверить capabilities

---

## 10. Interview Questions

- [ ] Что такое контейнер
- [ ] Container vs VM
- [ ] Что такое namespace
- [ ] Что такое cgroups
- [ ] Что такое OverlayFS
- [ ] Что такое Copy-on-Write
- [ ] Что такое OCI
- [ ] Что такое containerd
- [ ] Что такое runc
- [ ] Что такое CRI
- [ ] Как Kubernetes запускает контейнер
- [ ] Почему контейнер изолирован
- [ ] Почему контейнер не является виртуальной машиной
- [ ] Что происходит при запуске контейнера