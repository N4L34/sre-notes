
Scheduled jobs — это задачи, которые запускаются автоматически по расписанию.

Примеры:

- каждый день делать backup;

- раз в минуту проверять состояние сервиса;

- раз в час чистить временные файлы;

- каждую ночь запускать отчёт;

- раз в неделю обновлять сертификаты;

- регулярно отправлять метрики или healthcheck.


В Linux для этого чаще всего используют:

- `cron`;

- `systemd timers`;

- Kubernetes `CronJob`, если речь про Kubernetes.


---

# `cron`

`cron` — это классический Linux-демон для запуска команд по расписанию.

Демон обычно называется:

```bash
crond
```

или:

```bash
cron
```

Проверить статус:

```bash
systemctl status cron
```

На RHEL/CentOS/Fedora:

```bash
systemctl status crond
```

`cron` постоянно работает в фоне и каждую минуту проверяет, есть ли задачи, которые нужно запустить.

---

# `crontab`

`crontab` — это таблица cron-задач пользователя.

Посмотреть свои cron-задачи:

```bash
crontab -l
```

Открыть crontab на редактирование:

```bash
crontab -e
```

Удалить весь crontab текущего пользователя:

```bash
crontab -r
```

Посмотреть crontab другого пользователя:

```bash
sudo crontab -u username -l
```

Редактировать crontab другого пользователя:

```bash
sudo crontab -u username -e
```

Важно:

```bash
crontab -e
```

редактирует crontab **текущего пользователя**.

А вот:

```bash
sudo crontab -e
```

редактирует crontab **root-пользователя**.

Это частая ошибка.

---

# Cron syntax

Стандартная cron-строка выглядит так:

```bash
* * * * * command
```

Пять звёздочек означают:

```text
минута час день_месяца месяц день_недели
```

Формат:

```text
┌───────────── минута: 0-59
│ ┌─────────── час: 0-23
│ │ ┌───────── день месяца: 1-31
│ │ │ ┌─────── месяц: 1-12
│ │ │ │ ┌───── день недели: 0-7, где 0 и 7 — воскресенье
│ │ │ │ │
* * * * * command
```

Пример:

```bash
*/5 * * * * /opt/scripts/check.sh
```

Означает:

```text
каждые 5 минут запускать /opt/scripts/check.sh
```

---

# Примеры расписаний

Каждую минуту:

```bash
* * * * * /path/to/script.sh
```

Каждые 5 минут:

```bash
*/5 * * * * /path/to/script.sh
```

Каждый час:

```bash
0 * * * * /path/to/script.sh
```

Каждый день в 03:00:

```bash
0 3 * * * /path/to/script.sh
```

Каждое воскресенье в 04:30:

```bash
30 4 * * 0 /path/to/script.sh
```

Каждого первого числа месяца в 00:00:

```bash
0 0 1 * * /path/to/script.sh
```

По будням в 09:00:

```bash
0 9 * * 1-5 /path/to/script.sh
```

Каждые 15 минут:

```bash
*/15 * * * * /path/to/script.sh
```

---

# Специальные cron-выражения

Вместо пяти полей можно использовать специальные алиасы:

```bash
@reboot /path/to/script.sh
```

Запустить при старте системы.

```bash
@hourly /path/to/script.sh
```

Раз в час.

```bash
@daily /path/to/script.sh
```

Раз в день.

```bash
@weekly /path/to/script.sh
```

Раз в неделю.

```bash
@monthly /path/to/script.sh
```

Раз в месяц.

```bash
@yearly /path/to/script.sh
```

Раз в год.

---

# `/etc/crontab`

`/etc/crontab` — системный crontab.

Открыть:

```bash
cat /etc/crontab
```

В отличие от пользовательского `crontab -e`, в `/etc/crontab` есть дополнительное поле — **пользователь**, от которого запускать команду.

Пример:

```bash
* * * * * root /opt/scripts/check.sh
```

Формат:

```text
минута час день месяц день_недели пользователь команда
```

То есть здесь обязательно указывается user:

```bash
root
```

или, например:

```bash
deploy
```

Пример:

```bash
0 3 * * * backup /opt/backup/run.sh
```

Это значит:

```text
каждый день в 03:00 запускать /opt/backup/run.sh от пользователя backup
```

---

# `/etc/cron.d`

`/etc/cron.d` — директория для отдельных cron-файлов.

Например:

```bash
/etc/cron.d/myapp
```

Содержимое:

```bash
*/5 * * * * root /opt/myapp/check.sh
```

Формат такой же, как в `/etc/crontab`:

```text
минута час день месяц день_недели пользователь команда
```

То есть в `/etc/cron.d` тоже нужно указывать пользователя.

Это удобно для пакетов и приложений: каждое приложение может положить свой cron-файл отдельно.

Права обычно должны быть строгими:

```bash
sudo chmod 644 /etc/cron.d/myapp
sudo chown root:root /etc/cron.d/myapp
```

---

# `/etc/cron.daily`

`/etc/cron.daily` — директория для скриптов, которые запускаются раз в день.

Также есть:
``
```bash
/etc/cron.hourly
/etc/cron.daily
/etc/cron.weekly
/etc/cron.monthly
```

Пример:

```bash
ls -l /etc/cron.daily
```

Туда можно положить скрипт:

```bash
sudo cp cleanup.sh /etc/cron.daily/cleanup
```

Сделать исполняемым:

```bash
sudo chmod +x /etc/cron.daily/cleanup
```

Важно: скрипты в этих директориях обычно запускаются через `run-parts`.

Поэтому имя файла должно быть нормальным. Лучше без точек:

Плохо:

```text
cleanup.sh
```

Лучше:

```text
cleanup
```

Потому что в некоторых системах `run-parts` может игнорировать файлы с точкой в имени.

---

# Environment в cron

Одна из самых частых проблем: скрипт работает вручную, но не работает в cron.

Причина — в cron другое окружение.

Когда ты запускаешь команду руками, у тебя есть:

```bash
PATH
HOME
USER
SHELL
SSH_AUTH_SOCK
LANG
PWD
```

и другие переменные окружения.

А в cron окружение минимальное.

Посмотреть свой `PATH`:

```bash
echo $PATH
```

В cron он может быть сильно короче, например:

```text
/usr/bin:/bin
```

Поэтому команда может не находиться.

Например, вручную работает:

```bash
docker ps
```

А в cron нет, потому что `docker` лежит в:

```bash
/usr/bin/docker
```

Лучше в cron использовать абсолютные пути:

```bash
* * * * * /usr/bin/docker ps
```

То же самое со скриптами:

```bash
* * * * * /bin/bash /opt/scripts/job.sh
```

А не просто:

```bash
* * * * * job.sh
```

---

# Как задать environment в cron

Можно прямо в crontab указать переменные:

```bash
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
MAILTO=admin@example.com

*/5 * * * * /opt/scripts/check.sh
```

Можно внутри скрипта явно указать переменные:

```bash
#!/bin/bash

export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

/usr/bin/docker ps
```

Можно загрузить `.env` файл:

```bash
#!/bin/bash

source /opt/myapp/.env

/opt/myapp/run-job.sh
```

Но важно: если используется `source`, скрипт лучше запускать через bash:

```bash
* * * * * /bin/bash /opt/myapp/job.sh
```

---

# Почему скрипт работает вручную, но не работает в cron

Основные причины:

## 1. Не тот `PATH`

Вручную команда находится, а в cron нет.

Плохо:

```bash
* * * * * docker ps
```

Лучше:

```bash
* * * * * /usr/bin/docker ps
```

Проверить путь:

```bash
which docker
```

или:

```bash
command -v docker
```

---

## 2. Не та рабочая директория

Вручную ты запускаешь скрипт из нужной директории:

```bash
cd /opt/myapp
./backup.sh
```

А cron запускает его из другой директории, часто из `$HOME`.

Плохо:

```bash
* * * * * ./backup.sh
```

Лучше:

```bash
* * * * * cd /opt/myapp && /bin/bash backup.sh
```

Или внутри скрипта:

```bash
cd /opt/myapp || exit 1
```

---

## 3. Нет прав на выполнение

Проверить:

```bash
ls -l /opt/scripts/job.sh
```

Должно быть что-то вроде:

```bash
-rwxr-xr-x
```

Добавить execute bit:

```bash
chmod +x /opt/scripts/job.sh
```

---

## 4. Неправильный shebang

В начале скрипта должно быть:

```bash
#!/bin/bash
```

или:

```bash
#!/usr/bin/env bash
```

Если shebang неправильный, cron может не понять, чем запускать скрипт.

---

## 5. Скрипт требует интерактивного ввода

Cron не умеет нормально работать с интерактивными командами.

Плохо:

```bash
read name
```

или команда, которая спрашивает подтверждение:

```bash
rm -i file
```

Cron-задачи должны быть неинтерактивными.

---

## 6. Не хватает переменных окружения

Например, приложение ждёт:

```bash
DATABASE_URL
API_TOKEN
ENVIRONMENT
```

А в cron этих переменных нет.

Решение:

```bash
source /opt/myapp/.env
```

или прописать переменные прямо в crontab.

---

## 7. Команда пишет в stdout/stderr, но вывод никто не смотрит

Лучше всегда писать лог:

```bash
* * * * * /opt/scripts/job.sh >> /var/log/job.log 2>&1
```

Разбор:

```bash
>>
```

добавляет stdout в файл.

```bash
2>&1
```

перенаправляет stderr туда же, куда stdout.

---

# Логи cron

На Debian/Ubuntu cron-логи часто находятся в:

```bash
/var/log/syslog
```

Посмотреть cron-записи:

```bash
grep CRON /var/log/syslog
```

Следить в реальном времени:

```bash
tail -f /var/log/syslog | grep CRON
```

Через `journalctl`:

```bash
journalctl -u cron
```

или:

```bash
journalctl -u cron -f
```

На CentOS/RHEL cron-логи часто находятся в:

```bash
/var/log/cron
```

Посмотреть:

```bash
tail -f /var/log/cron
```

Для `crond` через systemd:

```bash
journalctl -u crond
```

---

# Как логировать сам cron job

Лучше не надеяться только на системные cron-логи.

Пример:

```bash
* * * * * /opt/scripts/job.sh >> /var/log/job.log 2>&1
```

Ещё лучше добавить дату:

```bash
* * * * * echo "$(date) start job" >> /var/log/job.log; /opt/scripts/job.sh >> /var/log/job.log 2>&1
```

Но удобнее делать это внутри скрипта:

```bash
#!/bin/bash

LOG_FILE="/var/log/my-job.log"

echo "$(date '+%Y-%m-%d %H:%M:%S') job started" >> "$LOG_FILE"

/opt/myapp/run.sh >> "$LOG_FILE" 2>&1

echo "$(date '+%Y-%m-%d %H:%M:%S') job finished with exit code $?" >> "$LOG_FILE"
```

---

# Как сделать регулярный job

Пример задачи: запускать backup каждый день в 02:00.

Создаём скрипт:

```bash
sudo mkdir -p /opt/scripts
sudo nano /opt/scripts/backup.sh
```

Содержимое:

```bash
#!/bin/bash

set -euo pipefail

BACKUP_DIR="/backup"
SOURCE_DIR="/var/www/html"
DATE="$(date +%F-%H-%M-%S)"

mkdir -p "$BACKUP_DIR"

tar -czf "$BACKUP_DIR/www-$DATE.tar.gz" "$SOURCE_DIR"
```

Делаем исполняемым:

```bash
sudo chmod +x /opt/scripts/backup.sh
```

Проверяем вручную:

```bash
sudo /opt/scripts/backup.sh
```

Добавляем в cron root-пользователя:

```bash
sudo crontab -e
```

Добавляем строку:

```bash
0 2 * * * /opt/scripts/backup.sh >> /var/log/backup.log 2>&1
```

Проверяем:

```bash
sudo crontab -l
```

---

# Как дебажить cron job

Алгоритм:

## 1. Проверить, что cron работает

```bash
systemctl status cron
```

или:

```bash
systemctl status crond
```

---

## 2. Проверить, есть ли задача в crontab

```bash
crontab -l
```

Для root:

```bash
sudo crontab -l
```

Для конкретного пользователя:

```bash
sudo crontab -u username -l
```

---

## 3. Проверить синтаксис cron

Например:

```bash
* * * * * /opt/scripts/job.sh
```

Это каждую минуту.

Для теста можно временно поставить запуск каждую минуту.

---

## 4. Проверить логи cron

Ubuntu/Debian:

```bash
grep CRON /var/log/syslog
```

RHEL/CentOS:

```bash
tail -f /var/log/cron
```

Через systemd:

```bash
journalctl -u cron
```

или:

```bash
journalctl -u crond
```

---

## 5. Добавить логирование stdout/stderr

```bash
* * * * * /opt/scripts/job.sh >> /tmp/job.log 2>&1
```

Потом смотреть:

```bash
cat /tmp/job.log
```

или:

```bash
tail -f /tmp/job.log
```

---

## 6. Проверить права

```bash
ls -l /opt/scripts/job.sh
```

Если нет `x`, добавить:

```bash
chmod +x /opt/scripts/job.sh
```

---

## 7. Проверить запуск от нужного пользователя

Например, задача запускается от `deploy`.

Проверить вручную именно от этого пользователя:

```bash
sudo -u deploy /opt/scripts/job.sh
```

Если вручную от своего пользователя работает, а от `deploy` нет — проблема в правах или окружении.

---

## 8. Проверить абсолютные пути

Вместо:

```bash
python app.py
```

лучше:

```bash
/usr/bin/python3 /opt/myapp/app.py
```

Путь к команде:

```bash
which python3
```

или:

```bash
command -v python3
```

---

## 9. Проверить рабочую директорию

В cron лучше делать так:

```bash
* * * * * cd /opt/myapp && /usr/bin/python3 app.py >> /var/log/myapp-job.log 2>&1
```

---

## 10. Проверить exit code

В скрипте можно добавить:

```bash
echo "exit code: $?" >> /tmp/job.log
```

Или сделать безопасный bash-скрипт:

```bash
#!/bin/bash

set -euo pipefail

cd /opt/myapp

/usr/bin/python3 app.py
```

---

# Systemd timers

`systemd timers` — это современная альтернатива cron.

Они состоят из двух unit-файлов:

1. `.service` — что запускать;

2. `.timer` — когда запускать.


Например:

```text
backup.service
backup.timer
```

---

# Пример systemd timer

Создаём service:

```bash
sudo nano /etc/systemd/system/backup.service
```

Содержимое:

```ini
[Unit]
Description=Run backup job

[Service]
Type=oneshot
ExecStart=/opt/scripts/backup.sh
```

Создаём timer:

```bash
sudo nano /etc/systemd/system/backup.timer
```

Содержимое:

```ini
[Unit]
Description=Run backup job every day

[Timer]
OnCalendar=*-*-* 02:00:00
Persistent=true

[Install]
WantedBy=timers.target
```

Активируем:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now backup.timer
```

Посмотреть таймеры:

```bash
systemctl list-timers
```

Посмотреть конкретный timer:

```bash
systemctl status backup.timer
```

Посмотреть логи service:

```bash
journalctl -u backup.service
```

---

# `OnCalendar`

`OnCalendar` — это расписание для systemd timer.

Каждый день в 02:00:

```ini
OnCalendar=*-*-* 02:00:00
```

Каждый час:

```ini
OnCalendar=hourly
```

Каждый день:

```ini
OnCalendar=daily
```

Каждую неделю:

```ini
OnCalendar=weekly
```

Каждый понедельник в 03:00:

```ini
OnCalendar=Mon *-*-* 03:00:00
```

Каждые 10 минут:

```ini
OnCalendar=*:0/10
```

---

# `Persistent=true`

Важная опция:

```ini
Persistent=true
```

Она означает: если сервер был выключен в момент, когда job должен был выполниться, systemd запустит его после следующей загрузки.

Пример:

Backup должен был запуститься в 02:00, но сервер был выключен.

Если стоит:

```ini
Persistent=true
```

то после включения сервера backup будет выполнен.

В cron такого поведения по умолчанию нет.

---

# Разница cron и systemd timer

## Cron

Плюсы:

- простой;

- есть почти везде;

- легко написать одну строку;

- хорошо подходит для простых задач.


Минусы:

- слабое логирование;

- ограниченное окружение;

- сложнее дебажить;

- нет нормальной зависимости от других systemd-сервисов;

- нет встроенного контроля missed runs;

- неудобно управлять как сервисом.


---

## Systemd timer

Плюсы:

- нормальные логи через `journalctl`;

- можно использовать зависимости systemd;

- можно задать `Persistent=true`;

- можно смотреть таймеры через `systemctl list-timers`;

- можно запускать вручную `.service`;

- лучше подходит для production-серверов;

- можно настроить sandbox/security options.


Минусы:

- нужно создавать два unit-файла;

- синтаксис сложнее, чем у cron;

- работает только на системах с systemd.


---

# Когда использовать cron, а когда systemd timer

Cron хорошо подходит для простых задач:

```text
раз в день удалить старые файлы
раз в 5 минут вызвать скрипт
раз в час отправить отчёт
```

Systemd timer лучше использовать, если:

```text
нужны нормальные логи
нужен production-подход
нужны зависимости от сервисов
нужен запуск пропущенной задачи после reboot
нужно управлять job как systemd unit
нужна безопасность через systemd options
```

---

# Частые вопросы на собеседовании

## Что такое cron?

`cron` — это демон Linux, который запускает команды по расписанию.

---

## Чем отличается `crontab -e` от `/etc/crontab`?

`crontab -e` редактирует cron-задачи конкретного пользователя.

`/etc/crontab` — системный crontab, где дополнительно указывается пользователь, от которого запускать команду.

Пример `/etc/crontab`:

```bash
* * * * * root /opt/job.sh
```

---

## Почему cron job не работает, хотя вручную скрипт работает?

Чаще всего из-за:

- другого `PATH`;

- другой рабочей директории;

- отсутствующих environment variables;

- прав доступа;

- неправильного пользователя;

- отсутствия execute bit;

- отсутствия логирования;

- неправильного shebang.


---

## Как посмотреть логи cron?

Ubuntu/Debian:

```bash
grep CRON /var/log/syslog
```

RHEL/CentOS:

```bash
tail -f /var/log/cron
```

Через systemd:

```bash
journalctl -u cron
```

или:

```bash
journalctl -u crond
```

---

## Как проверить, что job реально запускается?

Временно поставить запуск каждую минуту:

```bash
* * * * * echo "cron works $(date)" >> /tmp/cron-test.log 2>&1
```

Потом проверить:

```bash
cat /tmp/cron-test.log
```

---

## Как правильно писать cron job?

Хороший пример:

```bash
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

0 2 * * * cd /opt/myapp && /bin/bash backup.sh >> /var/log/backup.log 2>&1
```

Почему хорошо:

- задан `SHELL`;

- задан `PATH`;

- используется абсолютная директория;

- есть логирование;

- stderr тоже попадает в лог.


---

# Мини-шпаргалка

Посмотреть cron текущего пользователя:

```bash
crontab -l
```

Редактировать cron текущего пользователя:

```bash
crontab -e
```

Редактировать cron root:

```bash
sudo crontab -e
```

Посмотреть cron root:

```bash
sudo crontab -l
```

Системный crontab:

```bash
cat /etc/crontab
```

Cron-файлы приложений:

```bash
ls -l /etc/cron.d
```

Ежедневные задачи:

```bash
ls -l /etc/cron.daily
```

Проверить cron service:

```bash
systemctl status cron
```

или:

```bash
systemctl status crond
```

Логи cron на Ubuntu/Debian:

```bash
grep CRON /var/log/syslog
```

Логи cron на RHEL/CentOS:

```bash
tail -f /var/log/cron
```

Список systemd timers:

```bash
systemctl list-timers
```

Логи systemd timer service:

```bash
journalctl -u backup.service
```

---

# Главное, что нужно запомнить

`cron` — это простой способ запускать команды по расписанию.

`crontab -e` — cron-задачи пользователя.

`/etc/crontab` и `/etc/cron.d` требуют указания пользователя.

Cron имеет минимальное окружение, поэтому лучше использовать абсолютные пути и явно задавать `PATH`.

Если cron job не работает, первое, что нужно сделать:

```bash
добавить логирование stdout/stderr
```

Пример:

```bash
* * * * * /opt/scripts/job.sh >> /tmp/job.log 2>&1
```

`systemd timers` — более современная и удобная альтернатива cron для production-задач.