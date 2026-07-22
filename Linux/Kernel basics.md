## Что такое Linux kernel

**Linux kernel** — это ядро операционной системы.

Ядро — главный слой между программами и железом.

Приложения не работают с CPU, RAM, диском и сетью напрямую. Они обращаются к ядру через **system calls**.

Пример:

```
nginx хочет открыть файл index.html
↓
вызывает open()
↓
ядро проверяет права
↓
ядро обращается к файловой системе
↓
файл читается с диска или из page cache
↓
nginx получает данные
```

Ядро отвечает за:

- процессы;
- память;
- файловые системы;
- сетевой стек;
- драйверы устройств;
- права доступа;
- системные вызовы;
- планировщик CPU;
- работу с дисками;
- безопасность на низком уровне.

Упрощённо:

```
Application    ↓System calls    ↓Linux kernel    ↓Hardware
```

Примеры системных вызовов:

```
open()read()write()fork()exec()socket()connect()bind()
```

---

## Kernel modules

**Kernel module** — это модуль ядра, который можно загрузить или выгрузить без перезагрузки системы.

Чаще всего это:

- драйвер сетевой карты;
- драйвер файловой системы;
- модуль для iptables/nftables;
- модуль для overlayfs;
- модуль для VPN;
- модуль для Kubernetes networking.

Пример:

```
ядро базовое+ модуль ext4+ модуль overlay+ модуль br_netfilter+ модуль ip_tables
```

Посмотреть загруженные модули:

```
lsmod
```

Пример вывода:

```
overlay               151552  2br_netfilter           32768  0ip_tables              32768  4
```

Загрузить модуль:

```
sudo modprobe overlay
```

Выгрузить модуль:

```
sudo modprobe -r overlay
```

Важно:

```
modprobe
```

сам подгружает зависимости, а низкоуровневая команда `insmod` просто пытается загрузить конкретный `.ko` файл.

---

## `uname -a`

Команда показывает информацию о ядре и системе:

```
uname -a
```

Пример:

```
Linux server01 6.8.0-40-generic #40-Ubuntu SMP x86_64 GNU/Linux
```

Что здесь важно:

```
Linux                 # тип ядраserver01              # hostname6.8.0-40-generic      # версия kernelUbuntu                # сборкаx86_64                # архитектураGNU/Linux             # ОС
```

Часто используют:

```
uname -r
```

Показывает только версию ядра:

```
6.8.0-40-generic
```

Это полезно, когда нужно понять:

- поддерживается ли нужный модуль;
- есть ли баг в конкретной версии kernel;
- подходит ли драйвер;
- совместим ли Kubernetes/container runtime;
- нужна ли перезагрузка после обновления ядра.

---

## `lsmod`

Показывает загруженные kernel modules:

```
lsmod
```

Пример:

```
Module                  Size  Used byoverlay               151552  3br_netfilter           32768  0ip_tables              32768  2
```

Колонки:

```
Module   # имя модуляSize     # размерUsed by  # кто использует
```

Например:

```
lsmod | grep overlay
```

Если `overlay` загружен, Docker/containerd сможет использовать overlay filesystem.

Для Kubernetes часто проверяют:

```
lsmod | grep br_netfilter
```

---

## `modprobe`

`modprobe` загружает или выгружает модули ядра.

Загрузить модуль:

```
sudo modprobe br_netfilter
```

Проверить:

```
lsmod | grep br_netfilter
```

Выгрузить:

```
sudo modprobe -r br_netfilter
```

Сделать загрузку модуля постоянной после reboot:

```
echo br_netfilter | sudo tee /etc/modules-load.d/k8s.conf
```

Пример для Kubernetes:

```
sudo modprobe overlaysudo modprobe br_netfilter
```

И постоянная загрузка:

```
cat <<EOF | sudo tee /etc/modules-load.d/k8s.confoverlaybr_netfilterEOF
```

---

## `sysctl`

`sysctl` — команда для просмотра и изменения параметров ядра во время работы системы.

Посмотреть параметр:

```
sysctl net.ipv4.ip_forward
```

Пример:

```
net.ipv4.ip_forward = 0
```

Изменить временно, до перезагрузки:

```
sudo sysctl -w net.ipv4.ip_forward=1
```

Изменить постоянно:

```
echo "net.ipv4.ip_forward = 1" | sudo tee /etc/sysctl.d/99-custom.confsudo sysctl --system
```

Или через старый общий файл:

```
sudo nano /etc/sysctl.conf
```

После изменения:

```
sudo sysctl -p
```

Посмотреть все параметры:

```
sysctl -a
```

---

## `/proc`

`/proc` — виртуальная файловая система, через которую можно смотреть информацию о процессах и ядре.

Это не обычные файлы на диске. Это интерфейс к данным ядра.

Примеры:

```
cat /proc/cpuinfocat /proc/meminfocat /proc/loadavgcat /proc/uptimecat /proc/version
```

Информация о конкретном процессе:

```
ls /proc/1234
```

Где `1234` — PID процесса.

Полезные файлы:

```
/proc/PID/cmdline/proc/PID/environ/proc/PID/fd/proc/PID/status/proc/PID/limits/proc/PID/mounts
```

Например, посмотреть file descriptors процесса:

```
ls -l /proc/1234/fd
```

Посмотреть лимиты процесса:

```
cat /proc/1234/limits
```

Посмотреть, чем запущен процесс:

```
cat /proc/1234/cmdline
```

---

## `/sys`

`/sys` — виртуальная файловая система для информации об устройствах, драйверах и kernel subsystems.

Если `/proc` больше про процессы и runtime-информацию, то `/sys` больше про устройства и ядро.

Примеры:

```
ls /sys/class/net
```

Покажет сетевые интерфейсы:

```
eth0lodocker0
```

Посмотреть MAC-адрес интерфейса:

```
cat /sys/class/net/eth0/address
```

Посмотреть состояние:

```
cat /sys/class/net/eth0/operstate
```

Пример:

```
up
```

Информация о block devices:

```
ls /sys/block
```

Пример:

```
sdanvme0n1loop0
```

---

## Kernel parameters

**Kernel parameters** — это параметры ядра.

Они бывают двух типов:

### 1. Boot-time параметры

Передаются ядру при загрузке через bootloader, например GRUB.

Пример:

```
quiet splash
```

Или:

```
systemd.unified_cgroup_hierarchy=1
```

Посмотреть текущие boot parameters:

```
cat /proc/cmdline
```

Пример:

```
BOOT_IMAGE=/boot/vmlinuz-6.8.0 root=/dev/sda2 ro quiet splash
```

### 2. Runtime параметры

Меняются через `sysctl`.

Пример:

```
sysctl vm.swappinesssysctl net.ipv4.ip_forwardsysctl fs.file-max
```

---

## `dmesg`

`dmesg` показывает kernel ring buffer — сообщения ядра.

Это логи ядра, особенно полезные после загрузки или при проблемах с железом, драйверами, дисками, памятью, OOM.

Посмотреть:

```
dmesg
```

Удобнее:

```
dmesg -T
```

С человеческим временем:

```
[Mon Jul  6 10:15:12 2026] eth0: Link is Up
```

Искать ошибки:

```
dmesg -T | grep -i errordmesg -T | grep -i faildmesg -T | grep -i oomdmesg -T | grep -i killed
```

Пример OOM:

```
Out of memory: Killed process 1234 (java) total-vm:...
```

Пример проблем с диском:

```
I/O error, dev sda
```

Пример проблем с сетью:

```
eth0: Link is Down
```

Важно:

```
journalctl -k
```

тоже показывает kernel logs через systemd journal.

---

## OOM Killer

**OOM Killer** — механизм ядра, который убивает процессы, когда системе не хватает памяти.

OOM = Out Of Memory.

Ситуация:

```
RAM закончиласьswap закончился или почти закончилсяядро не может выделить памятьядро выбирает процесс-жертвуубивает его
```

Пример в логах:

```
dmesg -T | grep -i "killed process"
```

Пример:

```
Out of memory: Killed process 2451 (java) total-vm:4096000kB, anon-rss:2048000kB
```

Как ядро выбирает процесс?

У каждого процесса есть оценка:

```
cat /proc/PID/oom_score
```

Чем выше `oom_score`, тем больше шанс, что процесс убьют.

Есть ещё настройка:

```
cat /proc/PID/oom_score_adj
```

Диапазон:

```
-1000 до 1000
```

Пример:

```
-1000  # почти нельзя убивать0      # обычное поведение1000   # кандидат на убийство
```

В Kubernetes это важно.

Например:

```
Guaranteed pod    → меньше шанс быть убитымBurstable pod     → средний рискBestEffort pod    → высокий риск
```

Проверить память:

```
free -m
```

Посмотреть, кто ест память:

```
ps aux --sort=-%mem | head
```

Или:

```
tophtop
```

---

## File descriptors

**File descriptor** — это числовой идентификатор открытого ресурса внутри процесса.

В Linux почти всё выглядит как файл:

- обычный файл;
- socket;
- pipe;
- device;
- terminal;
- log file;
- TCP connection.

Стандартные file descriptors:

```
0 — stdin1 — stdout2 — stderr
```

Пример:

```
ls > out.txt
```

Здесь stdout, то есть FD `1`, перенаправлен в файл.

Посмотреть открытые FD процесса:

```
ls -l /proc/PID/fd
```

Пример:

```
0 -> /dev/null1 -> /var/log/nginx/access.log2 -> /var/log/nginx/error.log3 -> socket:[123456]4 -> /etc/nginx/nginx.conf
```

Посмотреть через `lsof`:

```
sudo lsof -p PID
```

Посмотреть количество открытых FD:

```
ls /proc/PID/fd | wc -l
```

Частая ошибка:

```
Too many open files
```

Это значит, что процесс превысил лимит открытых файлов.

---

## Limits

**Limits** — ограничения ресурсов для пользователя или процесса.

Они нужны, чтобы один процесс или пользователь не положил всю систему.

Типы лимитов:

- максимальное количество открытых файлов;
- максимальное количество процессов;
- размер stack;
- размер core dump;
- locked memory;
- CPU time.

Посмотреть лимиты текущего shell:

```
ulimit -a
```

Пример:

```
open files                      (-n) 1024max user processes              (-u) 4096stack size                      (-s) 8192
```

Посмотреть лимиты процесса:

```
cat /proc/PID/limits
```

Пример:

```
Max open files            1024                 1048576Max processes             4096                 4096
```

Там обычно есть два значения:

```
soft limithard limit
```

---

## `ulimit`

`ulimit` управляет лимитами текущего shell и процессов, которые будут из него запущены.

Посмотреть всё:

```
ulimit -a
```

Посмотреть лимит открытых файлов:

```
ulimit -n
```

Временно увеличить:

```
ulimit -n 65535
```

Но это действует только для текущей shell-сессии и её дочерних процессов.

Пример:

```
ulimit -n 65535./my-server
```

Тогда `my-server` стартует с новым лимитом.

Для production-сервисов через systemd лучше настраивать в unit-файле:

```
[Service]LimitNOFILE=65535
```

После изменения:

```
sudo systemctl daemon-reloadsudo systemctl restart my-service
```

---

## `/etc/security/limits.conf`

Файл для настройки лимитов пользователей через PAM.

Открыть:

```
sudo nano /etc/security/limits.conf
```

Пример:

```
deploy soft nofile 65535deploy hard nofile 65535
```

Означает:

```
пользователь deploysoft limit open files = 65535hard limit open files = 65535
```

Для всех пользователей:

```
* soft nofile 65535* hard nofile 65535
```

Для группы:

```
@developers soft nofile 65535@developers hard nofile 65535
```

Важно:

`limits.conf` обычно влияет на login-сессии через PAM, но не всегда влияет на systemd-сервисы.

Для systemd-сервисов лучше:

```
[Service]LimitNOFILE=65535
```

---

## `sysctl.conf`

`/etc/sysctl.conf` — файл для постоянных runtime-параметров ядра.

Пример:

```
sudo nano /etc/sysctl.conf
```

Добавить:

```
net.ipv4.ip_forward = 1vm.swappiness = 10fs.file-max = 2097152
```

Применить:

```
sudo sysctl -p
```

Более современный и аккуратный способ — отдельные файлы:

```
/etc/sysctl.d/99-custom.conf
```

Пример:

```
sudo nano /etc/sysctl.d/99-custom.conf
```

Содержимое:

```
net.ipv4.ip_forward = 1vm.swappiness = 10fs.file-max = 2097152
```

Применить все sysctl-файлы:

```
sudo sysctl --system
```

---

## `net.ipv4.ip_forward`

Параметр включает маршрутизацию IPv4-пакетов через сервер.

Проверить:

```
sysctl net.ipv4.ip_forward
```

Пример:

```
net.ipv4.ip_forward = 0
```

Значения:

```
0 — сервер не пересылает пакеты между интерфейсами1 — сервер может работать как router
```

Временно включить:

```
sudo sysctl -w net.ipv4.ip_forward=1
```

Постоянно:

```
echo "net.ipv4.ip_forward = 1" | sudo tee /etc/sysctl.d/99-ip-forward.confsudo sysctl --system
```

Где нужно:

- router;
- NAT gateway;
- Docker networking;
- Kubernetes;
- VPN;
- WireGuard;
- iptables forwarding.

Пример:

```
client → server eth0 → server wg0 → another network
```

Без `ip_forward=1` сервер не будет пересылать пакеты между интерфейсами.

---

## `vm.swappiness`

`vm.swappiness` управляет тем, насколько активно ядро будет использовать swap.

Проверить:

```
sysctl vm.swappiness
```

Пример:

```
vm.swappiness = 60
```

Диапазон:

```
0–100
```

Упрощённо:

```
0   — почти не использовать swap10  — использовать swap осторожно60  — стандартное значение во многих системах100 — активнее использовать swap
```

Временно изменить:

```
sudo sysctl -w vm.swappiness=10
```

Постоянно:

```
echo "vm.swappiness = 10" | sudo tee /etc/sysctl.d/99-swappiness.confsudo sysctl --system
```

Важно понимать:

`swappiness = 0` не означает “никогда не использовать swap”.

Это значит “избегать swap насколько возможно”.

Для серверов с базами данных часто ставят меньше, например:

```
vm.swappiness = 1
```

или:

```
vm.swappiness = 10
```

Но зависит от нагрузки.

---

## `fs.file-max`

`fs.file-max` — глобальный лимит на количество file descriptors во всей системе.

Проверить:

```
sysctl fs.file-max
```

Пример:

```
fs.file-max = 9223372036854775807
```

Посмотреть текущее использование:

```
cat /proc/sys/fs/file-nr
```

Пример:

```
12345 0 2097152
```

Обычно это:

```
allocated unused max
```

То есть:

```
12345 открыто/выделено0 свободных из выделенных2097152 максимум
```

Изменить временно:

```
sudo sysctl -w fs.file-max=2097152
```

Постоянно:

```
echo "fs.file-max = 2097152" | sudo tee /etc/sysctl.d/99-file-max.confsudo sysctl --system
```

Важно:

`fs.file-max` — это глобальный лимит системы.

Но у процесса есть ещё свой лимит:

```
ulimit -n
```

То есть может быть так:

```
fs.file-max = 2097152ulimit -n = 1024
```

И приложение всё равно упадёт с:

```
Too many open files
```

потому что процессу разрешено только 1024 открытых файла.

---

# Практические команды

## Посмотреть версию ядра

```
uname -auname -r
```

## Посмотреть параметры загрузки ядра

```
cat /proc/cmdline
```

## Посмотреть модули ядра

```
lsmod
```

## Загрузить модуль

```
sudo modprobe overlay
```

## Проверить sysctl-параметр

```
sysctl net.ipv4.ip_forward
```

## Изменить временно

```
sudo sysctl -w net.ipv4.ip_forward=1
```

## Изменить постоянно

```
echo "net.ipv4.ip_forward = 1" | sudo tee /etc/sysctl.d/99-custom.confsudo sysctl --system
```

## Смотреть kernel logs

```
dmesg -Tjournalctl -k
```

## Найти OOM Killer

```
dmesg -T | grep -i oomdmesg -T | grep -i "killed process"journalctl -k | grep -i oom
```

## Посмотреть FD процесса

```
ls -l /proc/PID/fd
```

## Посчитать FD процесса

```
ls /proc/PID/fd | wc -l
```

## Посмотреть лимиты процесса

```
cat /proc/PID/limits
```

## Посмотреть лимиты shell

```
ulimit -aulimit -n
```

---

# Частые проблемы

## `Too many open files`

Причина:

```
процесс открыл слишком много файлов/socket'ов
```

Проверить:

```
ulimit -ncat /proc/PID/limitsls /proc/PID/fd | wc -l
```

Решение для systemd-сервиса:

```
[Service]LimitNOFILE=65535
```

---

## Процесс убило без понятной ошибки

Проверить OOM:

```
dmesg -T | grep -i killedjournalctl -k | grep -i oom
```

Если есть:

```
Out of memory: Killed process ...
```

значит процесс убил OOM Killer.

---

## Docker/Kubernetes сеть странно работает

Проверить:

```
lsmod | grep br_netfiltersysctl net.ipv4.ip_forward
```

Для Kubernetes часто нужно:

```
net.ipv4.ip_forward = 1
```

и модуль:

```
br_netfilter
```

---

## Изменил sysctl, но после reboot сбросилось

Скорее всего изменил только временно:

```
sudo sysctl -w ...
```

Нужно записать в:

```
/etc/sysctl.conf
```

или лучше:

```
/etc/sysctl.d/99-custom.conf
```

---

# Что важно на собеседовании

Хорошо уметь объяснить так:

> Linux kernel — это ядро ОС, которое управляет процессами, памятью, файловыми системами, сетью и доступом к железу. Приложения работают с ядром через system calls. Параметры ядра можно смотреть и менять через `sysctl`, информацию о процессах — через `/proc`, об устройствах — через `/sys`, а kernel logs — через `dmesg` или `journalctl -k`.

Про OOM Killer:

> OOM Killer — механизм ядра, который убивает процесс, если системе не хватает памяти. Проверяется через `dmesg -T`, `journalctl -k`, а вероятность убийства процесса можно посмотреть через `/proc/PID/oom_score`.

Про file descriptors:

> File descriptor — это номер открытого ресурса процесса: файл, socket, pipe и так далее. Ошибка `Too many open files` означает, что процесс достиг лимита. Проверяется через `ulimit -n`, `/proc/PID/limits`, `/proc/PID/fd`.

Про sysctl:

> `sysctl` меняет runtime-параметры ядра. Временное изменение делается через `sysctl -w`, постоянное — через `/etc/sysctl.conf` или `/etc/sysctl.d/*.conf`.