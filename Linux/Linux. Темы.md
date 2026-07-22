# Linux для DevOps/SRE — список тем

## 1. База Linux

- [x] Что такое Linux, дистрибутив, ядро, user space
- [x] Отличия Ubuntu/Debian, CentOS/RHEL/Rocky/Alma
- [x] Структура Linux-системы
- [x] [[Что происходит при загрузке сервера]]
- [x] [[Что такое shell]]
- [x] Bash как основной инструмент DevOps/SRE
- [x] [[Работа через SSH]]
- [x] Где искать документацию: man, --help, /usr/share/doc

---

## 2. [[Filesystem Hierarchy]]

- [x] Что такое `/`
- [x] `/bin`
- [x] `/sbin`
- [x] `/usr`
- [x] `/usr/bin`
- [x] `/usr/sbin`
- [x] `/etc`
- [x] `/var`
- [x] `/var/log`
- [x] `/var/lib`
- [x] `/tmp`
- [x] `/home`
- [x] `/root`
- [x] `/opt`
- [x] `/mnt`
- [x] `/media`
- [x] `/dev`
- [x] `/proc`
- [x] `/sys`
- [x] `/run`
- [x] Где лежат конфиги
- [x] Где лежат логи
- [x] Где лежат данные сервисов
- [x] Где лежат бинарники
- [x] Какие директории виртуальные

---

## 3. [[Работа с файлами и директориями]]

- [x] `ls`
- [x] `cd`
- [x] `pwd`
- [x] `cp`
- [x] `mv`
- [x] `rm`
- [x] `mkdir`
- [x] `touch`
- [x] `cat`
- [x] `less`
- [x] `head`
- [x] `tail`
- [x] `tail -f`
- [x] `find`
- [x] `locate`
- [x] `stat`
- [x] `file`
- [x] Абсолютные и относительные пути
- [x] Hard links
- [x] Symbolic links
- [x] Разница hard link и symlink
- [x] Inode
- [x] Что будет, если удалить файл, который открыт процессом

---

## 4. [[Права доступа]]

- [x] Пользователи и группы
- [x] UID и GID
- [x] `/etc/passwd`
- [x] `/etc/shadow`
- [x] `/etc/group`
- [x] `chmod`
- [x] `chown`
- [x] `chgrp`
- [x] Права `rwx`
- [x] Права на файл
- [x] Права на директорию
- [x] Octal permissions: `644`, `755`, `600`, `700`
- [x] `umask`
- [x] SUID
- [x] SGID
- [x] Sticky bit
- [x] ACL
- [x] `getfacl`
- [x] `setfacl`
- [x] `sudo`
- [x] `/etc/sudoers`
- [x] `visudo`
- [x] Почему нельзя всем давать `sudo ALL=(ALL) NOPASSWD:ALL`

---

## 5. [[Пользователи, группы, авторизация]]

- [x] `useradd`
- [x] `usermod`
- [x] `userdel`
- [x] `groupadd`
- [x] `passwd`
- [x] SSH keys
- [x] `~/.ssh/authorized_keys`
- [x] `~/.ssh/config`
- [x] `sshd_config`
- [x] Password authentication vs key authentication
- [x] Root login через SSH
- [x] Как безопасно дать доступ на сервер
- [x] Как забрать доступ
- [x] Как посмотреть активные сессии
- [x] `who`
- [x] `w`
- [x] `last`

---

## 6. [[Bash и shell]]

- [x] Переменные окружения
- [x] `export`
- [x] `$PATH`
- [x] `$HOME`
- [x] `$USER`
- [x] `$?`
- [x] stdin, stdout, stderr
- [x] Redirects: `>`, `>>`, `<`
- [x] Pipe: `|`
- [x] `grep`
- [x] `awk`
- [x] `sed`
- [x] `cut`
- [x] `sort`
- [x] `uniq`
- [x] `wc`
- [x] `xargs`
- [x] Command substitution: `$(...)`
- [x] Exit codes
- [x] Bash scripts
- [x] `if`
- [x] `for`
- [x] `while`
- [x] Functions
- [x] Arguments: `$1`, `$2`, `$@`, `$#`
- [x] `set -e`
- [x] `set -u`
- [x] `set -o pipefail`
- [x] Cron-friendly scripts
- [x] Idempotent scripts

---

## 7. [[Процессы]]

- [x] Что такое процесс
- [x] PID
- [x] PPID
- [x] `ps`
- [x] `top`
- [x] `htop`
- [x] `pgrep`
- [x] `pidof`
- [x] `kill`
- [x] `kill -9`
- [x] `kill -15`
- [x] Signals
- [x] `SIGTERM`
- [x] `SIGKILL`
- [x] `SIGHUP`
- [x] Zombie processes
- [x] Orphan processes
- [x] Foreground/background jobs
- [x] `jobs`
- [x] `fg`
- [x] `bg`
- [x] `nohup`
- [x] `disown`
- [x] `nice`
- [x] `renice`
- [x] Как найти процесс, который грузит CPU
- [x] Как найти процесс, который ест память
- [x] Как найти процесс, который держит файл
- [x] Как найти процесс, который слушает порт

---

## 8. [[Systemd и сервисы]]

- [x] Что такое init system
- [x] Что такое systemd
- [x] `systemctl`
- [x] `systemctl status`
- [x] `systemctl start`
- [x] `systemctl stop`
- [x] `systemctl restart`
- [x] `systemctl reload`
- [x] `systemctl enable`
- [x] `systemctl disable`
- [x] `systemctl daemon-reload`
- [x] Unit files
- [x] Service unit
- [x] Timer unit
- [x] Target
- [x] `WantedBy`
- [x] `ExecStart`
- [x] `Restart`
- [x] `RestartSec`
- [x] `Environment`
- [x] `EnvironmentFile`
- [x] Как написать свой systemd service
- [x] Почему сервис не стартует
- [x] Как смотреть логи systemd-сервиса
- [x] `journalctl`
- [x] `journalctl -u service`
- [x] `journalctl -f`
- [x] `journalctl --since`
- [x] Разница restart и reload

---

## 9. [[Логи]]

- [x] Где лежат логи в Linux
- [x] `/var/log`
- [x] `journalctl`
- [x] `dmesg`
- [x] `syslog`
- [x] `auth.log`
- [x] `kern.log`
- [x] `messages`
- [x] Логи nginx
- [x] Логи systemd-сервисов
- [x] `tail -f`
- [x] `grep`
- [x] `less`
- [x] `zgrep`
- [x] Log rotation
- [x] `logrotate`
- [x] Как найти ошибку в логах
- [x] Как читать логи по времени
- [x] Как понять, почему сервис упал
- [x] Как отличать application logs от system logs

---

## 10. [[Сеть в Linux]]

- [x] IP address
- [x] Subnet
- [x] Gateway
- [x] DNS
- [x] Routing
- [x] TCP
- [x] UDP
- [x] ICMP
- [x] Ports
- [x] Listening socket
- [x] Established connection
- [x] `ip addr`
- [x] `ip route`
- [x] `ss`
- [x] `netstat`
- [x] `ping`
- [x] `traceroute`
- [x] `mtr`
- [x] `dig`
- [x] `nslookup`
- [x] `host`
- [x] `curl`
- [x] `wget`
- [x] `telnet`
- [x] `nc`
- [x] `/etc/hosts`
- [x] `/etc/resolv.conf`
- [x] DNS troubleshooting
- [x] Проверить, открыт ли порт
- [x] Проверить, слушает ли сервис порт
- [x] Проверить, доступен ли внешний адрес
- [x] Проверить TLS-сертификат через `openssl s_client`
- [x] MTU
- [x] NAT
- [x] Firewall basics
- [x] `iptables`
- [x] `nftables`
- [x] `ufw`
- [x] `firewalld`

---

## 11. [[Пакетные менеджеры]]

- [x] `apt`
- [x] `apt update`
- [x] `apt upgrade`
- [x] `apt install`
- [x] `apt remove`
- [x] `dpkg`
- [x] Репозитории apt
- [x] GPG keys
- [x] `yum`
- [x] `dnf`
- [x] `rpm`
- [x] Как поставить пакет
- [x] Как удалить пакет
- [x] Как найти, из какого пакета файл
- [x] Как зафиксировать версию пакета
- [x] Как обновлять сервер безопасно
- [x] Security updates
- [x] Unattended upgrades

---

## 12. [[Диски, разделы и файловые системы]]

- [x] Block devices
- [x] `/dev/sda`
- [x] `/dev/nvme0n1`
- [x] Partitions
- [x] `lsblk`
- [x] `blkid`
- [x] `fdisk`
- [x] `parted`
- [x] Filesystems
- [x] ext4
- [x] xfs
- [x] btrfs
- [x] Mount
- [x] Unmount
- [x] `/etc/fstab`
- [x] `mount`
- [x] `umount`
- [x] `df -h`
- [x] `du -sh`
- [x] Inodes
- [x] `df -i`
- [x] Что делать, если закончилось место
- [x] Что делать, если закончились inodes
- [x] LVM
- [x] Physical Volume
- [x] Volume Group
- [x] Logical Volume
- [x] Resize filesystem
- [x] Swap
- [x] `swapon`
- [x] `swapoff`

---

## 13. [[Производительность Linux]]

- [x] CPU usage
- [x] Load average
- [x] Memory usage
- [x] Disk I/O
- [x] Network I/O
- [x] `top`
- [x] `htop`
- [x] `uptime`
- [x] `free -m`
- [x] `vmstat`
- [x] `iostat`
- [x] `iotop`
- [x] `sar`
- [x] `pidstat`
- [x] `dstat`
- [x] `mpstat`
- [x] `lsof`
- [x] `strace`
- [x] Как понять, CPU bottleneck или I/O bottleneck
- [x] Что такое load average
- [x] Почему load average может быть высокий при низком CPU
- [x] Page cache
- [x] Buffers/cache
- [x] OOM Killer
- [x] Как найти, кто съел память
- [x] Как найти, кто пишет на диск
- [x] Как найти, кто открыл много файлов

---

## 14. [[Kernel basics]]

- [x] Что такое Linux kernel
- [x] Kernel modules
- [x] `uname -a`
- [x] `lsmod`
- [x] `modprobe`
- [x] `sysctl`
- [x] `/proc`
- [x] `/sys`
- [x] Kernel parameters
- [x] `dmesg`
- [x] OOM Killer
- [x] File descriptors
- [x] Limits
- [x] `ulimit`
- [x] `/etc/security/limits.conf`
- [x] `sysctl.conf`
- [x] `net.ipv4.ip_forward`
- [x] `vm.swappiness`
- [x] `fs.file-max`

---

## 15.[[ Cron и scheduled jobs]]

- [x] `cron`
- [x] `crontab`
- [x] `/etc/crontab`
- [x] `/etc/cron.d`
- [x] `/etc/cron.daily`
- [x] Cron syntax
- [x] Environment в cron
- [x] Почему скрипт работает вручную, но не работает в cron
- [x] Логи cron
- [x] Systemd timers
- [x] Разница cron и systemd timer
- [x] Как сделать регулярный job
- [x] Как дебажить cron job

---

## 16. [[Архивы и сжатие]]

- [x] `tar`
- [x] `gzip`
- [x] `gunzip`
- [x] `zip`
- [x] `unzip`
- [x] `xz`
- [x] `bzip2`
- [x] Как создать архив
- [x] Как распаковать архив
- [x] Как архивировать директорию
- [x] Как смотреть содержимое архива
- [x] Как передать архив по SSH

---

## 17. [[SSH и удалённый доступ]]

- [x] Как работает SSH
- [x] Public/private key
- [x] `ssh-keygen`
- [x] `ssh-copy-id`
- [x] `authorized_keys`
- [x] SSH agent
- [x] `ssh-agent`
- [x] `ssh-add`
- [x] SSH config
- [x] Jump host
- [x] Bastion host
- [x] Port forwarding
- [x] Local forwarding
- [x] Remote forwarding
- [x] `scp`
- [x] `rsync`
- [x] Как безопасно настроить SSH
- [x] Как дебажить SSH-подключение через `ssh -v`

---

## 18. [[Безопасность Linux]]

- [x] Минимизация прав
- [x] Принцип least privilege
- [x] Обновления безопасности
- [x] Firewall
- [x] SSH hardening
- [x] Fail2ban
- [x] Audit logs
- [x] `auditd`
- [x] SELinux basics
- [x] AppArmor basics
- [x] Разница SELinux и AppArmor
- [x] Как временно проверить проблему с SELinux
- [x] Почему нельзя просто отключать SELinux на проде
- [x] Secrets в Linux
- [x] Права на приватные ключи
- [x] Права на конфиги с паролями
- [x] Проверка открытых портов
- [x] Проверка лишних пользователей
- [x] Проверка sudo-доступов

---

## 19. Troubleshooting

- [ ] Сервер недоступен по SSH
- [ ] Не резолвится DNS
- [ ] Сервис не стартует
- [ ] Сервис стартует и сразу падает
- [ ] Порт занят
- [ ] Нет места на диске
- [ ] Закончились inodes
- [ ] Высокий CPU
- [ ] Высокий load average
- [ ] Высокое потребление памяти
- [ ] OOM Killer убивает процесс
- [ ] Медленный диск
- [ ] Медленная сеть
- [ ] Permission denied
- [ ] Connection refused
- [ ] Connection timed out
- [ ] No route to host
- [ ] Too many open files
- [ ] Read-only filesystem
- [ ] Как строить гипотезы
- [ ] Как проверять гипотезы командами
- [ ] Как не ломать прод при диагностике

---

## 20. Практические сценарии для собеседований

- [ ] Найти, почему nginx не стартует
- [ ] Найти, какой процесс слушает порт 8080
- [ ] Найти, почему сервер тормозит
- [ ] Найти, кто занял весь диск
- [ ] Найти большие файлы
- [ ] Найти удалённый, но всё ещё открытый файл
- [ ] Найти процесс, который ест CPU
- [ ] Найти процесс, который ест память
- [ ] Проверить, работает ли DNS
- [ ] Проверить, доступен ли сервис по TCP
- [ ] Проверить HTTP-ответ через curl
- [ ] Проверить TLS-сертификат
- [ ] Написать systemd unit
- [ ] Написать bash-скрипт для healthcheck
- [ ] Настроить cron job
- [ ] Добавить пользователя с SSH-доступом
- [ ] Ограничить sudo-доступ
- [ ] Смонтировать новый диск
- [ ] Увеличить LVM-раздел
- [ ] Разобрать логи падения сервиса
- [ ] Объяснить, что делать при alert: DiskSpaceLow
- [ ] Объяснить, что делать при alert: HighLoad
- [ ] Объяснить, что делать при alert: ServiceDown