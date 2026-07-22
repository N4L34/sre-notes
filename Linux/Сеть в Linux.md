# 1. Базовые понятия

## IP address

IP address — это адрес устройства или сетевого интерфейса в сети.

Пример IPv4:

```
192.168.1.101
0.0.0.5
172.16.20.3
```

IP нужен, чтобы другие устройства могли отправлять пакеты именно этому хосту.

Посмотреть IP-адреса на Linux:

```
ip addr
```

или короче:

```
ip a
```

Пример:

```
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>    inet 192.168.1.10/24
```

Здесь:

```
192.168.1.10/24
```

означает:

- IP адрес: `192.168.1.10`
- subnet mask: `/24`

---

## Subnet

Subnet — это подсеть, то есть диапазон IP-адресов.

Например:

```
192.168.1.0/24
```

означает диапазон:

```
192.168.1.0 - 192.168.1.255
```

Обычно:

```
192.168.1.10/24
```

значит, что хост находится в сети:

```
192.168.1.0/24
```

`/24` означает, что первые 24 бита — это сеть, а оставшиеся 8 бит — адреса хостов.

Примеры:

```
10.0.0.0/8172.16.0.0/12192.168.0.0/16192.168.1.0/24
```

Для DevOps/SRE важно понимать:

```
192.168.1.10/24
```

и

```
192.168.2.10/24
```

находятся в разных подсетях.

А вот:

```
192.168.1.10/24
192.168.1.20/24
```

находятся в одной подсети.

---

## Gateway

Gateway — это шлюз, через который сервер выходит в другие сети.

Например, твой сервер:

```
192.168.1.10
```

А gateway:

```
192.168.1.1
```

Если сервер хочет обратиться к адресу не из своей подсети, например:

```
8.8.8.8
```

он отправит пакет на gateway.

Посмотреть gateway:

```
ip route
```

Пример:

```
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.10
```

Главная строка:

```
default via 192.168.1.1 dev eth0
```

означает:

> если не знаешь, куда отправлять пакет, отправляй его через `192.168.1.1`.

---

## DNS

DNS — это система, которая превращает доменные имена в IP-адреса.

Например:

```
google.com
```

превращается в IP:

```
142.250.185.14
```

Проверить DNS:

```
dig google.com
```

или:

```
nslookup google.com
```

или:

```
host google.com
```

Если IP пингуется, а домен нет, часто проблема именно в DNS.

Пример:

```
ping 8.8.8.8
```

работает, а:

```
ping google.com
```

не работает.

Это значит:

> интернет есть, но DNS не работает.

---

## Routing

Routing — это процесс выбора пути, куда отправить пакет.

Linux смотрит в routing table и решает:

- пакет в локальную сеть?
- отправить через gateway?
- отправить через VPN-интерфейс?
- отправить через default route?

Посмотреть маршруты:

```
ip route
```

Пример:

```
default via 10.0.0.1 dev eth0
10.0.0.0/24 dev eth0 proto kernel scope link src 10.0.0.10
172.16.0.0/16 via 10.0.0.254 dev eth0
```

Что это значит:

```
default via 10.0.0.1 dev eth0
```

всё неизвестное отправлять через `10.0.0.1`.

```
10.0.0.0/24 dev eth0
```

сеть `10.0.0.0/24` доступна напрямую через `eth0`.

```
172.16.0.0/16 via 10.0.0.254 dev eth0
```

в сеть `172.16.0.0/16` ходить через `10.0.0.254`.

---

# 2. TCP, UDP, ICMP

## TCP

TCP — это протокол с установлением соединения.

Он гарантирует:

- доставку данных;
- правильный порядок;
- повторную отправку потерянных пакетов;
- контроль потока.

Используется для:

```
HTTP
HTTPS
SSH
PostgreSQL
MySQL
Redis
Kafka
SMTP
```

Примеры TCP-портов:

```
22    SSH
80    HTTP
443   HTTPS
5432  PostgreSQL
3306  MySQL
6379  Redis
```

TCP-соединение проходит через handshake:

```
SYN -> SYN/ACK -> ACK
```

Если TCP не может подключиться, ты часто видишь:

```
Connection refused
Connection timed out
No route to host
```

---

## UDP

UDP — это протокол без установления соединения.

Он не гарантирует доставку и порядок пакетов.

Используется там, где важна скорость или простота:

```
DNS
DHCP
NTP
VoIP
VPN
streaming
```

Примеры UDP-портов:

```
53   DNS
67   DHCP
123  NTP
```

Главное отличие:

TCP:

> сначала устанавливает соединение.

UDP:

> просто отправляет пакет.

---

## ICMP

ICMP — это служебный сетевой протокол.

Он используется для диагностики и сообщений об ошибках.

Самый известный пример — `ping`.

```
ping 8.8.8.8
```

`ping` использует ICMP Echo Request и ICMP Echo Reply.

Важно:

если `ping` не работает, это не всегда значит, что сервер недоступен.

ICMP может быть заблокирован firewall’ом.

Например:

```
ping example.com
```

не работает, но:

```
curl https://example.com
```

работает.

Значит, HTTP/HTTPS доступен, просто ICMP запрещён.

---

# 3. Ports

Port — это номер, по которому приложение принимает соединения.

IP отвечает на вопрос:

> на какой сервер идти?

Port отвечает на вопрос:

> в какое приложение на этом сервере идти?

Пример:

```
192.168.1.10:22
```

это SSH.

```
192.168.1.10:443
```

это HTTPS.

Один сервер может иметь много сервисов:

```
22    sshd80    nginx443   nginx5432  postgres6379  redis
```

---

# 4. Listening socket

Listening socket — это порт, который сервис открыл и слушает.

Например, nginx слушает `80` и `443`.

Проверить:

```
ss -lntp
```

Где:

```
-l   listening
-n   показывать числа, не резолвить имена
-t   TCP
-p   process
```

Пример:

```
LISTEN 0 511 0.0.0.0:80 0.0.0.0:* users:(("nginx",pid=1234,fd=6))
```

Это значит:

> nginx слушает порт `80` на всех IPv4-интерфейсах.

Если видишь:

```
127.0.0.1:8080
```

значит сервис доступен только локально.

Если видишь:

```
0.0.0.0:8080
```

значит сервис слушает на всех IPv4-интерфейсах.

Если видишь:

```
[::]:8080
```

значит сервис слушает IPv6, а иногда и IPv4 тоже, зависит от настроек системы.

---

# 5. Established connection

Established connection — это уже установленное TCP-соединение.

Посмотреть:

```
ss -tan
```

Пример:

```
ESTAB 0 0 10.0.0.5:443 10.0.0.20:53244
```

Это значит:

```
10.0.0.20:53244
```

подключился к:

```
10.0.0.5:443
```

Состояния TCP:

```
LISTEN       сервис слушает порт
ESTAB        соединение установлено
SYN-SENT     клиент отправил SYN
SYN-RECV     сервер получил SYN
TIME-WAIT    соединение закрывается
CLOSE-WAIT   удалённая сторона закрыла соединение, локальная ещё нет
```

Для SRE важно:

много `TIME-WAIT` — не всегда проблема.

много `CLOSE-WAIT` — часто проблема приложения, которое не закрывает соединения.

---

# 6. Команды диагностики

## `ip addr`

Показывает сетевые интерфейсы и IP-адреса.

```
ip addr
```

Пример:

```
ip addr show eth0
```

Смотри:

- есть ли IP;
- интерфейс `UP` или `DOWN`;
- какая подсеть;
- какой MAC-адрес.

---

## `ip route`

Показывает таблицу маршрутизации.

```
ip route
```

Самое важное:

```
default via ...
```

Если нет default route, сервер может не иметь выхода наружу.

---

## `ss`

Современная замена `netstat`.

Посмотреть слушающие TCP-порты:

```
ss -lntp
```

Посмотреть все TCP-соединения:

```
ss -tan
```

Посмотреть UDP:

```
ss -lunp
```

Посмотреть кто слушает порт `80`:

```
ss -lntp | grep ':80'
```

Посмотреть соединения к PostgreSQL:

```
ss -tan | grep ':5432'
```

---

## `netstat`

Старая команда, часто ещё встречается.

```
netstat -lntp
```

Но на современных Linux чаще используют:

```
ss
```

На собеседовании можно сказать:

> `netstat` устарел, сейчас обычно используют `ss`, потому что он быстрее и работает через современные kernel-интерфейсы.

---

## `ping`

Проверяет сетевую доступность через ICMP.

```
ping 8.8.8.8
ping google.com
```

Если:

```
ping 8.8.8.8
```

работает, а:

```
ping google.com
```

нет — проблема DNS.

Если оба не работают — проблема может быть в сети, маршрутах, gateway, firewall.

---

## `traceroute`

Показывает путь пакета до узла.

```
traceroute google.com
```

Если команды нет:

```
sudo apt install traceroute
```

`traceroute` помогает понять, где обрывается маршрут.

---

## `mtr`

`mtr` — это как `ping` + `traceroute`.

```
mtr google.com
```

или без интерактивного режима:

```
mtr -rw google.com
```

Полезно для анализа packet loss.

Смотри:

- на каком hop появляется loss;
- есть ли latency spikes;
- доходит ли трафик до финального адреса.

Важно:

loss на промежуточном hop не всегда проблема, если финальный host отвечает нормально.

---

## `dig`

Главная команда для DNS troubleshooting.

```
dig google.com
```

Проверить конкретный DNS-сервер:

```
dig @8.8.8.8 google.com
```

Проверить A-запись:

```
dig example.com A
```

Проверить AAAA-запись:

```
dig example.com AAAA
```

Проверить MX:

```
dig example.com MX
```

Проверить NS:

```
dig example.com NS
```

Короткий вывод:

```
dig +short google.com
```

Трассировка DNS-резолва:

```
dig +trace example.com
```

---

## `nslookup`

Старая, но часто встречающаяся DNS-команда.

```
nslookup google.com
```

Можно проверить через конкретный DNS:

```
nslookup google.com 8.8.8.8
```

---

## `host`

Простая команда для DNS.

```
host google.com
```

Пример:

```
host -t MX gmail.com
```

---

## `curl`

Одна из самых важных команд для DevOps/SRE.

Проверить HTTP:

```
curl http://example.com
```

Проверить HTTPS:

```
curl https://example.com
```

Показать только headers:

```
curl -I https://example.com
```

Verbose-режим:

```
curl -v https://example.com
```

Проверить конкретный endpoint:

```
curl -v http://localhost:8080/health
```

Проверить статус-код:

```
curl -o /dev/null -s -w "%{http_code}\n" https://example.com
```

Проверить время ответа:

```
curl -o /dev/null -s -w "time_total=%{time_total}\n" https://example.com
```

Проверить через конкретный Host header:

```
curl -H "Host: example.com" http://1.2.3.4
```

Очень полезно, когда DNS ещё не переключён, но надо проверить backend.

---

## `wget`

`wget` чаще используют для скачивания файлов.

```
wget https://example.com/file.tar.gz
```

Можно проверить доступность:

```
wget --spider https://example.com
```

Но для диагностики HTTP обычно удобнее `curl`.

---

## `telnet`

Можно проверить TCP-порт:

```
telnet example.com 443
```

Если подключилось — TCP-порт доступен.

Но `telnet` устарел. Лучше использовать `nc`.

---

## `nc`

`nc`, или netcat, — очень полезная команда для проверки портов.

Проверить TCP-порт:

```
nc -vz example.com 443
```

Пример успешного вывода:

```
Connection to example.com 443 port [tcp/https] succeeded!
```

Проверить локальный порт:

```
nc -vz localhost 5432
```

Открыть простой listening socket:

```
nc -l 8080
```

С другого терминала:

```
nc localhost 8080
```

Можно руками отправлять текст между двумя соединениями.

---

# 7. `/etc/hosts`

`/etc/hosts` — локальный файл соответствия имени и IP.

Пример:

```
127.0.0.1 localhost192.168.1.10 myapp.local
```

Если написать:

```
192.168.1.10 myapp.local
```

то команда:

```
ping myapp.local
```

будет обращаться к `192.168.1.10`.

`/etc/hosts` часто используется для:

- локального тестирования;
- временного переопределения DNS;
- debug до переключения DNS;
- Kubernetes/VM/lab окружений.

Важно:

`/etc/hosts` обычно имеет приоритет выше DNS.

То есть если в `/etc/hosts` написано:

```
1.2.3.4 example.com
```

то система может использовать именно `1.2.3.4`, даже если настоящий DNS показывает другой IP.

---

# 8. `/etc/resolv.conf`

`/etc/resolv.conf` содержит DNS-серверы.

Пример:

```
nameserver 8.8.8.8
nameserver 1.1.1.1
search company.local
```

`nameserver` — DNS-сервер.

`search` — домены, которые автоматически добавляются при резолве коротких имён.

Например:

```
search company.local
```

Если выполнить:

```
ping app01
```

система может попробовать:

```
app01.company.local
```

Важно:

на современных Linux `/etc/resolv.conf` часто управляется автоматически:

- `systemd-resolved`;
- NetworkManager;
- DHCP client;
- cloud-init.

Поэтому руками менять `/etc/resolv.conf` иногда бесполезно: изменения могут быть перезаписаны.

Проверить systemd-resolved:

```
resolvectl status
```

---

# 9. DNS troubleshooting

Типовой порядок проверки DNS:

## 1. Проверить, есть ли сеть вообще

```
ping 8.8.8.8
```

Если IP не пингуется — проблема не в DNS, а в сети/маршрутах/firewall.

---

## 2. Проверить резолв домена

```
dig example.com
```

или:

```
host example.com
```

---

## 3. Проверить конкретный DNS-сервер

```
dig @8.8.8.8 example.com
```

Если через `8.8.8.8` работает, а обычный `dig example.com` нет — проблема в системном DNS resolver.

---

## 4. Проверить `/etc/resolv.conf`

```
cat /etc/resolv.conf
```

Смотри:

```
nameserver
search
options
```

---

## 5. Проверить `/etc/hosts`

```
cat /etc/hosts
```

Возможно, домен переопределён локально.

---

## 6. Проверить systemd-resolved

```
resolvectl status
```

Сбросить DNS cache:

```
sudo resolvectl flush-caches
```

---

## 7. Проверить разные типы записей

```
LISTEN       сервис слушает порт
ESTAB        соединение установлено
SYN-SENT     клиент отправил SYN
SYN-RECV     сервер получил SYN
TIME-WAIT    соединение закрывается
CLOSE-WAIT   удалённая сторона закрыла соединение, локальная ещё нет
```

---

# 10. Проверить, открыт ли порт

С клиентской машины:

```
nc -vz example.com 443
```

или:

```
telnet example.com 443
```

или через curl, если это HTTP/HTTPS:

```
curl -v https://example.com
```

Возможные результаты:

## `Connection refused`

Пример:

```
connect to 1.2.3.4 port 8080 failed: Connection refused
```

Обычно значит:

> сервер доступен, но на этом порту никто не слушает или соединение активно отклоняется.

Проверить на сервере:

```
ss -lntp | grep 8080
```

---

## `Connection timed out`

Обычно значит:

> пакеты не доходят или firewall/drop/security group блокирует трафик.

Причины:

- firewall;
- cloud security group;
- неправильный route;
- сервис слушает только `127.0.0.1`;
- NAT/LB не пробрасывает порт;
- сетевой ACL.

---

## `No route to host`

Обычно проблема маршрутизации или firewall с ICMP unreachable.

Проверить:

```
ip route
ping <gateway>
traceroute <target>
```

---

# 11. Проверить, слушает ли сервис порт

На сервере:

```
ss -lntp
```

Например:

```
ss -lntp | grep ':80'
```

Если nginx работает, увидишь что-то вроде:

```
LISTEN 0 511 0.0.0.0:80 0.0.0.0:* users:(("nginx",pid=1234,fd=6))
```

Если сервис слушает только localhost:

```
127.0.0.1:8080
```

то снаружи к нему подключиться нельзя.

Тогда нужно проверить конфиг приложения.

Например, приложение слушает:

```
127.0.0.1:8080
```

а нужно:

```
0.0.0.0:8080
```

---

# 12. Проверить, доступен ли внешний адрес

Порядок:

```
ping 8.8.8.8
```

Проверить DNS:

```
dig google.com
```

Проверить HTTP/HTTPS:

```
curl -v https://google.com
```

Проверить маршрут:

```
ip route
```

Проверить путь:

```
traceroute google.com
```

или:

```
mtr -rw google.com
```

Проверить firewall:

```
sudo iptables -L -n -vsudo nft list ruleset
```

---

# 13. Проверить TLS-сертификат через `openssl s_client`

Очень полезная команда для HTTPS debug.

```
openssl s_client -connect example.com:443
```

Лучше указывать SNI:

```
openssl s_client -connect example.com:443 -servername example.com
```

Почему `-servername` важен?

Потому что на одном IP может быть много HTTPS-сайтов. Сервер должен знать, для какого домена показать сертификат.

Посмотреть даты сертификата:

```
echo | openssl s_client -connect example.com:443 -servername example.com 2>/dev/null | openssl x509 -noout -dates
```

Вывод:

```
notBefore=...
notAfter=...
```

Посмотреть subject и issuer:

```
echo | openssl s_client -connect example.com:443 -servername example.com 2>/dev/null | openssl x509 -noout -subject -issuer
```

Проверить цепочку сертификатов:

```
openssl s_client -connect example.com:443 -servername example.com -showcerts
```

Типовые TLS-проблемы:

- сертификат истёк;
- сертификат не на тот домен;
- не хватает intermediate certificate;
- сервер отдаёт default certificate;
- SNI не настроен;
- клиент не доверяет CA;
- слишком старый TLS protocol/cipher.

---

# 14. MTU

MTU — Maximum Transmission Unit.

Это максимальный размер пакета, который может пройти через интерфейс без фрагментации.

Обычно Ethernet MTU:

```
1500
```

Посмотреть MTU:

```
ip link
```

Пример:

```
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
```

Проблемы с MTU часто проявляются странно:

- маленькие запросы работают;
- большие запросы зависают;
- SSH подключается, но подвисает;
- HTTP partially работает;
- VPN работает нестабильно.

Проверить MTU через ping:

```
ping -M do -s 1472 8.8.8.8
```

Почему `1472`?

Потому что:

```
1472 bytes payload + 28 bytes IP/ICMP header = 1500
```

Если не проходит, уменьшают размер:

```
ping -M do -s 1400 8.8.8.8
```

Для VPN, tunnels, cloud networking MTU часто меньше 1500.

---

# 15. NAT

NAT — Network Address Translation.

Это механизм подмены IP-адресов.

Самый частый вариант — когда много внутренних машин выходят в интернет через один внешний IP.

Например:

```
10.0.0.1010.0.0.1110.0.0.12
```

выходят наружу через:

```
203.0.113.5
```

NAT бывает:

## Source NAT / SNAT

Меняется source IP.

Например, внутренний сервер выходит в интернет.

```
10.0.0.10 -> 8.8.8.8
```

после NAT становится:

```
203.0.113.5 -> 8.8.8.8
```

---

## Destination NAT / DNAT

Меняется destination IP.

Например, внешний запрос приходит на публичный IP:

```
203.0.113.5:80
```

а отправляется на внутренний сервер:

```
10.0.0.10:80
```

---

## Port forwarding

Это частный случай DNAT.

Например:

```
203.0.113.5:8080 -> 10.0.0.10:80
```

---

# 16. Firewall basics

Firewall фильтрует сетевой трафик.

Он может разрешать или запрещать пакеты по:

- source IP;
- destination IP;
- port;
- protocol;
- interface;
- connection state;
- direction: inbound/outbound/forward.

Типовая логика:

```
разрешить SSH с admin IP
разрешить HTTP/HTTPS всем
запретить всё остальное
```

Важно различать:

## Inbound

Входящий трафик к серверу.

Например:

```
client -> server:443
```

## Outbound

Исходящий трафик с сервера.

Например:

```
server -> api.stripe.com:443
```

## Forward

Трафик, проходящий через сервер как через router/NAT.

---

# 17. `iptables`

`iptables` — классический Linux firewall.

Посмотреть правила:

```
sudo iptables -L -n -v
```

Показать NAT-таблицу:

```
sudo iptables -t nat -L -n -v
```

Основные chains:

```
INPUT
OUTPUT
FORWARD
```

- `INPUT` — входящий трафик на сам сервер;
- `OUTPUT` — исходящий трафик с сервера;
- `FORWARD` — транзитный трафик через сервер.

Пример разрешить SSH:

```
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

Пример запретить входящий трафик на порт 8080:

```
sudo iptables -A INPUT -p tcp --dport 8080 -j DROP
```

Посмотреть правила с номерами:

```
sudo iptables -L INPUT -n --line-numbers
```

Удалить правило:

```
sudo iptables -D INPUT 3
```

Важно:

`iptables`-правила могут не сохраниться после reboot, если не настроено сохранение.

---

# 18. `nftables`

`nftables` — современная замена `iptables`.

Посмотреть ruleset:

```
sudo nft list ruleset
```

Пример структуры:

```
table inet filter {
    chain input {
        type filter hook input priority 0;
        policy drop;

        tcp dport 22 accept
        tcp dport {80, 443} accept
    }
}
```

`nftables` более современный и гибкий.

На новых дистрибутивах `iptables` часто работает поверх `nftables` backend.

Проверить:

```
iptables --version
```

Можешь увидеть:

```
iptables v1.8.x (nf_tables)
```

Это значит, что команда `iptables` использует backend `nftables`.

---

# 19. `ufw`

`ufw` — простой firewall frontend, часто используется в Ubuntu.

Проверить статус:

```
sudo ufw status verbose
```

Разрешить SSH:

```
sudo ufw allow 22/tcp
```

Разрешить HTTP/HTTPS:

```
sudo ufw allow 80/tcpsudo ufw allow 443/tcp
```

Запретить порт:

```
sudo ufw deny 8080/tcp
```

Включить firewall:

```
sudo ufw enable
```

Осторожно:

перед `ufw enable` убедись, что SSH разрешён, иначе можно отрезать себе доступ к серверу.

---

# 20. `firewalld`

`firewalld` часто используется в RHEL/CentOS/Rocky/Alma.

Проверить статус:

```
sudo firewall-cmd --state
```

Посмотреть активные зоны:

```
sudo firewall-cmd --get-active-zones
```

Посмотреть правила:

```
sudo firewall-cmd --list-all
```

Разрешить HTTP:

```
sudo firewall-cmd --add-service=http --permanentsudo firewall-cmd --reload
```

Разрешить порт:

```
sudo firewall-cmd --add-port=8080/tcp --permanentsudo firewall-cmd --reload
```

Убрать порт:

```
sudo firewall-cmd --remove-port=8080/tcp --permanentsudo firewall-cmd --reload
```

Важно:

без `--permanent` правило действует только до перезагрузки firewalld.

---

# 21. Типовые troubleshooting-сценарии

## Сценарий 1: сервис недоступен снаружи

Проверить, работает ли сервис:

```
systemctl status nginx
```

Проверить, слушает ли порт:

```
ss -lntp | grep ':80'
```

Проверить локально:

```
curl -v http://localhost:80
```

Проверить на IP сервера:

```
curl -v http://<server-ip>:80
```

Проверить firewall:

```
sudo iptables -L -n -vsudo nft list rulesetsudo ufw statussudo firewall-cmd --list-all
```

Проверить cloud security group / load balancer / ingress rules.

---

## Сценарий 2: DNS не работает

Проверить IP-доступность:

```
ping 8.8.8.8
```

Проверить DNS:

```
dig example.com
```

Проверить конкретный DNS:

```
dig @8.8.8.8 example.com
```

Проверить resolv.conf:

```
cat /etc/resolv.conf
```

Проверить hosts:

```
cat /etc/hosts
```

Проверить systemd-resolved:

```
resolvectl status
```

---

## Сценарий 3: порт закрыт

С клиента:

```
nc -vz server-ip 8080
```

На сервере:

```
ss -lntp | grep 8080
```

Если нет вывода — сервис не слушает порт.

Если слушает `127.0.0.1:8080`, снаружи порт недоступен.

Если слушает `0.0.0.0:8080`, но снаружи timeout — смотри firewall/security group/routes.

---

## Сценарий 4: `connection refused`

Обычно:

- IP доступен;
- сервер ответил;
- но порт закрыт или сервис не слушает.

Проверить:

```
ss -lntp
systemctl status <service>
journalctl -u <service> -n 100
```

---

## Сценарий 5: `connection timed out`

Обычно:

- firewall drop;
- security group;
- network ACL;
- неправильный route;
- NAT issue;
- сервис слушает не на том интерфейсе.

Проверить:

```
ip routeping <target>traceroute <target>nc -vz <target> <port>
```

---

## Сценарий 6: HTTPS не работает

Проверить curl:

```
curl -v https://example.com
```

Проверить сертификат:

```
openssl s_client -connect example.com:443 -servername example.com
```

Проверить даты:

```
echo | openssl s_client -connect example.com:443 -servername example.com 2>/dev/null | openssl x509 -noout -dates
```

Проверить, какой сертификат отдаётся:

```
echo | openssl s_client -connect example.com:443 -servername example.com 2>/dev/null | openssl x509 -noout -subject -issuer
```

---

# 22. Минимальный набор команд, который надо знать на собесе

```
ip addr
ip route
ss -lntp
ss -tan
ping
traceroute
mtr
dig
nslookup
host
curl -v
curl -I
wget
nc -vz
telnet
cat /etc/hosts
cat /etc/resolv.conf
openssl s_client -connect host:443 -servername host
iptables -L -n -v
nft list ruleset
ufw status
firewall-cmd --list-all
```

---

# 23. Как бы я отвечал на собеседовании

Вопрос:

> Пользователь говорит, что сервис недоступен. Что будешь проверять?

Хороший ответ:

```
Сначала разделю проблему: DNS, сеть, порт, приложение или TLS.

1. Проверю, резолвится ли домен:
   dig service.example.com

2. Проверю, доступен ли IP:
   ping или mtr/traceroute

3. Проверю TCP-порт с клиента:
   nc -vz service.example.com 443

4. На сервере проверю, слушает ли сервис порт:
   ss -lntp

5. Проверю, на каком address bind:
   127.0.0.1 или 0.0.0.0

6. Проверю firewall:
   iptables/nftables/ufw/firewalld

7. Если HTTPS — проверю TLS:
   openssl s_client -connect host:443 -servername host

8. Потом уже пойду в логи приложения:
   journalctl -u service
   /var/log/...
```

Это очень хороший SRE-style ответ, потому что ты не гадаешь, а идёшь по слоям.

---

# 24. Главная mental model

Когда что-то “не работает по сети”, думай слоями:

```
1. Есть ли IP?
2. Есть ли route?
3. Работает ли DNS?
4. Доступен ли host?
5. Открыт ли port?
6. Слушает ли service?
7. Не режет ли firewall?
8. Корректен ли protocol?
9. Если HTTPS — нормальный ли TLS?
10. Что говорят logs?
```

Именно такой подход отличает нормального DevOps/SRE от человека, который просто рандомно запускает команды.