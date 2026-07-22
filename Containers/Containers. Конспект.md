

## 1. Основы

### Что такое Container

Container — изолированный процесс с собственным окружением.

Внутри контейнера есть:

- приложение;
    
- зависимости;
    
- filesystem;
    
- переменные окружения;
    
- настройки запуска.
    

Контейнер использует kernel хостовой ОС.

---

### Containers vs Virtual Machines

VM запускает полноценную гостевую ОС.

Container изолирует процесс, но использует kernel хоста.

```text
VM:        app -> guest OS -> hypervisor -> host OS
Container: app -> namespaces/cgroups -> host kernel
```

Контейнеры легче и быстрее VM.

---

### Container lifecycle

Жизненный цикл контейнера:

```text
created -> running -> stopped -> removed
```

Основные действия:

- создать;
    
- запустить;
    
- остановить;
    
- удалить.
    

---

### OCI (Open Container Initiative)

OCI — стандарт для контейнеров.

Определяет:

- формат container image;
    
- как запускать container;
    
- как должен работать runtime.
    

Благодаря OCI разные runtime совместимы между собой.

---

### Container runtime

Container runtime — компонент, который запускает контейнеры.

Примеры:

```text
containerd
runc
CRI-O
Docker Engine
```

Runtime создает изоляцию, filesystem, namespaces и запускает процесс.

---

## 2. Linux Containers

### Namespaces

Namespaces — механизм Linux для изоляции процессов.

Они позволяют контейнеру иметь свои:

- процессы;
    
- сеть;
    
- hostname;
    
- mounts;
    
- users.
    

---

### PID namespace

PID namespace изолирует список процессов.

Процесс внутри контейнера может видеть себя как PID 1.

```bash
ps aux
```

Внутри контейнера будет виден только свой набор процессов.

---

### Network namespace

Network namespace изолирует сеть.

У контейнера могут быть свои:

- interfaces;
    
- IP;
    
- routing table;
    
- ports;
    
- firewall rules.
    

---

### Mount namespace

Mount namespace изолирует filesystem mounts.

Контейнер видит свой `/`, свои mount points и свою структуру файлов.

---

### IPC namespace

IPC namespace изолирует межпроцессное взаимодействие.

Например:

- shared memory;
    
- semaphores;
    
- message queues.
    

---

### UTS namespace

UTS namespace изолирует hostname и domain name.

Контейнер может иметь свой hostname.

```bash
hostname
```

---

### User namespace

User namespace изолирует пользователей и группы.

Например, root внутри контейнера может быть не root на хосте.

Это повышает безопасность.

---

### cgroups

cgroups ограничивают и учитывают ресурсы процессов.

Можно ограничить:

- CPU;
    
- memory;
    
- disk I/O;
    
- количество процессов.
    

---

### Resource limits

Resource limits — ограничения ресурсов контейнера.

Например:

```bash
docker run --memory 512m --cpus 1 nginx
```

Если контейнер превысит memory limit, его могут убить через OOM.

---

## 3. Filesystem

### Union filesystem

Union filesystem объединяет несколько слоев в один filesystem.

Контейнер видит это как обычную файловую систему.

Docker images используют layered filesystem.

---

### OverlayFS

OverlayFS — Linux filesystem для объединения слоев.

Обычно используется Docker/container runtime.

```text
lower layers -> read-only image layers
upper layer  -> writable container layer
```

---

### Copy-on-Write

Copy-on-Write означает: read-only слой не меняется.

Если контейнер изменяет файл из image, файл копируется в writable layer.

После этого изменения живут отдельно от image.

---

### Writable layer

Writable layer — верхний записываемый слой контейнера.

Все изменения внутри контейнера пишутся туда.

Если удалить контейнер, writable layer обычно удалится тоже.

---

### Read-only layers

Read-only layers — неизменяемые слои image.

Они создаются при build image.

Их можно переиспользовать между разными контейнерами.

---

## 4. Container Runtime

### containerd

`containerd` — container runtime высокого уровня.

Он управляет:

- images;
    
- containers;
    
- snapshots;
    
- lifecycle;
    
- запуском через `runc`.
    

Docker и Kubernetes могут использовать containerd.

---

### runc

`runc` — низкоуровневый OCI runtime.

Он непосредственно создает контейнер через Linux namespaces, cgroups и filesystem.

Проще:

```text
containerd -> runc -> container process
```

---

### CRI

CRI — Container Runtime Interface.

Это интерфейс Kubernetes для общения с container runtime.

Через CRI kubelet может работать с containerd или CRI-O.

---

### CRI-O

CRI-O — container runtime для Kubernetes.

Он реализует CRI и запускает OCI containers.

Часто используется как альтернатива containerd.

---

### Docker Engine

Docker Engine — платформа Docker для запуска контейнеров.

Включает:

- Docker daemon;
    
- Docker CLI;
    
- API;
    
- работу с images, networks, volumes.
    

---

### kubelet и runtime

`kubelet` не запускает контейнеры напрямую.

Он обращается к container runtime через CRI.

```text
kubelet -> CRI -> containerd/CRI-O -> runc -> container
```

---

## 5. Container Networking

### Network namespace

Network namespace дает контейнеру отдельную сетевую среду.

Внутри контейнера могут быть свои:

- IP;
    
- interfaces;
    
- routes;
    
- ports.
    

---

### veth pair

`veth pair` — пара виртуальных сетевых интерфейсов.

Один конец находится внутри контейнера, другой — на хосте.

```text
container eth0 <-> veth on host
```

---

### Linux bridge

Linux bridge соединяет контейнеры на одном хосте.

Docker bridge network обычно работает через Linux bridge.
 
```text
container -> veth -> bridge -> host network
```

---

### Container IP

Container IP — IP-адрес внутри container network.

Например, в Docker bridge network контейнер может получить адрес типа:

```text
172.17.0.2
```

Этот IP обычно доступен внутри Docker network, но не напрямую снаружи.

---

### Port mapping

Port mapping пробрасывает порт контейнера на порт хоста.

```bash
docker run -p 8080:80 nginx
```

Формат:

```text
host_port:container_port
```

---

### DNS внутри контейнера

Контейнер использует DNS-настройки, которые ему дает runtime.

В Docker custom network контейнеры могут резолвить друг друга по имени.

```text
app -> db:5432
```

---

## 6. Container Storage

### Writable layer

Writable layer хранит изменения контейнера.

Например:

- созданные файлы;
    
- измененные конфиги;
    
- временные данные.
    

Но это не лучший вариант для важных данных.

---

### Volumes

Volumes — способ хранить данные отдельно от lifecycle контейнера.

```bash
docker volume create data
docker run -v data:/var/lib/app app
```

Volumes подходят для persistent data.

---

### Bind mounts

Bind mount монтирует путь с хоста в контейнер.

```bash
docker run -v /host/path:/container/path nginx
```

Удобно для конфигов, разработки и доступа к файлам хоста.

---

### Tmpfs

Tmpfs хранит данные в памяти.

```bash
docker run --tmpfs /tmp nginx
```

После остановки контейнера данные исчезают.

Полезно для временных файлов и secrets.

---

### Persistent data

Persistent data — данные, которые должны пережить удаление контейнера.

Например:

- база данных;
    
- uploads;
    
- state приложения.
    

Для этого используют volumes или внешнее хранилище.

---

## 7. Безопасность

### Root vs non-root container

Root внутри контейнера — это риск.

Лучше запускать приложение от обычного пользователя.

```dockerfile
USER appuser
```

Это снижает последствия взлома контейнера.

---

### User namespace

User namespace позволяет маппить пользователей контейнера на других пользователей хоста.

Например:

```text
root in container != root on host
```

Это усиливает изоляцию.

---

### Linux Capabilities

Capabilities — отдельные привилегии Linux.

Вместо полного root можно дать только нужные права.

Пример:

```bash
docker run --cap-drop ALL nginx
```

Идея — минимизировать права контейнера.

---

### seccomp basics

seccomp ограничивает системные вызовы процесса.

Можно запретить опасные syscalls.

Docker по умолчанию использует seccomp profile.

---

### AppArmor basics

AppArmor ограничивает, что процесс может делать в системе.

Например:

- какие файлы читать;
    
- какие операции выполнять;
    
- какие capabilities использовать.
    

Это дополнительный слой безопасности.

---

### SELinux basics

SELinux — Mandatory Access Control в Linux.

Может ограничивать доступ контейнера к файлам и ресурсам хоста.

Часто встречается в RHEL/CentOS/Fedora/OpenShift.

---

### Read-only filesystem

Read-only filesystem запрещает запись в filesystem контейнера.

```bash
docker run --read-only nginx
```

Это уменьшает риск изменения файлов внутри контейнера.

---

### Privileged container

Privileged container получает почти полный доступ к хосту.

```bash
docker run --privileged image
```

Это опасно и должно использоваться только при реальной необходимости.

---

## 8. Troubleshooting

### Container не запускается

Сначала проверить:

```bash
docker ps -a
docker logs container_name
docker inspect container_name
```

Частые причины:

- неправильный command;
    
- нет env;
    
- ошибка приложения;
    
- нет прав;
    
- не найден image.
    

---

### OOMKilled

OOMKilled значит, контейнер убит из-за нехватки памяти.

Проверить:

```bash
docker inspect container_name
docker stats
```

Частый exit code:

```text
137
```

---

### CrashLoopBackOff

CrashLoopBackOff — концепция из Kubernetes.

Означает, что контейнер постоянно падает, а kubelet пытается его перезапустить.

Причины:

- приложение падает;
    
- неправильный config;
    
- нет secret/env;
    
- dependency недоступна.
    

---

### Permission denied

`Permission denied` обычно связан с правами.

Проверить:

- user внутри контейнера;
    
- права на файл;
    
- bind mount;
    
- SELinux/AppArmor;
    
- execute bit.
    

```bash
docker exec -it container id
ls -la
```

---

### Network недоступна

Проверить:

- network namespace;
    
- route;
    
- DNS;
    
- firewall;
    
- Docker network;
    
- port mapping.
    

```bash
docker network ls
docker network inspect bridge
```

---

### DNS не работает

Проверить внутри контейнера:

```bash
cat /etc/resolv.conf
nslookup example.com
```

В Docker custom network также проверить имена service/container.

---

### Container exited

Container exited значит, главный процесс завершился.

Контейнер живет только пока работает основной процесс.

Проверить:

```bash
docker logs container_name
docker ps -a
```

---

### Resource limits

Если контейнер ограничен по CPU/memory, приложение может тормозить или падать.

Проверить:

```bash
docker stats
docker inspect container_name
```

В Kubernetes смотреть requests/limits.

---

## 9. Практика

### Посмотреть namespaces процесса

На хосте можно посмотреть namespaces процесса:

```bash
ls -l /proc/<PID>/ns
```

Для контейнера сначала найти PID:

```bash
docker inspect -f '{{.State.Pid}}' container_name
```

---

### Ограничить CPU контейнера

Docker:

```bash
docker run --cpus 1 nginx
```

Можно также ограничивать shares:

```bash
docker run --cpu-shares 512 nginx
```

---

### Ограничить память контейнера

```bash
docker run --memory 512m nginx
```

Если контейнер превысит лимит, возможен OOMKilled.

---

### Проверить cgroups

Можно смотреть cgroups процесса:

```bash
cat /proc/<PID>/cgroup
```

Или через systemd/cgroup filesystem на хосте.

---

### Проверить OverlayFS

У контейнера можно посмотреть storage driver:

```bash
docker info | grep -i storage
docker inspect container_name
```

Часто будет:

```text
overlay2
```

---

### Debug контейнера

Базовый debug:

```bash
docker ps -a
docker logs container_name
docker inspect container_name
docker exec -it container_name sh
```

Смотреть:

- env;
    
- filesystem;
    
- network;
    
- process;
    
- logs;
    
- exit code.
    

---

### Проверить network namespace

Найти PID контейнера:

```bash
docker inspect -f '{{.State.Pid}}' container_name
```

Зайти в network namespace:

```bash
nsenter -t <PID> -n ip addr
```

---

### Проверить capabilities

Внутри контейнера:

```bash
grep Cap /proc/1/status
```

Также можно запускать контейнер с ограничениями:

```bash
docker run --cap-drop ALL nginx
```

---

## 10. Interview Questions

### Что такое контейнер

Контейнер — изолированный процесс с собственным filesystem, сетью и окружением.

Он использует kernel хостовой ОС.

---

### Container vs VM

VM виртуализирует целую ОС.

Container изолирует процессы на уровне Linux kernel.

Контейнер легче, быстрее стартует и потребляет меньше ресурсов.

---

### Что такое namespace

Namespace — механизм Linux для изоляции.

Например:

- PID namespace изолирует процессы;
    
- network namespace изолирует сеть;
    
- mount namespace изолирует filesystem.
    

---

### Что такое cgroups

cgroups — механизм Linux для ограничения и учета ресурсов.

Ограничивают:

- CPU;
    
- memory;
    
- I/O;
    
- количество процессов.
    

---

### Что такое OverlayFS

OverlayFS — filesystem, который объединяет несколько слоев.

Используется для layered container images.

---

### Что такое Copy-on-Write

Copy-on-Write — механизм, при котором read-only layer не меняется.

Если контейнер изменяет файл, копия создается в writable layer.

---

### Что такое OCI

OCI — стандарт контейнеров.

Он описывает:

- формат image;
    
- runtime specification;
    
- совместимость container runtimes.
    

---

### Что такое containerd

containerd — container runtime высокого уровня.

Он управляет images, containers, snapshots и запускает контейнеры через `runc`.

---

### Что такое runc

`runc` — низкоуровневый OCI runtime.

Он непосредственно создает и запускает контейнер через namespaces и cgroups.

---

### Что такое CRI

CRI — интерфейс Kubernetes для общения с container runtime.

Через CRI kubelet работает с containerd или CRI-O.

---

### Как Kubernetes запускает контейнер

Упрощенно:

```text
API Server -> kubelet -> CRI -> containerd/CRI-O -> runc -> container
```

Kubelet получает PodSpec и просит runtime запустить контейнер.

---

### Почему контейнер изолирован

Контейнер изолирован за счет Linux namespaces и cgroups.

Namespaces изолируют окружение, cgroups ограничивают ресурсы.

---

### Почему контейнер не является виртуальной машиной

Контейнер не запускает отдельный kernel.

Он использует kernel хоста и изолирует только процессы и окружение.

---

### Что происходит при запуске контейнера

Упрощенно:

```text
runtime получает image
создает filesystem layers
создает namespaces
настраивает cgroups
настраивает сеть
запускает главный процесс
```

Главный процесс контейнера становится PID 1 внутри контейнера.