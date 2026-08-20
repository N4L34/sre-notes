# Linux для DevOps/SRE — список тем

## 1. База Linux

- [ ] Что такое Linux, дистрибутив, ядро, user space
- [ ] Отличия Ubuntu/Debian, CentOS/RHEL/Rocky/Alma
- [ ] Структура Linux-системы
- [ ] [[Что происходит при загрузке сервера]]
- [ ] [[Что такое shell]]
- [ ] Bash как основной инструмент DevOps/SRE
- [ ] [[Работа через SSH]]
- [ ] Где искать документацию: man, --help, /usr/share/doc

---

## 2. [[Filesystem Hierarchy]]

- [ ] Что такое `/`
- [ ] `/bin`
- [ ] `/sbin`
- [ ] `/usr`
- [ ] `/usr/bin`
- [ ] `/usr/sbin`
- [ ] `/etc`
- [ ] `/var`
- [ ] `/var/log`
- [ ] `/var/lib`
- [ ] `/tmp`
- [ ] `/home`
- [ ] `/root`
- [ ] `/opt`
- [ ] `/mnt`
- [ ] `/media`
- [ ] `/dev`
- [ ] `/proc`
- [ ] `/sys`
- [ ] `/run`
- [ ] Где лежат конфиги
- [ ] Где лежат логи
- [ ] Где лежат данные сервисов
- [ ] Где лежат бинарники
- [ ] Какие директории виртуальные

---

## 3. [[Работа с файлами и директориями]]

- [ ] `ls`
- [ ] `cd`
- [ ] `pwd`
- [ ] `cp`
- [ ] `mv`
- [ ] `rm`
- [ ] `mkdir`
- [ ] `touch`
- [ ] `cat`
- [ ] `less`
- [ ] `head`
- [ ] `tail`
- [ ] `tail -f`
- [ ] `find`
- [ ] `locate`
- [ ] `stat`
- [ ] `file`
- [ ] Абсолютные и относительные пути
- [ ] Hard links
- [ ] Symbolic links
- [ ] Разница hard link и symlink
- [ ] Inode
- [ ] Что будет, если удалить файл, который открыт процессом

---

## 4. [[Права доступа]]

- [ ] Пользователи и группы
- [ ] UID и GID
- [ ] `/etc/passwd`
- [ ] `/etc/shadow`
- [ ] `/etc/group`
- [ ] `chmod`
- [ ] `chown`
- [ ] `chgrp`
- [ ] Права `rwx`
- [ ] Права на файл
- [ ] Права на директорию
- [ ] Octal permissions: `644`, `755`, `600`, `700`
- [ ] `umask`
- [ ] SUID
- [ ] SGID
- [ ] Sticky bit
- [ ] ACL
- [ ] `getfacl`
- [ ] `setfacl`
- [ ] `sudo`
- [ ] `/etc/sudoers`
- [ ] `visudo`
- [ ] Почему нельзя всем давать `sudo ALL=(ALL) NOPASSWD:ALL`

---

## 5. [[Пользователи, группы, авторизация]]

- [ ] `useradd`
- [ ] `usermod`
- [ ] `userdel`
- [ ] `groupadd`
- [ ] `passwd`
- [ ] SSH keys
- [ ] `~/.ssh/authorized_keys`
- [ ] `~/.ssh/config`
- [ ] `sshd_config`
- [ ] Password authentication vs key authentication
- [ ] Root login через SSH
- [ ] Как безопасно дать доступ на сервер
- [ ] Как забрать доступ
- [ ] Как посмотреть активные сессии
- [ ] `who`
- [ ] `w`
- [ ] `last`

---

## 6. [[Bash и shell]]

- [ ] Переменные окружения
- [ ] `export`
- [ ] `$PATH`
- [ ] `$HOME`
- [ ] `$USER`
- [ ] `$?`
- [ ] stdin, stdout, stderr
- [ ] Redirects: `>`, `>>`, `<`
- [ ] Pipe: `|`
- [ ] `grep`
- [ ] `awk`
- [ ] `sed`
- [ ] `cut`
- [ ] `sort`
- [ ] `uniq`
- [ ] `wc`
- [ ] `xargs`
- [ ] Command substitution: `$(...)`
- [ ] Exit codes
- [ ] Bash scripts
- [ ] `if`
- [ ] `for`
- [ ] `while`
- [ ] Functions
- [ ] Arguments: `$1`, `$2`, `$@`, `$#`
- [ ] `set -e`
- [ ] `set -u`
- [ ] `set -o pipefail`
- [ ] Cron-friendly scripts
- [ ] Idempotent scripts

---

## 7. [[Процессы]]

- [ ] Что такое процесс
- [ ] PID
- [ ] PPID
- [ ] `ps`
- [ ] `top`
- [ ] `htop`
- [ ] `pgrep`
- [ ] `pidof`
- [ ] `kill`
- [ ] `kill -9`
- [ ] `kill -15`
- [ ] Signals
- [ ] `SIGTERM`
- [ ] `SIGKILL`
- [ ] `SIGHUP`
- [ ] Zombie processes
- [ ] Orphan processes
- [ ] Foreground/background jobs
- [ ] `jobs`
- [ ] `fg`
- [ ] `bg`
- [ ] `nohup`
- [ ] `disown`
- [ ] `nice`
- [ ] `renice`
- [ ] Как найти процесс, который грузит CPU
- [ ] Как найти процесс, который ест память
- [ ] Как найти процесс, который держит файл
- [ ] Как найти процесс, который слушает порт

---

## 8. [[Systemd и сервисы]]

- [ ] Что такое init system
- [ ] Что такое systemd
- [ ] `systemctl`
- [ ] `systemctl status`
- [ ] `systemctl start`
- [ ] `systemctl stop`
- [ ] `systemctl restart`
- [ ] `systemctl reload`
- [ ] `systemctl enable`
- [ ] `systemctl disable`
- [ ] `systemctl daemon-reload`
- [ ] Unit files
- [ ] Service unit
- [ ] Timer unit
- [ ] Target
- [ ] `WantedBy`
- [ ] `ExecStart`
- [ ] `Restart`
- [ ] `RestartSec`
- [ ] `Environment`
- [ ] `EnvironmentFile`
- [ ] Как написать свой systemd service
- [ ] Почему сервис не стартует
- [ ] Как смотреть логи systemd-сервиса
- [ ] `journalctl`
- [ ] `journalctl -u service`
- [ ] `journalctl -f`
- [ ] `journalctl --since`
- [ ] Разница restart и reload

---

## 9. [[Логи]]

- [ ] Где лежат логи в Linux
- [ ] `/var/log`
- [ ] `journalctl`
- [ ] `dmesg`
- [ ] `syslog`
- [ ] `auth.log`
- [ ] `kern.log`
- [ ] `messages`
- [ ] Логи nginx
- [ ] Логи systemd-сервисов
- [ ] `tail -f`
- [ ] `grep`
- [ ] `less`
- [ ] `zgrep`
- [ ] Log rotation
- [ ] `logrotate`
- [ ] Как найти ошибку в логах
- [ ] Как читать логи по времени
- [ ] Как понять, почему сервис упал
- [ ] Как отличать application logs от system logs

---

## 10. [[Сеть в Linux]]

- [ ] IP address
- [ ] Subnet
- [ ] Gateway
- [ ] DNS
- [ ] Routing
- [ ] TCP
- [ ] UDP
- [ ] ICMP
- [ ] Ports
- [ ] Listening socket
- [ ] Established connection
- [ ] `ip addr`
- [ ] `ip route`
- [ ] `ss`
- [ ] `netstat`
- [ ] `ping`
- [ ] `traceroute`
- [ ] `mtr`
- [ ] `dig`
- [ ] `nslookup`
- [ ] `host`
- [ ] `curl`
- [ ] `wget`
- [ ] `telnet`
- [ ] `nc`
- [ ] `/etc/hosts`
- [ ] `/etc/resolv.conf`
- [ ] DNS troubleshooting
- [ ] Проверить, открыт ли порт
- [ ] Проверить, слушает ли сервис порт
- [ ] Проверить, доступен ли внешний адрес
- [ ] Проверить TLS-сертификат через `openssl s_client`
- [ ] MTU
- [ ] NAT
- [ ] Firewall basics
- [ ] `iptables`
- [ ] `nftables`
- [ ] `ufw`
- [ ] `firewalld`

---

## 11. [[Пакетные менеджеры]]

- [ ] `apt`
- [ ] `apt update`
- [ ] `apt upgrade`
- [ ] `apt install`
- [ ] `apt remove`
- [ ] `dpkg`
- [ ] Репозитории apt
- [ ] GPG keys
- [ ] `yum`
- [ ] `dnf`
- [ ] `rpm`
- [ ] Как поставить пакет
- [ ] Как удалить пакет
- [ ] Как найти, из какого пакета файл
- [ ] Как зафиксировать версию пакета
- [ ] Как обновлять сервер безопасно
- [ ] Security updates
- [ ] Unattended upgrades

---

## 12. [[Диски, разделы и файловые системы]]

- [ ] Block devices
- [ ] `/dev/sda`
- [ ] `/dev/nvme0n1`
- [ ] Partitions
- [ ] `lsblk`
- [ ] `blkid`
- [ ] `fdisk`
- [ ] `parted`
- [ ] Filesystems
- [ ] ext4
- [ ] xfs
- [ ] btrfs
- [ ] Mount
- [ ] Unmount
- [ ] `/etc/fstab`
- [ ] `mount`
- [ ] `umount`
- [ ] `df -h`
- [ ] `du -sh`
- [ ] Inodes
- [ ] `df -i`
- [ ] Что делать, если закончилось место
- [ ] Что делать, если закончились inodes
- [ ] LVM
- [ ] Physical Volume
- [ ] Volume Group
- [ ] Logical Volume
- [ ] Resize filesystem
- [ ] Swap
- [ ] `swapon`
- [ ] `swapoff`

---

## 13. [[Производительность Linux]]

- [ ] CPU usage
- [ ] Load average
- [ ] Memory usage
- [ ] Disk I/O
- [ ] Network I/O
- [ ] `top`
- [ ] `htop`
- [ ] `uptime`
- [ ] `free -m`
- [ ] `vmstat`
- [ ] `iostat`
- [ ] `iotop`
- [ ] `sar`
- [ ] `pidstat`
- [ ] `dstat`
- [ ] `mpstat`
- [ ] `lsof`
- [ ] `strace`
- [ ] Как понять, CPU bottleneck или I/O bottleneck
- [ ] Что такое load average
- [ ] Почему load average может быть высокий при низком CPU
- [ ] Page cache
- [ ] Buffers/cache
- [ ] OOM Killer
- [ ] Как найти, кто съел память
- [ ] Как найти, кто пишет на диск
- [ ] Как найти, кто открыл много файлов

---

## 14. [[Kernel basics]]

- [ ] Что такое Linux kernel
- [ ] Kernel modules
- [ ] `uname -a`
- [ ] `lsmod`
- [ ] `modprobe`
- [ ] `sysctl`
- [ ] `/proc`
- [ ] `/sys`
- [ ] Kernel parameters
- [ ] `dmesg`
- [ ] OOM Killer
- [ ] File descriptors
- [ ] Limits
- [ ] `ulimit`
- [ ] `/etc/security/limits.conf`
- [ ] `sysctl.conf`
- [ ] `net.ipv4.ip_forward`
- [ ] `vm.swappiness`
- [ ] `fs.file-max`

---

## 15.[[ Cron и scheduled jobs]]

- [ ] `cron`
- [ ] `crontab`
- [ ] `/etc/crontab`
- [ ] `/etc/cron.d`
- [ ] `/etc/cron.daily`
- [ ] Cron syntax
- [ ] Environment в cron
- [ ] Почему скрипт работает вручную, но не работает в cron
- [ ] Логи cron
- [ ] Systemd timers
- [ ] Разница cron и systemd timer
- [ ] Как сделать регулярный job
- [ ] Как дебажить cron job

---

## 16. [[Архивы и сжатие]]

- [ ] `tar`
- [ ] `gzip`
- [ ] `gunzip`
- [ ] `zip`
- [ ] `unzip`
- [ ] `xz`
- [ ] `bzip2`
- [ ] Как создать архив
- [ ] Как распаковать архив
- [ ] Как архивировать директорию
- [ ] Как смотреть содержимое архива
- [ ] Как передать архив по SSH

---

## 17. [[SSH и удалённый доступ]]

- [ ] Как работает SSH
- [ ] Public/private key
- [ ] `ssh-keygen`
- [ ] `ssh-copy-id`
- [ ] `authorized_keys`
- [ ] SSH agent
- [ ] `ssh-agent`
- [ ] `ssh-add`
- [ ] SSH config
- [ ] Jump host
- [ ] Bastion host
- [ ] Port forwarding
- [ ] Local forwarding
- [ ] Remote forwarding
- [ ] `scp`
- [ ] `rsync`
- [ ] Как безопасно настроить SSH
- [ ] Как дебажить SSH-подключение через `ssh -v`

---

## 18. [[Безопасность Linux]]

- [ ] Минимизация прав
- [ ] Принцип least privilege
- [ ] Обновления безопасности
- [ ] Firewall
- [ ] SSH hardening
- [ ] Fail2ban
- [ ] Audit logs
- [ ] `auditd`
- [ ] SELinux basics
- [ ] AppArmor basics
- [ ] Разница SELinux и AppArmor
- [ ] Как временно проверить проблему с SELinux
- [ ] Почему нельзя просто отключать SELinux на проде
- [ ] Secrets в Linux
- [ ] Права на приватные ключи
- [ ] Права на конфиги с паролями
- [ ] Проверка открытых портов
- [ ] Проверка лишних пользователей
- [ ] Проверка sudo-доступов

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