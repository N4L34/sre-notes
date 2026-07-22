

SSH — это основной способ безопасного удалённого подключения к Linux-серверам.

SSH используется для:

- входа на сервер;
    
- выполнения команд на удалённой машине;
    
- копирования файлов;
    
- туннелирования портов;
    
- доступа к закрытым серверам через bastion/jump host.
    

---

# Как работает SSH

SSH означает **Secure Shell**.

Когда ты подключаешься:

```bash
ssh user@server
```

происходит примерно следующее:

1. Клиент подключается к SSH-серверу на удалённой машине.
    
2. По умолчанию используется порт `22`.
    
3. Клиент и сервер договариваются о шифровании.
    
4. Сервер доказывает свою подлинность через host key.
    
5. Пользователь проходит аутентификацию:
    
    - по паролю;
        
    - по SSH-ключу;
        
    - через agent;
        
    - иногда через MFA/SSO.
        
6. После успешной проверки открывается shell на удалённом сервере.
    

Пример:

```bash
ssh root@192.168.1.10
```

Это значит: подключиться по SSH к серверу `192.168.1.10` под пользователем `root`.

---

# Public/private key

SSH-ключ состоит из двух частей:

```text
private key — приватный ключ
public key  — публичный ключ
```

Приватный ключ остаётся у тебя на машине.

Обычно лежит здесь:

```bash
~/.ssh/id_rsa
~/.ssh/id_ed25519
```

Публичный ключ можно копировать на сервер.

Обычно выглядит так:

```bash
~/.ssh/id_rsa.pub
~/.ssh/id_ed25519.pub
```

Принцип простой:

- приватный ключ никому не отдаём;
    
- публичный ключ можно класть на сервер;
    
- сервер проверяет, что у клиента есть приватный ключ, соответствующий публичному.
    

Хорошая практика — использовать `ed25519`:

```bash
ssh-keygen -t ed25519
```

---

# `ssh-keygen`

`ssh-keygen` создаёт SSH-ключи.

Пример:

```bash
ssh-keygen -t ed25519 -C "my-laptop"
```

Что здесь происходит:

```bash
-t ed25519
```

указывает тип ключа.

```bash
-C "my-laptop"
```

добавляет комментарий к ключу. Это удобно, чтобы понимать, чей это ключ.

После команды появятся файлы:

```bash
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
```

Права на приватный ключ должны быть строгими:

```bash
chmod 600 ~/.ssh/id_ed25519
```

На директорию `.ssh`:

```bash
chmod 700 ~/.ssh
```

---

# `ssh-copy-id`

`ssh-copy-id` копирует публичный ключ на сервер.

Пример:

```bash
ssh-copy-id user@server
```

После этого публичный ключ попадёт на сервер в файл:

```bash
~/.ssh/authorized_keys
```

И ты сможешь подключаться без пароля, используя приватный ключ:

```bash
ssh user@server
```

Если SSH работает на нестандартном порту:

```bash
ssh-copy-id -p 2222 user@server
```

---

# `authorized_keys`

Файл `authorized_keys` лежит на сервере у конкретного пользователя:

```bash
/home/user/.ssh/authorized_keys
```

или для root:

```bash
/root/.ssh/authorized_keys
```

Внутри лежат публичные ключи, которым разрешён вход под этим пользователем.

Пример строки:

```text
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAI... my-laptop
```

Если ключ есть в `authorized_keys`, пользователь с соответствующим приватным ключом может войти.

Права должны быть такими:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

Если права слишком широкие, SSH может отказаться использовать ключи.

---

# SSH agent

SSH agent хранит приватные ключи в памяти.

Это нужно, чтобы не вводить passphrase каждый раз.

Например, у тебя есть приватный ключ с паролем. Вместо того чтобы вводить пароль при каждом `ssh`, ты добавляешь ключ в agent.

---

# `ssh-agent`

Запустить agent:

```bash
eval "$(ssh-agent -s)"
```

Проверить переменные окружения:

```bash
echo $SSH_AUTH_SOCK
```

Если `SSH_AUTH_SOCK` задан, значит shell знает, где находится SSH agent.

---

# `ssh-add`

Добавить ключ в SSH agent:

```bash
ssh-add ~/.ssh/id_ed25519
```

Посмотреть добавленные ключи:

```bash
ssh-add -l
```

Удалить все ключи из agent:

```bash
ssh-add -D
```

Типичный сценарий:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
ssh user@server
```

---

# SSH config

Чтобы не писать длинные команды, можно использовать SSH config.

Файл:

```bash
~/.ssh/config
```

Пример:

```sshconfig
Host prod
    HostName 192.168.1.10
    User deploy
    Port 22
    IdentityFile ~/.ssh/id_ed25519
```

Теперь вместо:

```bash
ssh deploy@192.168.1.10 -i ~/.ssh/id_ed25519
```

можно писать:

```bash
ssh prod
```

Ещё пример:

```sshconfig
Host github.com
    User git
    IdentityFile ~/.ssh/github_ed25519
```

Права на config:

```bash
chmod 600 ~/.ssh/config
```

---

# Jump host

Jump host — это промежуточный сервер, через который ты подключаешься к другому серверу.

Например:

```text
laptop -> jump-host -> private-server
```

Это нужно, когда внутренний сервер недоступен напрямую из интернета.

Команда:

```bash
ssh -J user@jump.example.com user@private-server
```

Пример:

```bash
ssh -J bastion@1.2.3.4 app@10.0.1.15
```

Это значит:

1. Сначала подключиться к `1.2.3.4` под пользователем `bastion`.
    
2. Через него подключиться к `10.0.1.15` под пользователем `app`.
    

---

# Bastion host

Bastion host — это специально выделенный сервер для входа во внутреннюю инфраструктуру.

Обычно он:

- доступен из интернета;
    
- хорошо защищён;
    
- логирует подключения;
    
- имеет ограниченный список пользователей;
    
- используется как единая точка входа.
    

Разница между jump host и bastion host:

```text
jump host — роль промежуточного сервера
bastion host — защищённый jump host для доступа в инфраструктуру
```

На практике эти термины часто используют как синонимы.

---

# SSH config для Jump Host

Можно настроить так:

```sshconfig
Host bastion
    HostName 1.2.3.4
    User bastion
    IdentityFile ~/.ssh/bastion_key

Host private-app
    HostName 10.0.1.15
    User app
    IdentityFile ~/.ssh/app_key
    ProxyJump bastion
```

Теперь подключение:

```bash
ssh private-app
```

автоматически пойдёт через bastion.

---

# Port forwarding

SSH умеет пробрасывать порты.

Это называется SSH tunneling.

Есть три основных типа:

- local forwarding;
    
- remote forwarding;
    
- dynamic forwarding.
    

Чаще всего в SRE используется local forwarding.

---

# Local forwarding

Local forwarding пробрасывает порт с твоей локальной машины на удалённый ресурс.

Формат:

```bash
ssh -L local_port:target_host:target_port user@ssh_server
```

Пример:

```bash
ssh -L 5432:localhost:5432 user@db-server
```

Теперь на твоей машине можно подключаться к:

```bash
localhost:5432
```

а трафик пойдёт на `db-server:5432`.

Частый пример с базой данных:

```bash
ssh -L 15432:localhost:5432 deploy@prod-db
```

Теперь локально:

```bash
psql -h localhost -p 15432
```

Подключение пойдёт к PostgreSQL на `prod-db`.

Важно понимать:

```bash
-L 15432:localhost:5432
```

означает:

```text
локальный порт 15432 -> с точки зрения удалённого сервера localhost:5432
```

---

# Local forwarding через bastion

Допустим, база доступна только из внутренней сети:

```text
laptop -> bastion -> db.internal:5432
```

Команда:

```bash
ssh -L 15432:db.internal:5432 user@bastion
```

Теперь локально:

```bash
psql -h localhost -p 15432
```

Трафик пойдёт так:

```text
localhost:15432 -> bastion -> db.internal:5432
```

---

# Remote forwarding

Remote forwarding делает обратное: открывает порт на удалённом сервере и пробрасывает его на твою локальную машину.

Формат:

```bash
ssh -R remote_port:local_host:local_port user@server
```

Пример:

```bash
ssh -R 8080:localhost:3000 user@server
```

Это значит:

```text
server:8080 -> твой localhost:3000
```

Это полезно, если нужно временно показать локальный сервис на удалённом сервере.

Например, у тебя локально работает приложение:

```bash
localhost:3000
```

Ты делаешь:

```bash
ssh -R 8080:localhost:3000 user@server
```

Теперь на сервере можно обратиться к:

```bash
localhost:8080
```

и попасть в твой локальный сервис.

---

# `scp`

`scp` копирует файлы по SSH.

Скопировать файл на сервер:

```bash
scp file.txt user@server:/tmp/
```

Скопировать файл с сервера:

```bash
scp user@server:/var/log/nginx/access.log .
```

Скопировать директорию:

```bash
scp -r ./mydir user@server:/tmp/
```

Использовать нестандартный порт:

```bash
scp -P 2222 file.txt user@server:/tmp/
```

Важно:

```bash
ssh использует -p
scp использует -P
```

Пример:

```bash
ssh -p 2222 user@server
scp -P 2222 file.txt user@server:/tmp/
```

---

# `rsync`

`rsync` тоже копирует файлы по SSH, но умнее, чем `scp`.

Он умеет:

- копировать только изменившиеся файлы;
    
- сохранять права;
    
- удалять лишние файлы на стороне назначения;
    
- показывать прогресс;
    
- эффективно синхронизировать директории.
    

Пример:

```bash
rsync -avz ./site/ user@server:/var/www/site/
```

Расшифровка:

```bash
-a
```

archive mode: сохраняет права, владельцев, symlink и структуру.

```bash
-v
```

verbose: подробный вывод.

```bash
-z
```

сжатие при передаче.

С прогрессом:

```bash
rsync -avz --progress ./site/ user@server:/var/www/site/
```

Удалять на сервере файлы, которых больше нет локально:

```bash
rsync -avz --delete ./site/ user@server:/var/www/site/
```

Осторожно с `--delete`: можно случайно удалить нужные файлы.

Важный момент со слешем:

```bash
rsync -avz dir user@server:/tmp/
```

создаст:

```text
/tmp/dir
```

А вот:

```bash
rsync -avz dir/ user@server:/tmp/dir/
```

скопирует содержимое директории внутрь `/tmp/dir/`.

---

# Как безопасно настроить SSH

Основной конфиг SSH-сервера:

```bash
/etc/ssh/sshd_config
```

После изменения конфига нужно проверить синтаксис:

```bash
sudo sshd -t
```

Если ошибок нет, перезапустить SSH:

```bash
sudo systemctl restart ssh
```

или:

```bash
sudo systemctl restart sshd
```

Название сервиса зависит от дистрибутива.

Проверить статус:

```bash
sudo systemctl status ssh
```

или:

```bash
sudo systemctl status sshd
```

---

## Отключить root login

В `/etc/ssh/sshd_config`:

```sshconfig
PermitRootLogin no
```

Это запрещает прямой вход под root по SSH.

Лучше заходить под обычным пользователем и использовать `sudo`.

---

## Отключить вход по паролю

```sshconfig
PasswordAuthentication no
```

После этого вход будет только по ключам.

Важно: перед этим нужно проверить, что вход по ключу уже работает, иначе можно заблокировать себе доступ.

---

## Разрешить только конкретных пользователей

```sshconfig
AllowUsers deploy admin
```

Теперь по SSH смогут входить только пользователи `deploy` и `admin`.

---

## Использовать нестандартный порт

```sshconfig
Port 2222
```

Это не полноценная защита, но уменьшает количество автоматических брутфорс-попыток.

Подключение:

```bash
ssh -p 2222 user@server
```

---

## Ограничить SSH по firewall

Например, разрешить SSH только со своего IP.

Через `ufw`:

```bash
sudo ufw allow from 1.2.3.4 to any port 22 proto tcp
```

Или для нестандартного порта:

```bash
sudo ufw allow from 1.2.3.4 to any port 2222 proto tcp
```

---

## Запретить пустые пароли

```sshconfig
PermitEmptyPasswords no
```

---

## Ограничить попытки входа

```sshconfig
MaxAuthTries 3
```

---

## Не использовать старые алгоритмы

Современные OpenSSH обычно уже безопасны по умолчанию, но важно не включать старые слабые алгоритмы вручную.

---

# Пример безопасного `sshd_config`

Пример базовой настройки:

```sshconfig
Port 22
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
PermitEmptyPasswords no
MaxAuthTries 3
AllowUsers deploy
```

После изменения:

```bash
sudo sshd -t
sudo systemctl restart ssh
```

---

# Как не заблокировать себе доступ

Перед изменением SSH-конфига:

1. Открой вторую SSH-сессию.
    
2. В первой сессии меняй конфиг.
    
3. Проверь синтаксис:
    

```bash
sudo sshd -t
```

4. Перезапусти SSH.
    
5. Во второй новой сессии проверь, что вход работает.
    
6. Только потом закрывай старую сессию.
    

Это важная практика для реальных серверов.

---

# Как дебажить SSH-подключение через `ssh -v`

Для дебага используется:

```bash
ssh -v user@server
```

Больше подробностей:

```bash
ssh -vv user@server
```

Ещё больше:

```bash
ssh -vvv user@server
```

Чем больше `v`, тем подробнее вывод.

---

# Что смотреть в `ssh -v`

## Проверка подключения к серверу

Если видишь:

```text
Connection timed out
```

возможные причины:

- сервер недоступен;
    
- порт закрыт firewall;
    
- SSH слушает другой порт;
    
- нет маршрута до сервера.
    

Проверить порт:

```bash
nc -vz server 22
```

или:

```bash
telnet server 22
```

---

## Permission denied

Ошибка:

```text
Permission denied (publickey)
```

Означает, что сервер не принял ключ.

Проверить:

```bash
ssh -v user@server
```

И смотреть строки вида:

```text
Offering public key
Server accepts key
```

Если ключ не предлагается, можно указать его явно:

```bash
ssh -i ~/.ssh/id_ed25519 user@server
```

---

## Неправильный пользователь

Частая ошибка:

```bash
ssh server
```

SSH использует текущего локального пользователя.

Например, если локально ты `alex`, то команда может стать:

```bash
ssh alex@server
```

Если на сервере нужен `deploy`, надо писать:

```bash
ssh deploy@server
```

или настроить `~/.ssh/config`.

---

## Проблемы с правами

На клиенте:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
chmod 600 ~/.ssh/config
```

На сервере:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

## Host key changed

Ошибка:

```text
WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED
```

Это значит, что изменился host key сервера.

Возможные причины:

- сервер переустановили;
    
- изменился IP;
    
- ты подключаешься не к тому серверу;
    
- возможна MITM-атака.
    

Удалить старый ключ:

```bash
ssh-keygen -R server
```

После этого подключиться заново:

```bash
ssh user@server
```

Но перед удалением важно убедиться, что сервер действительно тот самый.

---

# Логи SSH на сервере

На systemd-системах:

```bash
sudo journalctl -u ssh
```

или:

```bash
sudo journalctl -u sshd
```

Смотреть в реальном времени:

```bash
sudo journalctl -u ssh -f
```

На Debian/Ubuntu также часто:

```bash
/var/log/auth.log
```

Пример:

```bash
sudo tail -f /var/log/auth.log
```

На RHEL/CentOS/Rocky/AlmaLinux:

```bash
/var/log/secure
```

Пример:

```bash
sudo tail -f /var/log/secure
```

---

# Полезные команды

Подключиться к серверу:

```bash
ssh user@server
```

Подключиться с конкретным ключом:

```bash
ssh -i ~/.ssh/id_ed25519 user@server
```

Подключиться к нестандартному порту:

```bash
ssh -p 2222 user@server
```

Подключиться с дебагом:

```bash
ssh -v user@server
```

Сгенерировать ключ:

```bash
ssh-keygen -t ed25519 -C "my-key"
```

Скопировать ключ на сервер:

```bash
ssh-copy-id user@server
```

Пробросить локальный порт:

```bash
ssh -L 15432:localhost:5432 user@server
```

Пробросить порт через bastion:

```bash
ssh -L 15432:db.internal:5432 user@bastion
```

Скопировать файл на сервер:

```bash
scp file.txt user@server:/tmp/
```

Скопировать файл с сервера:

```bash
scp user@server:/tmp/file.txt .
```

Синхронизировать директорию:

```bash
rsync -avz ./dir/ user@server:/tmp/dir/
```

---

# Частые вопросы на собеседовании

## Чем SSH key лучше пароля?

Ключ сложнее подобрать брутфорсом, его можно защитить passphrase, удобно отзывать и автоматизировать.

---

## Что такое `authorized_keys`?

Это файл на сервере, где лежат публичные ключи, которым разрешён вход под конкретным пользователем.

---

## Что такое private key?

Это секретная часть SSH-ключа. Её нельзя передавать другим людям и нельзя хранить в открытом доступе.

---

## Что такое bastion host?

Это защищённый промежуточный сервер для доступа во внутреннюю инфраструктуру.

---

## Чем `scp` отличается от `rsync`?

`scp` просто копирует файлы.

`rsync` синхронизирует и передаёт только изменения, поэтому лучше подходит для больших директорий и повторных копирований.

---

## Что делает `ssh -L`?

Пробрасывает локальный порт на удалённый адрес через SSH-туннель.

Пример:

```bash
ssh -L 15432:localhost:5432 user@server
```

После этого локальный порт `15432` ведёт на `localhost:5432` с точки зрения удалённого сервера.

---

## Что делать, если `Permission denied (publickey)`?

Проверить:

```bash
ssh -v user@server
```

Потом проверить:

- правильный ли пользователь;
    
- правильный ли ключ;
    
- есть ли публичный ключ в `authorized_keys`;
    
- правильные ли права на `.ssh`;
    
- разрешён ли `PubkeyAuthentication`;
    
- не запрещён ли пользователь через `AllowUsers`.
    

---

# Главное, что нужно запомнить

SSH — это безопасный удалённый доступ к серверу.

Ключевые файлы:

```bash
~/.ssh/id_ed25519
~/.ssh/id_ed25519.pub
~/.ssh/authorized_keys
~/.ssh/config
/etc/ssh/sshd_config
```

Ключевые команды:

```bash
ssh
ssh-keygen
ssh-copy-id
ssh-agent
ssh-add
scp
rsync
```

Для безопасности:

```sshconfig
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
AllowUsers deploy
```

Для дебага:

```bash
ssh -v user@server
sudo journalctl -u ssh -f
```