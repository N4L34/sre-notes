## Что такое init system

**Init system** — это первый пользовательский процесс в Linux.

У него обычно PID `1`.

Проверить:

```
ps -p 1 -o pid,comm,args
```

Пример:

```
PID COMMAND         COMMAND1   systemd         /sbin/init
```

Init system отвечает за:

- запуск сервисов при загрузке;
- остановку сервисов при shutdown/reboot;
- перезапуск упавших сервисов;
- управление зависимостями;
- сбор логов;
- управление состояниями системы.

---

## Что такое systemd

**systemd** — это современная init system в большинстве Linux-дистрибутивов.

Она управляет:

- сервисами;
- логами;
- таймерами;
- mount-point'ами;
- targets;
- sockets;
- зависимостями между сервисами.

Главная идея:

> systemd описывает компоненты системы через unit-файлы.

---

# Unit files

**Unit** — это объект, которым управляет systemd.

Например:

```
nginx.service
ssh.service
backup.timer
multi-user.target
```

Посмотреть unit-файлы:

```
systemctl list-units
```

Посмотреть только сервисы:

```
systemctl list-units --type=service
```

Посмотреть все unit'ы, включая неактивные:

```
systemctl list-unit-files
```

---

# Service unit

**Service unit** описывает сервис.

Например:

```
nginx.service
docker.service
postgresql.service
```

Обычно unit-файлы лежат тут:

```
/etc/systemd/system/
```

и тут:

```
/lib/systemd/system/
```

или:

```
/usr/lib/systemd/system/
```

Важно:

```
/etc/systemd/system/
```

имеет приоритет над системными unit-файлами.

Обычно свои unit'ы кладут именно сюда.

---

# systemctl

`systemctl` — главная команда для управления systemd.

Общий формат:

```
systemctl <действие> <unit>
```

Например:

```
sudo systemctl restart nginx
```

---

## systemctl status

Показывает состояние сервиса:

```
systemctl status nginx
```

Пример важного вывода:

```
Active: active (running)
Main PID: 1234
```

Возможные состояния:

```
active
inactive
failed
activating
deactivating
```

Если сервис упал, часто будет:

```
Active: failed
```

---

## systemctl start

Запустить сервис сейчас:

```
sudo systemctl start nginx
```

Важно:

`start` не включает автозапуск после перезагрузки.

---

## systemctl stop

Остановить сервис сейчас:

```
sudo systemctl stop nginx
```

---

## systemctl restart

Перезапустить сервис:

```
sudo systemctl restart nginx
```

Это значит:

```
stop + start
```

Процесс полностью останавливается и запускается заново.

---

## systemctl reload

Перечитать конфигурацию без полного рестарта:

```
sudo systemctl reload nginx
```

Например, nginx может перечитать конфиг без полного убийства всех worker-процессов.

Но reload работает только если сам сервис это поддерживает.

---

## Разница restart и reload

`restart`:

```
stop + start
```

То есть сервис полностью перезапускается.

`reload`:

```
перечитать конфиг без полного рестарта
```

Пример:

```
sudo systemctl reload nginx
```

Для nginx это обычно безопаснее, чем restart.

Но если сервис не поддерживает reload, команда может не сработать.

---

## systemctl enable

Включить автозапуск сервиса при загрузке:

```
sudo systemctl enable nginx
```

Важно:

`enable` не запускает сервис прямо сейчас.

То есть:

```
systemctl enable nginx
```

означает:

> запускать nginx автоматически после reboot.

Если надо включить автозапуск и сразу запустить:

```
sudo systemctl enable --now nginx
```

---

## systemctl disable

Отключить автозапуск:

```
sudo systemctl disable nginx
```

Важно:

`disable` не останавливает сервис прямо сейчас.

Если надо отключить автозапуск и сразу остановить:

```
sudo systemctl disable --now nginx
```

---

## systemctl daemon-reload

Перечитать unit-файлы systemd:

```
sudo systemctl daemon-reload
```

Нужно после изменения unit-файла.

Например, изменил:

```
/etc/systemd/system/myapp.service
```

После этого надо выполнить:

```
sudo systemctl daemon-reload
sudo systemctl restart myapp
```

Важно:

`daemon-reload` не рестартует сервис.

Он только говорит systemd:

> перечитай описание unit-файлов.

---

# Структура service unit

Пример:

```
[Unit]
Description=My App
After=network.target

[Service]
ExecStart=/usr/local/bin/myapp
Restart=always
RestartSec=5
Environment=PORT=8080

[Install]
WantedBy=multi-user.target
```

---

## `[Unit]`

Общее описание unit'а и зависимости.

Пример:

```
[Unit]
Description=My App
After=network.target
```

`Description` — человекочитаемое описание.

`After` — запускать после указанного target/service.

Важно:

`After=network.target` означает порядок запуска, но не включает зависимость сам по себе.

---

## `[Service]`

Описание того, как запускать сервис.

Пример:

```
[Service]ExecStart=/usr/local/bin/myapp
Restart=always
RestartSec=5
```

---

## `[Install]`

Описание автозапуска.

Пример:

```
[Install]
WantedBy=multi-user.target
```

Эта секция нужна для `systemctl enable`.

---

# ExecStart

`ExecStart` — команда запуска сервиса.

Пример:

```
ExecStart=/usr/bin/python3 /opt/app/app.py
```

Или:

```
ExecStart=/usr/sbin/nginx -g 'daemon off;'
```

Важно:

в `ExecStart` обычно лучше указывать абсолютные пути.

Плохо:

```
ExecStart=python app.py
```

Лучше:

```
ExecStart=/usr/bin/python3 /opt/app/app.py
```

---

# Restart

`Restart` говорит systemd, когда перезапускать сервис.

Пример:

```
Restart=always
```

Частые варианты:

```
Restart=no
Restart=on-failure
Restart=always
```

## Restart=no

Не перезапускать автоматически.

```
Restart=no
```

## Restart=on-failure

Перезапускать, если сервис завершился с ошибкой.

```
Restart=on-failure
```

Для production-сервисов это часто нормальный вариант.

## Restart=always

Перезапускать почти всегда.

```
Restart=always
```

Даже если процесс завершился успешно.

---

# RestartSec

`RestartSec` — пауза перед перезапуском.

```
RestartSec=5
```

Это значит:

> если сервис упал, подождать 5 секунд и запустить снова.

Полезно, чтобы сервис не перезапускался слишком часто.

---

# Environment

Можно задать переменные окружения прямо в unit-файле:

```
Environment=PORT=8080
Environment=ENV=production
```

Приложение потом увидит их как обычные env-переменные.

Например:

```
echo $PORT
```

---

# EnvironmentFile

`EnvironmentFile` позволяет вынести переменные в отдельный файл.

Unit:

```
[Service]EnvironmentFile=/etc/myapp/myapp.env
ExecStart=/usr/local/bin/myapp
```

Файл:

```
PORT=8080
ENV=productionDB_HOST=localhost
```

Часто используется для конфигов приложения.

Можно сделать необязательный файл через `-`:

```
EnvironmentFile=-/etc/myapp/myapp.env
```

Если файла нет, systemd не упадёт с ошибкой.

---

# WantedBy

`WantedBy` говорит, к какому target привязать сервис при enable.

Пример:

```
[Install]WantedBy=multi-user.target
```

Когда делаешь:

```
sudo systemctl enable myapp
```

systemd создаёт symlink примерно сюда:

```
/etc/systemd/system/multi-user.target.wants/myapp.service
```

Смысл:

> когда система входит в multi-user.target, запусти myapp.service.

---

# Target

**Target** — это группа unit'ов или состояние системы.

Пример:

```
multi-user.target
graphical.target
rescue.target
```

Похоже на старые runlevels.

Частые targets:

```
multi-user.target
```

обычный серверный режим без GUI.

```
graphical.target
```

режим с графическим интерфейсом.

```
rescue.target
```

режим восстановления.

Посмотреть текущий target:

```
systemctl get-default
```

Поставить default target:

```
sudo systemctl set-default multi-user.target
```

---

# Timer unit

**Timer unit** — systemd-аналог cron.

Обычно состоит из двух файлов:

```
backup.service
backup.timer
```

`backup.service` описывает, что запускать.

`backup.timer` описывает, когда запускать.

Пример `backup.service`:

```
[Unit]Description=Run backup[Service]Type=oneshotExecStart=/usr/local/bin/backup.sh
```

Пример `backup.timer`:

```
[Unit]
Description=Run backup daily
[Timer]
OnCalendar=dailyPersistent=true
[Install
]WantedBy=timers.target
```

Включить timer:

```
sudo systemctl enable --now backup.timer
```

Посмотреть timers:

```
systemctl list-timers
```

---

# Как написать свой systemd service

Допустим, есть приложение:

```
/opt/myapp/app.py
```

Создаём unit:

```
sudo nano /etc/systemd/system/myapp.service
```

Содержимое:

```
[Unit]Description=My Python AppAfter=network.target
[Service]
WorkingDirectory=/opt/myapp
ExecStart=/usr/bin/python3 /opt/myapp/app.py
Restart=on-failure
RestartSec=5
Environment=PORT=8080
[Install]
WantedBy=multi-user.target
```

Потом:

```
sudo systemctl daemon-reload
sudo systemctl start myapp
sudo systemctl status myapp
```

Включить автозапуск:

```
sudo systemctl enable myapp
```

Или сразу:

```
sudo systemctl enable --now myapp
```

---

# Как смотреть логи systemd-сервиса

Главная команда:

```
journalctl
```

Логи конкретного сервиса:

```
journalctl -u nginx
```

Смотреть в реальном времени:

```
journalctl -u nginx -f
```

За последнее время:

```
journalctl -u nginx --since "1 hour ago"
```

С конкретной даты:

```
journalctl -u nginx --since "2026-07-04 10:00"
```

Последние 100 строк:

```
journalctl -u nginx -n 100
```

Логи текущей загрузки:

```
journalctl -u nginx -b
```

---

# journalctl

`journalctl` читает systemd journal.

Это централизованные логи systemd.

Просто открыть все логи:

```
journalctl
```

С конца:

```
journalctl -e
```

Следить live:

```
journalctl -f
```

---

## journalctl -u service

Логи конкретного сервиса:

```
journalctl -u ssh
```

```
journalctl -u nginx
```

```
journalctl -u docker
```

---

## journalctl -f

Follow mode:

```
journalctl -f
```

Похоже на:

```
tail -f
```

Для конкретного сервиса:

```
journalctl -u nginx -f
```

---

## journalctl --since

Фильтр по времени:

```
journalctl --since "today"
```

```
journalctl --since "yesterday"
```

```
journalctl --since "30 minutes ago"
```

```
journalctl -u nginx --since "1 hour ago"
```

---

# Почему сервис не стартует

Типовые причины:

## 1. Ошибка в unit-файле

Проверить:

```
systemctl status myapp
```

и:

```
journalctl -u myapp -xe
```

---

## 2. Не сделали daemon-reload

Если изменил unit-файл, но не сделал:

```
sudo systemctl daemon-reload
```

systemd может использовать старую версию unit'а.

---

## 3. Неправильный путь в ExecStart

Плохо:

```
ExecStart=python app.py
```

Лучше:

```
ExecStart=/usr/bin/python3 /opt/myapp/app.py
```

Проверить путь:

```
which python3
```

---

## 4. Нет прав на выполнение файла

Например:

```
ExecStart=/opt/myapp/start.sh
```

Проверить:

```
ls -l /opt/myapp/start.sh
```

Добавить execute bit:

```
chmod +x /opt/myapp/start.sh
```

---

## 5. Неправильный пользователь

Если в unit'е указано:

```
User=myapp
```

то сервис запускается от пользователя `myapp`.

Проверить доступы:

```
sudo -u myapp /opt/myapp/start.sh
```

---

## 6. Приложение не может открыть порт

Например, порт уже занят.

Проверить:

```
ss -lntp
```

Или конкретный порт:

```
sudo ss -lntp | grep ':8080'
```

---

## 7. Нет переменных окружения

В интерактивной shell переменные есть, а в systemd — нет.

Например, руками работает:

```
python3 app.py
```

А через systemd не работает.

Решение:

```
Environment=PORT=8080EnvironmentFile=/etc/myapp/myapp.env
```

---

## 8. Неправильная рабочая директория

Приложение ожидает файлы рядом с собой.

Добавить:

```
WorkingDirectory=/opt/myapp
```

---

## 9. Сервис быстро падает и уходит в failed

Посмотреть:

```
systemctl status myappjournalctl -u myapp -n 100
```

Иногда будет:

```
Start request repeated too quickly
```

Это значит, что systemd пытался много раз перезапустить сервис, но он постоянно падал.

Можно сбросить failed-состояние:

```
sudo systemctl reset-failed myapp
```