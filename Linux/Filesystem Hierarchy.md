### `/`

/ - это  корень файловой системы Linux
Абсолютные пути всегда начинаются с /

Не путать с /root. /root - домашняя директория пользователя root

---
### `/bin`

`/bin` — базовые пользовательские бинарники

Там лежали команды, нужные системе даже в минимальном режиме:

```
ls
cp
mv
cat
mkdir
rm
sh
bash
```

на современных linux это часто ссылка на ``` /usr/bin```

---
### `/usr`
/usr - большая директория с программами, библиотеками, документацией и системными файлами.

```Важно: `/usr` — это не “user home”.```

Исторически название связано с **Unix System Resources** / user system resources.

Внутри обычно:

```
/usr/bin      обычные программы
/usr/sbin     админские программы
/usr/lib      библиотеки
/usr/share    общие данные, docs, man pages
/usr/local    локально установленные программы
```
---
### `/usr/bin`

`/usr/bin` — основное место, где лежат пользовательские команды.

Например:

```
/usr/bin/python3  
/usr/bin/git  
/usr/bin/curl  
/usr/bin/vim  
/usr/bin/ssh  
/usr/bin/systemctl
```
---
### `/usr/sbin`

`/usr/sbin` — системные команды для администрирования.

Например:

```
/usr/sbin/sshd
/usr/sbin/nginx
/usr/sbin/useradd
/usr/sbin/groupadd
```
---
### `/etc`

`/etc` — **конфиги системы и сервисов**.

Etcetera

Изначально в Unix туда клали разные системные файлы, которые не подходили в другие категории. Со временем `/etc` стал стандартным местом для **конфигурационных файлов системы и сервисов**.


Это одна из самых важных директорий для DevOps/SRE.

Примеры:

```
/etc/ssh/sshd_config
/etc/nginx/nginx.conf
/etc/fstab
/etc/passwd
/etc/group
/etc/hosts
/etc/resolv.conf
/etc/systemd/system/
```

Если спрашивают:

> Где лежат конфиги в Linux?

Базовый ответ:

```
В /etc
```

Пример:

```
cat /etc/ssh/sshd_config
cat /etc/fstab
ls /etc/nginx/
```
---
### `/var`

`/var` — изменяемые данные системы.

`var` = variable data.

Там лежит то, что постоянно меняется:

```
логи
кэши
очереди
данные сервисов
spool-файлы
runtime-данные некоторых приложений
```

Примеры:

```
/var/log
/var/lib
/var/cache
/var/spool
/var/tmp
```

---
### `/var/log`

`/var/log` — логи.

Очень важная директория для SRE.

Примеры:

```
/var/log/syslog
/var/log/auth.log
/var/log/kern.log
/var/log/nginx/access.log
/var/log/nginx/error.log
/var/log/audit/audit.log
```
---
### `/var/lib`

`/var/lib` — данные сервисов и приложений.

Это прям очень важная директория.

Примеры:

```
/var/lib/docker
/var/lib/postgresql
/var/lib/mysql
/var/lib/redis
/var/lib/systemd
/var/lib/kubelet
```

Если сервис хранит своё состояние, БД, metadata, volume data — часто это будет где-то в `/var/lib`.

Например:

```
ls /var/lib/dockerls /var/lib/postgresqlls /var/lib/kubelet
```
---
### `/tmp`

`/tmp` — временные файлы.

Туда программы могут складывать временные данные.

```
/tmp
```

Особенности:

```
часто очищается после перезагрузки
обычно доступен всем пользователям
имеет специальные права
```

---
### `/home`

`/home` — домашние директории обычных пользователей.

Пример:

```
/home/alex
/home/deploy
/home/ubuntu
```

Когда обычный пользователь логинится, он обычно попадает в свою home-директорию:

```
cd ~pwd
```

Например:

```
/home/ubuntu
```

---
### `/root`

`/root` — домашняя директория пользователя `root`.

Не путать:

```
/       — корень файловой системы
/root   — home-директория root-пользователя
```

Если ты root:

```
cd ~pwd
```

получишь:

```
/root
```

---
### `/opt`

`/opt` — optional software.

Обычно туда ставят сторонние приложения, которые не идут стандартно через пакетный менеджер.

Примеры:

```
/opt/google
/opt/teamcity
/opt/app
/opt/backup-agent
```

Для DevOps часто встречается:

```
/opt/company-app
/opt/scripts
/opt/tools
```

Но это зависит от организации.

---
### `/mnt`

`/mnt` — временное ручное монтирование файловых систем.

Например, ты подключил дополнительный диск:

```
sudo mount /dev/sdb1 /mnt
```

Или создал точку:

```
sudo mkdir /mnt/data
sudo mount /dev/sdb1 /mnt/data
```

Обычно `/mnt` используют для ручных/админских mount-операций.

---
### `/media`

`/media` — автоматическое монтирование removable devices.

Например:

```
/media/usb
/media/alex/MyFlashDrive
```

Часто используется для:

```
USB-флешек
внешних дисков
CD/DVD
```

Разница грубо:

```
/mnt    — ручное монтирование админом
/media  — авто-монтирование внешних устройств
```
---
### `/dev`

`/dev` — устройства как файлы.

В Linux многие устройства представлены как файлы.

Примеры:

```
/dev/sda
/dev/sda1
/dev/nvme0n1
/dev/null
/dev/zero
/dev/random
/dev/tty
```

Диски:

```
/dev/sda
/dev/sdb
/dev/nvme0n1
```

Разделы:

```
/dev/sda1
/dev/sda2
/dev/nvme0n1p1
```

Специальные устройства:

```
/dev/null    — “чёрная дыра”, всё выбрасывает
/dev/zero    — генерирует нули
/dev/random  — случайные данные
```

Пример:

```
echo hello > /dev/null
```

Вывод просто исчезнет.

---
### `/proc`

`/proc` — виртуальная файловая система с информацией о процессах и kernel.

Это не обычные файлы на диске.

Примеры:

```
/proc/cpuinfo
/proc/meminfo
/proc/uptime
/proc/loadavg
/proc/mounts
/proc/1
/proc/self
```

Посмотреть CPU:

```
cat /proc/cpuinfo
```

Посмотреть память:

```
cat /proc/meminfo
```

Посмотреть информацию о процессе PID 1:

```
ls /proc/1
```

Например:

```
cat /proc/1/cmdline
```

`/proc` создаётся kernel’ом в памяти и показывает текущее состояние системы.

---
### `/sys`

`/sys` — виртуальная файловая система для информации об устройствах, драйверах, kernel subsystem.

Она связана с:

```
devices
drivers
kernel modules
cgroups
block devices
network interfaces
```

Примеры:

```
/sys/class/net
/sys/block
/sys/devices
/sys/fs/cgroup
```

Посмотреть сетевые интерфейсы:

```
ls /sys/class/net
```

Посмотреть block devices:

```
ls /sys/block
```

Для DevOps/SRE `/sys` важен, но обычно ты не работаешь с ним каждый день напрямую, кроме специфичного дебага.

---

### `/run`

`/run` — runtime data после загрузки системы.

Там лежат временные данные запущенной системы:

```
PID-файлы
socket-файлы
lock-файлы
runtime state
```

Примеры:

```
/run/systemd/run/docker.sock/run/sshd/run/nginx.pid
```

Важно:

```
/run очищается после перезагрузки
```

То есть это не место для постоянного хранения данных. 