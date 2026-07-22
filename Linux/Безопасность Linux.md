Главная идея безопасности Linux:

> не давать процессам, пользователям и сервисам больше прав, чем им реально нужно.

Это называется **least privilege** — принцип минимальных привилегий.

---

# 1. Минимизация прав

**Минимизация прав** — это когда:

- обычные пользователи не работают под `root`;
- сервисы запускаются от отдельных системных пользователей;
- файлы с секретами доступны только нужному пользователю;
- `sudo` даётся только на конкретные команды;
- открыты только нужные сетевые порты;
- ненужные сервисы отключены.

Плохой пример:

```
chmod 777 /etc/app/config.yml
```

Это значит: читать, писать и выполнять может кто угодно.

Хороший пример:

```
chown app:app /etc/app/config.ymlchmod 600 /etc/app/config.yml
```

Только пользователь `app` может читать и писать файл.

---

# 2. Principle of least privilege

**Least privilege** — пользователь или процесс должен иметь только те права, которые нужны для работы.

Например, если nginx должен читать файлы сайта, ему не нужно право менять `/etc/shadow`.

Плохо:

```
app ALL=(ALL) NOPASSWD:ALL
```

Это даёт пользователю `app` полный root без пароля.

Лучше:

```
app ALL=(root) NOPASSWD: /bin/systemctl restart myapp
```

Это разрешает только перезапуск конкретного сервиса.

Проверка sudo-доступов:

```
sudo -l
```

Посмотреть sudoers:

```
sudo visudo
```

Файлы:

```
/etc/sudoers/etc/sudoers.d/
```

---

# 3. Обновления безопасности

Старые пакеты могут содержать уязвимости.

Для Debian/Ubuntu:

```
sudo apt update
sudo apt upgrade
```

Только security updates можно смотреть так:

```
apt list --upgradable
```

На Ubuntu можно включить unattended upgrades:

```
sudo apt install unattended-upgrades
sudo dpkg-reconfigure unattended-upgrades
```

Для RHEL/CentOS/Fedora:

```
sudo dnf update
```

или старый вариант:

```
sudo yum update
```

Важно на проде:

- не обновлять всё вслепую;
- читать changelog;
- иметь backup/snapshot;
- тестировать на staging;
- обновлять security patches регулярно.

---

# 4. Firewall

Firewall ограничивает сетевой доступ.

Популярные варианты:

- `iptables` — старый, но всё ещё часто встречается;
- `nftables` — современная замена iptables;
- `ufw` — простой frontend для iptables/nftables, часто в Ubuntu;
- `firewalld` — часто в RHEL/CentOS/Fedora.

Проверить открытые порты:

```
ss -tulpen
```

Пример вывода:

```
LISTEN 0 128 0.0.0.0:22LISTEN 0 128 0.0.0.0:80LISTEN 0 128 127.0.0.1:5432
```

Значит:

- `0.0.0.0:22` — SSH слушает на всех интерфейсах;
- `0.0.0.0:80` — HTTP доступен извне;
- `127.0.0.1:5432` — PostgreSQL доступен только локально.

Пример с `ufw`:

```
sudo ufw statussudo ufw allow 22/tcpsudo ufw allow 80/tcpsudo ufw enable
```

Закрыть порт:

```
sudo ufw deny 3306/tcp
```

Смысл firewall:

> если сервис не должен быть доступен извне, он не должен торчать наружу.

---

# 5. SSH hardening

**SSH hardening** — безопасная настройка SSH.

Главный файл:

```
/etc/ssh/sshd_config
```

Проверить итоговую конфигурацию:

```
sshd -T
```

Полезные настройки:

```
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
PermitEmptyPasswords no
X11Forwarding no
MaxAuthTries 3
AllowUsers deploy admin
```

После изменения:

```
sudo systemctl reload ssh
```

или:

```
sudo systemctl restart sshd
```

В зависимости от дистрибутива сервис может называться `ssh` или `sshd`.

Проверить статус:

```
systemctl status sshsystemctl status sshd
```

Важно:

```
PermitRootLogin no
```

означает, что под `root` нельзя логиниться по SSH напрямую.

Лучше заходить обычным пользователем и потом делать:

```
sudo command
```

---

# 6. Fail2ban

**Fail2ban** — сервис, который банит IP-адреса за подозрительные попытки входа.

Например, если кто-то много раз неправильно вводит пароль по SSH, fail2ban может временно заблокировать этот IP.

Установка:

```
sudo apt install fail2ban
```

Проверка:

```
sudo systemctl status fail2ban
```

Конфиги:

```
/etc/fail2ban/jail.conf
/etc/fail2ban/jail.local
/etc/fail2ban/jail.d/
```

Лучше не править `jail.conf`, а создать `jail.local`.

Пример:

```
[sshd]enabled = trueport = sshmaxretry = 5bantime = 1hfindtime = 10m
```

Проверить статус jail:

```
sudo fail2ban-client status
sudo fail2ban-client status sshd
```

Разбанить IP:

```
sudo fail2ban-client set sshd unbanip 1.2.3.4
```

---

# 7. Audit logs

**Audit logs** — логи безопасности.

Обычно полезно смотреть:

```
/var/log/auth.log
```

на Debian/Ubuntu.

На RHEL/CentOS:

```
/var/log/secure
```

Также через systemd:

```
journalctljournalctl -u sshjournalctl -u sshd
```

Посмотреть попытки входа:

```
grep "Failed password" /var/log/auth.log
```

Посмотреть sudo-команды:

```
grep sudo /var/log/auth.log
```

Посмотреть успешные SSH-входы:

```
grep "Accepted" /var/log/auth.log
```

---

# 8. `auditd`

`auditd` — демон аудита Linux. Он позволяет отслеживать важные действия в системе:

- кто изменил файл;
- кто запускал команду;
- кто менял права;
- кто трогал `/etc/passwd`;
- кто использовал sudo;
- кто открывал секретные файлы.

Установка:

```
sudo apt install auditd audispd-plugins
```

Статус:

```
sudo systemctl status auditd
```

Основные команды:

```
auditctl
ausearch
aureport
```

Пример: следить за изменениями `/etc/passwd`:

```
sudo auditctl -w /etc/passwd -p wa -k passwd_changes
```

Где:

- `-w /etc/passwd` — watch file;
- `-p wa` — следить за write и attribute changes;
- `-k passwd_changes` — ключ для поиска.

Найти события:

```
sudo ausearch -k passwd_changes
```

Отчёт:

```
sudo aureport
```

Постоянные правила обычно кладут сюда:

```
/etc/audit/rules.d/audit.rules
```

---

# 9. SELinux basics

**SELinux** — Mandatory Access Control система.

Обычные Linux-права отвечают на вопрос:

> может ли пользователь читать/писать/выполнять файл?

SELinux добавляет второй уровень:

> разрешает ли политика безопасности этому процессу делать это действие?

Даже если обычные права позволяют, SELinux может запретить.

Проверить статус:

```
getenforce
```

Возможные значения:

```
Enforcing
Permissive
Disabled
```

- `Enforcing` — SELinux реально блокирует запрещённые действия;
- `Permissive` — не блокирует, только пишет предупреждения в логи;
- `Disabled` — выключен полностью.

Подробно:

```
sestatus
```

Контексты SELinux:

```
ls -Zps -Z
```

Пример:

```
-rw-r--r--. root root system_u:object_r:httpd_sys_content_t:s0 index.html
```

SELinux смотрит не только на владельца и права, но и на **security context**.

---

# 10. AppArmor basics

**AppArmor** — тоже Mandatory Access Control, но проще по модели.

Он ограничивает программы через профили.

Часто используется в Ubuntu/Debian.

Проверить статус:

```
sudo aa-status
```

Профили лежат здесь:

```
/etc/apparmor.d/
```

Режимы:

- `enforce` — блокирует запрещённое;
- `complain` — только логирует;
- `disabled` — профиль выключен.

Перевести профиль в complain mode:

```
sudo aa-complain /etc/apparmor.d/usr.sbin.nginx
```

Вернуть в enforce:

```
sudo aa-enforce /etc/apparmor.d/usr.sbin.nginx
```

---

# 11. Разница SELinux и AppArmor

Упрощённо:

|SELinux|AppArmor|
|---|---|
|Чаще в RHEL/CentOS/Fedora|Чаще в Ubuntu/Debian|
|Основан на security context / labels|Основан на путях файлов|
|Более мощный и сложный|Проще в понимании|
|Политики детальные|Профили проще читать|
|Сложнее дебажить|Обычно проще дебажить|

Примерно:

**SELinux** думает так:

> процесс с типом `httpd_t` может читать файлы типа `httpd_sys_content_t`.

**AppArmor** думает так:

> `/usr/sbin/nginx` может читать `/var/www/**`.

---

# 12. Как временно проверить проблему с SELinux

Если приложение не работает и есть подозрение на SELinux, не надо сразу отключать его навсегда.

Проверить текущий режим:

```
getenforce
```

Временно перевести в permissive:

```
sudo setenforce 0
```

Вернуть обратно:

```
sudo setenforce 1
```

Важно:

```
setenforce 0
```

не отключает SELinux полностью. Он просто перестаёт блокировать, но продолжает логировать.

Смотреть AVC-denied ошибки:

```
sudo ausearch -m avc -ts recent
```

или:

```
sudo grep denied /var/log/audit/audit.log
```

Ещё полезно:

```
sudo sealert -a /var/log/audit/audit.log
```

если установлен `setroubleshoot`.

---

# 13. Почему нельзя просто отключать SELinux на проде

Потому что SELinux — это дополнительный слой защиты.

Если злоумышленник взломал nginx, обычные Linux-права могут быть недостаточны. SELinux может не дать процессу читать чужие файлы, ходить куда не надо или выполнять опасные действия.

Плохой подход:

```
SELINUX=disabled
```

в файле:

```
/etc/selinux/config
```

Лучше:

- понять, что именно блокируется;
- посмотреть audit logs;
- исправить context;
- настроить boolean;
- добавить корректное правило политики.

Пример восстановления контекста:

```
sudo restorecon -Rv /var/www/html
```

Посмотреть SELinux booleans:

```
getsebool -a
```

Изменить boolean:

```
sudo setsebool -P httpd_can_network_connect on
```

---

# 14. Secrets в Linux

**Secrets** — это чувствительные данные:

- пароли;
- API keys;
- private keys;
- database credentials;
- tokens;
- `.env` файлы;
- TLS private keys;
- SSH private keys.

Плохие места для секретов:

```
git repository
docker image
bash history
world-readable config
command line arguments
```

Плохой пример:

```
mysql -u root -pMySecretPassword
```

Пароль может попасть в history или быть виден через `ps`.

Лучше:

```
mysql -u root -p
```

или использовать защищённый config-файл с правильными правами.

Проверить bash history:

```
history
```

Очистить конкретно текущую историю:

```
history -c
```

Но если секрет уже попал в git, просто удалить строку недостаточно — он останется в истории коммитов.

---

# 15. Права на приватные ключи

SSH private key должен быть закрыт от других пользователей.

Обычно:

```
chmod 600 ~/.ssh/id_rsachmod 600 ~/.ssh/id_ed25519
```

Директория `.ssh`:

```
chmod 700 ~/.ssh
```

Публичный ключ:

```
chmod 644 ~/.ssh/id_ed25519.pub
```

`authorized_keys`:

```
chmod 600 ~/.ssh/authorized_keys
```

Правильный владелец:

```
chown -R user:user ~/.ssh
```

Если права слишком открытые, SSH может отказаться использовать ключ:

```
Permissions are too open
```

---

# 16. Права на конфиги с паролями

Например, есть файл:

```
/etc/myapp/config.yml
```

Внутри:

```
database_password: secretapi_token: token
```

Нельзя делать так:

```
chmod 644 /etc/myapp/config.yml
```

Потому что читать смогут все пользователи.

Лучше:

```
chown myapp:myapp /etc/myapp/config.ymlchmod 600 /etc/myapp/config.yml
```

Если конфиг должен читать root и группа приложения:

```
chown root:myapp /etc/myapp/config.ymlchmod 640 /etc/myapp/config.yml
```

Расшифровка:

```
-rw-r-----
```

- owner может читать и писать;
- group может читать;
- others не могут ничего.

---

# 17. Проверка открытых портов

Основная команда:

```
ss -tulpen
```

Где:

- `-t` — TCP;
- `-u` — UDP;
- `-l` — listening;
- `-p` — process;
- `-e` — extended info;
- `-n` — numeric, не резолвить имена.

Примеры:

```
sudo ss -tulpen
```

Проверить только TCP:

```
sudo ss -tlpn
```

Проверить конкретный порт:

```
sudo ss -tlpn | grep ':22'
```

Снаружи можно проверить через `nmap`:

```
nmap -sV server_ip
```

Но на сервере чаще всего достаточно:

```
ss -tulpen
```

Важно различать:

```
127.0.0.1:5432
```

и:

```
0.0.0.0:5432
```

`127.0.0.1` — доступно только локально.

`0.0.0.0` — слушает на всех интерфейсах, потенциально доступно извне.

---

# 18. Проверка лишних пользователей

Список пользователей:

```
cat /etc/passwd
```

Но там есть и системные пользователи.

Пользователи с обычными UID часто начинаются от `1000`:

```
awk -F: '$3 >= 1000 {print}' /etc/passwd
```

Посмотреть пользователей с shell:

```
grep -E '/bin/bash|/bin/sh|/bin/zsh' /etc/passwd
```

Проверить, кто может логиниться:

```
cat /etc/passwd | grep -v nologin
```

или:

```
grep -vE 'nologin|false' /etc/passwd
```

Посмотреть последние входы:

```
last
```

Кто сейчас в системе:

```
whow
```

Проверить SSH-ключи пользователей:

```
find /home -name authorized_keys -type f -ls
```

Посмотреть содержимое:

```
sudo cat /home/user/.ssh/authorized_keys
```

---

# 19. Проверка sudo-доступов

Кто в группе `sudo`:

```
getent group sudo
```

На RHEL/CentOS часто группа называется `wheel`:

```
getent group wheel
```

Проверить sudoers:

```
sudo visudo
```

Проверить дополнительные правила:

```
ls -la /etc/sudoers.d/
```

Посмотреть их:

```
sudo cat /etc/sudoers.d/*
```

Проверить права конкретного пользователя:

```
sudo -l -U username
```

Проверить свои права:

```
sudo -l
```

Плохие признаки:

```
NOPASSWD:ALLALL=(ALL) ALLchmod 777
```

Особенно опасно:

```
user ALL=(ALL) NOPASSWD:ALL
```

Это почти полный root без пароля.

---

# 20. Быстрый security checklist для сервера

Минимальный чеклист:

```
# кто сейчас в системе
whow
# последние логины
last
# пользователи с shell
grep -vE 'nologin|false' /etc/passwd
# sudo-доступы
getent group sudo
getent group wheel
sudo -l
# открытые порты
sudo ss -tulpen
# firewall
sudo ufw status
sudo firewall-cmd --list-all
# ssh config
sudo sshd -T
# логи авторизации
sudo grep "Failed password" /var/log/auth.log
sudo grep "Accepted" /var/log/auth.log
# SELinux
geten
forcesestatus
# AppArmor
sudo aa-status
# auditd
sudo systemctl status auditd
```

---

# 21. Типовые вопросы на собеседовании

## Что такое least privilege?

Это принцип, при котором пользователь или процесс получает только минимально необходимые права.

---

## Почему нельзя всем давать sudo?

Потому что `sudo` позволяет выполнять команды от root. Ошибка или компрометация такого пользователя может привести к полному захвату сервера.

---

## Как проверить открытые порты?

```
sudo ss -tulpen
```

---

## Как запретить root login по SSH?

В `/etc/ssh/sshd_config`:

```
PermitRootLogin no
```

Потом:

```
sudo systemctl reload sshd
```

---

## Почему нельзя отключать SELinux?

Потому что SELinux ограничивает процессы даже после их компрометации. Отключение SELinux убирает важный слой защиты.

---

## Как временно проверить, мешает ли SELinux?

```
getenforcesudo setenforce 0
```

Потом обязательно вернуть:

```
sudo setenforce 1
```

---

## Какие права должны быть у SSH private key?

```
chmod 600 ~/.ssh/id_ed25519chmod 700 ~/.ssh
```

---

## Где смотреть попытки входа по SSH?

Ubuntu/Debian:

```
/var/log/auth.log
```

RHEL/CentOS:

```
/var/log/secure
```

Через journalctl:

```
journalctl -u sshjournalctl -u sshd
```

---

# Коротко

Linux security — это не одна команда, а набор практик:

- минимум прав;
- регулярные обновления;
- закрытые лишние порты;
- безопасный SSH;
- контроль sudo;
- правильные права на секреты;
- аудит логов;
- SELinux/AppArmor как дополнительная защита;
- отсутствие лишних пользователей и сервисов.