```
## 1. Базовые понятия
```
### Private IP ranges  
  
Private IP — это адреса для внутренних сетей.  
  
Основные диапазоны:  
  
- `10.0.0.0/8`  
- `172.16.0.0/12`  
- `192.168.0.0/16`  
  
Они не маршрутизируются напрямую в интернет.

Чтобы private IP вышел в интернет, обычно нужен NAT.

---

### Subnet

Subnet — это подсеть, часть IP-сети.

Например:

```
192.168.1.0/24
```

Значит:

- сеть: `192.168.1.0`
- хосты: `192.168.1.1–192.168.1.254`
- broadcast: `192.168.1.255`

Подсети нужны, чтобы делить сеть на логические части.

---

### Subnet mask

Subnet mask показывает, какая часть IP — сеть, а какая — хост.

Пример:

```
192.168.1.10/24
```

`/24` = маска:

```
255.255.255.0
```

То есть первые 24 бита — сеть.

---

### Broadcast address

Broadcast address — адрес для отправки всем устройствам в подсети.

Пример для:

```
192.168.1.0/24
```

broadcast будет:

```
192.168.1.255
```

Используется, например, в ARP.

---

### Network address

Network address — адрес самой сети.

Пример:

```
192.168.1.0/24
```

`192.168.1.0` — это network address.

Его нельзя назначить обычному хосту.

---

### Socket

Socket — это связка:

```
IP + port + protocol
```

Пример:

```
192.168.1.10:443 TCP
```

Когда сервис слушает порт, он создает listening socket.

Проверка:

```
ss -tulpn
```

---

### Protocol

Protocol — это набор правил обмена данными.

Примеры:

- TCP
- UDP
- ICMP
- HTTP
- DNS
- TLS

Важно понимать: TCP/UDP — транспорт, HTTP/DNS — application layer.

---

### MTU

MTU — максимальный размер пакета, который можно передать без фрагментации.

Обычно Ethernet MTU:

```
1500 bytes
```

Если MTU неправильный, могут быть странные сетевые проблемы:

- сайт открывается частично;
- VPN работает нестабильно;
- большие запросы зависают.

Проверка:

```
ip link
```

---

### MSS

MSS — максимальный размер TCP payload внутри TCP-сегмента.

Обычно:

```
MSS = MTU - IP header - TCP header
```

Для MTU 1500:

```
1500 - 20 - 20 = 1460
```

MSS важен для TCP и Path MTU Discovery.

---

### TTL

TTL — Time To Live.

Это число, которое уменьшается на 1 на каждом роутере.

Если TTL стал 0, пакет удаляется.

Используется в `traceroute`.

Пример:

```
traceroute google.com
```

---

### ARP

ARP нужен, чтобы узнать MAC-адрес по IP-адресу внутри локальной сети.

Пример:

```
Кто имеет 192.168.1.1?Ответ: MAC aa:bb:cc:dd:ee:ff
```

Посмотреть ARP table:

```
ip neigharp -a
```

---

### NAT

NAT меняет IP-адрес в пакете.

Частый пример:

```
private IP → public IP
```

Например:

```
192.168.1.10 → 5.5.5.5
```

Нужен, чтобы много внутренних машин могли выходить в интернет через один public IP.

---

### PAT

PAT — это разновидность NAT, где дополнительно меняется port.

Пример:

```
192.168.1.10:50000 → 5.5.5.5:30001
192.168.1.11:50000 → 5.5.5.5:30002
```

Именно PAT чаще всего используется дома, в офисах и облаках.

---

## 2. TCP/IP и OSI

### TCP/IP model

TCP/IP model — практическая модель сети.

Обычно 4 слоя:

- Application
- Transport
- Internet
- Link

Пример:

```
HTTP → TCP → IP → Ethernet
```

---

### L1 Physical

Physical layer — физическая передача битов.

Примеры:

- кабель;
- Wi-Fi radio;
- оптика;
- сигнал.

Проблемы L1:

- кабель не подключен;
- порт down;
- плохой сигнал;
- битые пакеты.

---

### L2 Data Link

L2 отвечает за передачу внутри локальной сети.

Основные сущности:

- MAC address;
- Ethernet frame;
- switch;
- VLAN;
- ARP.

Команды:

```
ip link
bridge link
```

---

### L3 Network

L3 отвечает за IP-адресацию и маршрутизацию.

Основные сущности:

- IP address;
- subnet;
- route;
- gateway;
- router.

Команды:

```
ip addr
ip route
ping
traceroute
```

---

### L4 Transport

L4 отвечает за доставку между процессами через порты.

Основные протоколы:

- TCP
- UDP

Примеры:

```
TCP 443
UDP 53
```

Команды:

```
ss -tulpn
nc -vz host port
```

---

### L7 Application

L7 — уровень приложений.

Примеры:

- HTTP
- DNS
- SMTP
- SSH
- TLS

Команды:

```
curl
dig
openssl 
s_client
ssh -v
```

---

### Ethernet

Ethernet — основной L2-протокол в локальных сетях.

Он работает с:

- MAC-адресами;
- Ethernet frames;
- switches;
- VLAN.

---

### Encapsulation

Encapsulation — это когда данные заворачиваются в заголовки разных уровней.

Пример:

```
HTTP data→ TCP segment→ IP packet→ Ethernet frame
```

---

### Decapsulation

Decapsulation — обратный процесс.

На принимающей стороне заголовки снимаются:

```
Ethernet frame→ IP packet→ TCP segment→ HTTP data
```

---

### Packet

Packet — обычно единица данных на L3.

То есть IP packet.

Содержит:

- source IP;
- destination IP;
- payload.

---

### Frame

Frame — единица данных на L2.

Ethernet frame содержит:

- source MAC;
- destination MAC;
- payload;
- checksum.

---

### Segment

Segment — единица данных TCP.

TCP segment содержит:

- source port;
- destination port;
- sequence number;
- flags.

---

### Datagram

Datagram — обычно единица данных UDP.

UDP datagram содержит:

- source port;
- destination port;
- length;
- checksum.

---

## 3. Routing

### Static route

Static route — маршрут, заданный вручную.

Пример:

```
ip route add 10.10.0.0/16 via 192.168.1.1
```

Используется, когда нужно явно сказать системе, куда отправлять трафик.

---

### Dynamic routing basics

Dynamic routing — маршруты строятся автоматически через routing protocols.

Примеры:

- BGP
- OSPF
- RIP

Для DevOps/SRE чаще всего важно понимать BGP на базовом уровне, особенно в cloud/networking.

---

### Default route

Default route — маршрут по умолчанию.

Используется, если нет более точного маршрута.

Пример:

```
default via 192.168.1.1 dev eth0
```

Проверка:

```
ip route
```

---

### Host route

Host route — маршрут до одного конкретного IP.

Обычно `/32` для IPv4.

Пример:

```
ip route add 8.8.8.8/32 via 192.168.1.1
```

---

### Network route

Network route — маршрут до целой сети.

Пример:

```
10.10.0.0/16 via 192.168.1.1
```

Значит весь трафик в `10.10.x.x` пойдет через `192.168.1.1`.

---

### Route metric

Metric — приоритет маршрута.

Чем меньше metric, тем предпочтительнее маршрут.

Пример:

```
default via 192.168.1.1 metric 100
default via 192.168.1.2 metric 200
```

Будет выбран первый маршрут.

---

### Policy routing basics

Policy routing — маршрутизация не только по destination IP, но и по правилам.

Например:

- по source IP;
- по interface;
- по mark;
- по custom routing table.

Команды:

```
ip rule
ip route show table 100
```

---

### Asymmetric routing

Asymmetric routing — запрос идет одним путем, а ответ возвращается другим.

Это может ломать:

- firewall;
- NAT;
- stateful security groups;
- load balancers.

Симптом: пакеты вроде уходят, но ответа нет.

---

### Blackhole route

Blackhole route — маршрут, который просто дропает трафик.

Пример:

```
ip route add blackhole 10.10.10.0/24
```

Используется для блокировки или защиты.

---

## 4. TCP

### TCP handshake

TCP handshake — установка TCP-соединения в 3 шага:

```
SYN →
← SYN-ACK
ACK →
```

После этого соединение становится `ESTABLISHED`.

---

### SYN

SYN — первый пакет для открытия TCP-соединения.

Клиент говорит:

```
Хочу установить соединение
```

---

### SYN-ACK

SYN-ACK — ответ сервера на SYN.

Сервер говорит:

```
Ок, я готов, подтверждаю
```

---

### ACK

ACK — подтверждение получения данных или этапа соединения.

В handshake третий пакет ACK завершает установку TCP-соединения.

---

### FIN

FIN — корректное закрытие TCP-соединения.

Означает:

```
Я больше не буду отправлять данные
```

---

### RST

RST — резкий сброс соединения.

Обычно значит:

- порт закрыт;
- приложение оборвало соединение;
- firewall/load balancer сбросил сессию.

---

### TCP states

TCP states — состояния TCP-соединения.

Смотреть:

```
ss -tan
netstat -tan
```

Главные:

- `LISTEN`
- `SYN_SENT`
- `SYN_RECV`
- `ESTABLISHED`
- `TIME_WAIT`
- `CLOSE_WAIT`
- `FIN_WAIT`

---

### LISTEN

`LISTEN` — сервис слушает порт и ждет подключения.

Пример:

```
LISTEN 0 128 0.0.0.0:80
```

---

### ESTABLISHED

`ESTABLISHED` — TCP-соединение установлено и активно.

Пример:

```
ESTAB 0 0 10.0.0.5:443 10.0.0.10:52000
```

---

### TIME_WAIT

`TIME_WAIT` — соединение уже закрыто, но ОС временно держит состояние.

Это нормально.

Нужно, чтобы старые пакеты не попали в новое соединение.

Много `TIME_WAIT` — не всегда проблема.

---

### CLOSE_WAIT

`CLOSE_WAIT` — удаленная сторона закрыла соединение, но локальное приложение еще не закрыло socket.

Часто это проблема приложения.

Если много `CLOSE_WAIT`, приложение может неправильно закрывать соединения.

---

### FIN_WAIT

`FIN_WAIT` — локальная сторона начала закрытие соединения и ждет подтверждений.

Много `FIN_WAIT` может указывать на сетевые проблемы или странное поведение приложения.

---

### SYN_SENT

`SYN_SENT` — клиент отправил SYN и ждет SYN-ACK.

Если зависает в `SYN_SENT`, возможны причины:

- сервер недоступен;
- firewall дропает;
- routing issue;
- security group не пускает.

---

### SYN_RECV

`SYN_RECV` — сервер получил SYN, отправил SYN-ACK и ждет ACK.

Много `SYN_RECV` может быть при:

- SYN flood;
- packet loss;
- проблемах с обратным маршрутом.

---

### Retransmission

Retransmission — повторная отправка TCP-пакета.

Происходит, если ACK не пришел.

Причины:

- packet loss;
- перегрузка сети;
- firewall drop;
- MTU issues.

Смотреть через:

```
tcp
dumpss -i
```

---

### Window size

TCP window size — сколько данных можно отправить без ожидания ACK.

Большое окно = выше throughput.

Маленькое окно может ограничивать скорость передачи.

---

### Congestion control basics

Congestion control — механизм TCP, который регулирует скорость передачи, чтобы не перегрузить сеть.

TCP смотрит на:

- packet loss;
- RTT;
- ACK;
- retransmissions.

---

### Slow start basics

Slow start — TCP начинает передачу осторожно, с маленькой скорости, и постепенно увеличивает.

Это нужно, чтобы не забить сеть сразу.

---

### Keepalive

TCP keepalive — механизм проверки, живо ли соединение.

Если долго нет активности, ОС может отправлять keepalive probes.

Полезно для long-lived connections.

---

### Backlog

Backlog — очередь входящих TCP-соединений.

Если приложение не успевает принимать подключения, очередь может переполниться.

Симптомы:

- connection timeout;
- connection refused;
- много `SYN_RECV`.

---

### Ephemeral ports

Ephemeral ports — временные клиентские порты.

Пример:

```
client: 10.0.0.5:52344 → server: 1.1.1.1:443
```

`52344` — ephemeral port.

Проверка диапазона:

```
cat /proc/sys/net/ipv4/ip_local_port_range
```

---

### Nagle algorithm basics

Nagle algorithm уменьшает количество маленьких TCP-пакетов.

Он может улучшить эффективность, но иногда увеличивает latency.

Для latency-sensitive приложений его иногда отключают через `TCP_NODELAY`.

---

## 5. UDP

### UDP basics

UDP — простой транспортный протокол без установки соединения.

Нет:

- handshake;
- гарантированной доставки;
- retransmission на уровне UDP;
- порядка доставки.

Зато быстро и просто.

---

### Connectionless protocol

Connectionless значит, что нет постоянного соединения.

Клиент просто отправляет datagram.

Если пакет потерялся — UDP сам его не восстановит.

---

### DNS over UDP

Обычный DNS чаще всего работает через UDP port 53.

Пример:

```
dig google.com
```

Если ответ большой, DNS может использовать TCP.

---

### QUIC basics

QUIC — современный протокол поверх UDP.

Используется в HTTP/3.

Он дает:

- шифрование;
- multiplexing;
- быстрее установку соединения;
- лучше работу при packet loss.

---

### Когда использовать UDP

UDP используют, когда важна скорость и допустимы потери.

Примеры:

- DNS;
- VoIP;
- video streaming;
- online games;
- QUIC/HTTP3;
- metrics/log shipping в некоторых случаях.

---

### Ограничения UDP

Минусы UDP:

- нет гарантии доставки;
- нет порядка пакетов;
- нет flow control;
- нет congestion control на уровне UDP;
- приложение само должно решать эти проблемы.

---

## 6. ICMP

### Echo Request

Echo Request — ICMP-запрос от `ping`.

Клиент спрашивает:

```
Ты жив?
```

---

### Echo Reply

Echo Reply — ответ на Echo Request.

Сервер отвечает:

```
Да, я доступен
```

Пример:

```
ping 8.8.8.8
```

---

### Destination Unreachable

Destination Unreachable значит, что пакет нельзя доставить.

Причины:

- сеть недоступна;
- хост недоступен;
- порт недоступен;
- запрещено firewall;
- нужна фрагментация, но стоит DF bit.

---

### Time Exceeded

Time Exceeded возникает, когда TTL стал 0.

Используется в `traceroute`.

Пример:

```
traceroute 8.8.8.8
```

Каждый роутер по пути возвращает ICMP Time Exceeded.

---

### Path MTU Discovery

Path MTU Discovery определяет максимальный MTU на пути до хоста.

Работает через ICMP.

Если ICMP заблокирован, могут быть проблемы:

- соединение установилось, но большие пакеты зависают;
- сайты открываются частично;
- VPN работает странно.

---

### ICMP troubleshooting

ICMP полезен для диагностики сети.

Команды:

```
ping host
traceroute host
mtr host
```

Важно: если `ping` не проходит, это не всегда значит, что сервис недоступен. ICMP может быть просто заблокирован.

  
### DNS architecture  
  
DNS — распределенная система, которая превращает домены в IP.  
  
Пример:


Уровни:

- root DNS
- TLD DNS: `.com`, `.org`, `.ru`
- authoritative DNS конкретного домена
- recursive resolver

---

### Recursive resolver

Recursive resolver — DNS-сервер, который ищет ответ за клиента.

Примеры:

```
8.8.8.8
1.1.1.1
```

Клиент спрашивает resolver, а resolver сам ходит по DNS-иерархии.

---

### Authoritative DNS

Authoritative DNS — сервер, который реально хранит DNS-записи домена.

Например, для `example.com` authoritative DNS знает:

```
A
AAAA
MX
TXT
NS
```

---

### DNS cache

DNS cache — временное хранение DNS-ответов.

Кэш может быть:

- в браузере;
- в ОС;
- в `systemd-resolved`;
- на recursive resolver;
- у провайдера.

---

### DNS propagation

DNS propagation — время, пока новые DNS-записи обновятся в кэшах.

На самом деле DNS не “распространяется”, а старые записи просто живут до истечения TTL.

---

### TTL

TTL в DNS — сколько секунд запись может храниться в кэше.

Пример:

```
example.com 300 A 1.2.3.4
```

`300` = кэшировать 5 минут.

---

### A

`A` запись связывает домен с IPv4.

```
example.com → 1.2.3.4
```

---

### AAAA

`AAAA` запись связывает домен с IPv6.

```
example.com → 2001:db8::1
```

---

### CNAME

`CNAME` — alias на другой домен.

```
www.example.com → example.com
```

---

### MX

`MX` — mail server для домена.

```
example.com → mail.example.com
```

Нужен для email.

---

### TXT

`TXT` — текстовая DNS-запись.

Часто используется для:

- SPF
- DKIM
- DMARC
- проверки владения доменом

---

### NS

`NS` — какие DNS-серверы authoritative для домена.

```
example.com NS ns1.provider.com
```

---

### PTR

`PTR` — reverse DNS: IP → domain.

```
1.2.3.4 → example.com
```

---

### SOA

`SOA` — служебная запись зоны DNS.

Содержит:

- главный DNS-сервер;
- email администратора;
- serial;
- refresh/retry/expire TTL.

---

### SRV

`SRV` — запись для поиска сервиса.

Пример:

```
_service._proto.example.com
```

Используется, например, в Kubernetes, LDAP, SIP.

---

### Reverse DNS

Reverse DNS — поиск домена по IP.

```
dig -x 8.8.8.8
```

Используется для mail, логов, диагностики.

---

### Split-horizon DNS

Split-horizon DNS — разные ответы DNS в зависимости от того, откуда пришел запрос.

Пример:

изнутри компании:

```
app.example.com → 10.0.0.10
```

снаружи:

```
app.example.com →  public IP
```

---

### `/etc/resolv.conf`

Файл с настройками DNS resolver.

Пример:

```
nameserver 8.8.8.8
search local
```

Показывает, какие DNS-серверы использует система.

---

### `systemd-resolved`

`systemd-resolved` — локальный DNS resolver/cache в systemd-системах.

Проверка:

```
resolvectl status
```

Часто `/etc/resolv.conf` указывает на:

```
127.0.0.53
```

---

### DNS over TLS basics

DNS over TLS шифрует DNS-запросы через TLS.

Обычный DNS идет открытым текстом.

DoT обычно использует порт:

```
853
```

---

## 8. HTTP / HTTPS

### PUT

`PUT` обычно полностью заменяет ресурс.

Пример:

```
PUT /users/1
```

Смысл:

```
замени user 1 вот этими данными
```

---

### PATCH

`PATCH` частично изменяет ресурс.

Пример:

```
PATCH /users/1
```

Смысл:

```
измени только email
```

---

### HEAD

`HEAD` как `GET`, но без body.

Нужен, чтобы получить только headers.

Пример:

```
curl -I https://example.com
```

---

### OPTIONS

`OPTIONS` показывает, какие методы поддерживает endpoint.

Часто используется в CORS.

```
OPTIONS /api/users
```

---

### Host header

`Host` говорит серверу, какой домен запрашивает клиент.

Пример:

```
Host: example.com
```

Важно для virtual hosts, nginx, ingress, SNI-like логики на HTTP уровне.

---

### Cookie

`Cookie` хранит данные клиента.

Часто используется для:

- session id;
- auth;
- tracking;
- preferences.

Пример:

```
Cookie: session_id=abc123
```

---

### Content-Type

`Content-Type` говорит, какой формат body.

Пример:

```
Content-Type: application/json
```

---

### Content-Length

`Content-Length` показывает размер body в байтах.

```
Content-Length: 348
```

---

### Keep-Alive

Keep-Alive позволяет переиспользовать TCP-соединение для нескольких HTTP-запросов.

Без него каждый запрос создавал бы новое TCP-соединение.

---

### Status codes

HTTP status code — код результата запроса.

Группы:

- `2xx` success
- `3xx` redirect
- `4xx` client error
- `5xx` server error

---

### 2xx

`2xx` — успешно.

Примеры:

```
200 OK
201 Created
204 No Content
```

---

### 3xx

`3xx` — redirect.

Примеры:

```
301 Moved Permanently
302 Found
304 Not Modified
```

---

### 4xx

`4xx` — ошибка на стороне клиента.

Примеры:

```
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
429 Too Many Requests
```

---

### 5xx

`5xx` — ошибка на стороне сервера.

Примеры:

```
500 Internal Server Error
502 Bad Gateway
503 Service Unavailable
504 Gateway Timeout
```

---

### HTTP/1.1

HTTP/1.1 — классическая версия HTTP.

Особенности:

- текстовый протокол;
- Host header обязателен;
- keep-alive;
- запросы в основном идут последовательно на соединении.

---

### HTTP/2 basics

HTTP/2 — более новая версия HTTP.

Особенности:

- binary protocol;
- multiplexing;
- header compression;
- несколько запросов по одному TCP-соединению.

---

## 9. TLS / SSL

### TLS handshake

TLS handshake — процесс установки защищенного соединения.

Во время него:

- клиент и сервер договариваются о версии TLS;
- сервер показывает certificate;
- проверяется certificate chain;
- создаются session keys.

---

### Certificate

Certificate подтверждает, что public key принадлежит конкретному домену.

Содержит:

- домен;
- public key;
- срок действия;
- issuer;
- подпись CA.

---

### Certificate chain

Certificate chain — цепочка доверия:

```
server certificate→ intermediate CA→ root CA
```

Клиент проверяет всю цепочку.

---

### Root CA

Root CA — корневой центр сертификации.

Его сертификаты уже встроены в ОС/браузер.

Если root CA доверенный — можно доверять цепочке.

---

### Intermediate CA

Intermediate CA — промежуточный центр сертификации.

Он подписывает server certificates.

Нужен, чтобы не использовать root CA напрямую.

---

### Public key

Public key можно отдавать всем.

Он используется для проверки подписи и обмена ключами.

---

### Private key

Private key должен храниться секретно.

Если private key утек — сертификат скомпрометирован.

---

### CSR

CSR — Certificate Signing Request.

Это запрос на выпуск сертификата.

Содержит:

- домен;
- public key;
- данные организации;
- подпись private key.

---

### SAN

SAN — Subject Alternative Name.

Современный способ указать домены в сертификате.

Пример:

```
example.com
www.example.com
api.example.com
```

---

### CN

CN — Common Name.

Раньше использовался как основной домен сертификата.

Сейчас домены должны быть в SAN.

---

### SNI

SNI — Server Name Indication.

Клиент во время TLS handshake говорит, какой домен хочет открыть.

Нужно, чтобы один IP мог обслуживать много HTTPS-доменов.

---

### TLS termination

TLS termination — TLS заканчивается на load balancer/reverse proxy.

Пример:

```
Client HTTPS → Load Balancer → HTTP to backend
```

---

### TLS passthrough

TLS passthrough — load balancer не расшифровывает TLS.

Он просто прокидывает encrypted traffic до backend.

```
Client HTTPS → LB → Backend HTTPS
```

---

### mTLS basics

mTLS — mutual TLS.

Обычно сервер показывает сертификат клиенту.

В mTLS еще и клиент показывает сертификат серверу.

Используется для service-to-service auth.

---

### Self-signed certificate

Self-signed certificate — сертификат, подписанный самим собой.

Браузеры ему не доверяют по умолчанию.

Подходит для тестов, но не для публичного production.

---

### Let's Encrypt basics

Let's Encrypt — бесплатный CA для TLS-сертификатов.

Часто используется с автоматическим обновлением.

Инструменты:

```
certbotacme.sh
```

---

## 10. Linux Networking

### Интерфейсы

Network interface — сетевой интерфейс.

Примеры:

```
eth0ens33wlan0lodocker0
```

Проверка:

```
ip linkip addr
```

---

### Loopback

Loopback — локальный интерфейс самой машины.

```
127.0.0.1localhostlo
```

Если сервис слушает только `127.0.0.1`, извне он недоступен.

---

### Bridge

Bridge — L2-мост, как виртуальный switch.

Используется в:

- Docker;
- KVM;
- Kubernetes nodes.

Пример:

```
docker0
```

---

### Bonding basics

Bonding объединяет несколько сетевых интерфейсов в один.

Нужно для:

- отказоустойчивости;
- увеличения пропускной способности.

Пример:

```
eth0 + eth1 → bond0
```

---

### VLAN basics

VLAN делит одну физическую сеть на несколько логических L2-сетей.

Пример:

```
VLAN 10 — prod
VLAN 20 — dev
```

---

### Routing

Routing в Linux решает, куда отправить пакет.

Проверка:

```
ip route
```

Добавить маршрут:

```
ip route add 10.0.0.0/8 via 192.168.1.1
```

---

### ARP cache

ARP cache хранит соответствие:

```
IP → MAC
```

Проверка:

```
ip neigh
```

---

### Neighbor table

Neighbor table — современное название таблицы соседей.

Для IPv4 это ARP, для IPv6 это NDP.

```
ip neigh show
```

---

## 11. Проверка соединений

### Listening sockets

Listening socket — сервис слушает порт и готов принимать соединения.

Проверка:

```
ss -tulpn
```

---

### Established connections

Established connection — соединение уже установлено.

Проверка:

```
ss -tan state established
```

---

### TCP sockets

TCP sockets — сокеты TCP-соединений.

Проверка:

```
ss -tan
```

---

### UDP sockets

UDP sockets — UDP-порты.

Проверка:

```
ss -uanss -uulpn
```

---

## 12. Firewall и NAT

### Stateful firewall

Stateful firewall помнит состояние соединений.

Например, если клиент сделал исходящий запрос, ответ обратно разрешается автоматически.

---

### Stateless firewall

Stateless firewall не помнит состояние соединений.

Он смотрит каждый пакет отдельно.

Нужно явно разрешать и входящий, и обратный трафик.

---

### SNAT

SNAT меняет source IP.

Пример:

```
10.0.0.5 → public IP
```

Используется для выхода из private network в интернет.

---

### DNAT

DNAT меняет destination IP.

Пример:

```
public IP:80 → 10.0.0.5:80
```

Используется для port forwarding.

---

### Masquerade

Masquerade — разновидность SNAT, когда внешний IP может меняться.

Часто используется на домашних роутерах, Docker, Linux NAT.

---

### Port forwarding

Port forwarding перенаправляет порт с одного адреса на другой.

Пример:

```
1.2.3.4:8080 → 10.0.0.5:80
```

---

### Connection tracking

Connection tracking отслеживает состояния соединений.

В Linux это `conntrack`.

Проверка:

```
conntrack -L
```

Используется firewall/NAT для stateful-логики.

---

## 13. Load Balancing

### L4 Load Balancer

L4 балансирует на уровне TCP/UDP.

Он смотрит на:

- IP;
- port;
- protocol.

Не понимает HTTP path/header.

---

### L7 Load Balancer

L7 балансирует на уровне приложения.

Например HTTP.

Может смотреть на:

- Host header;
- path;
- headers;
- cookies.

---

### Reverse Proxy

Reverse proxy стоит перед backend-сервисами.

Пример:

```
Client → Nginx → App
```

Используется для TLS, routing, caching, auth, balancing.

---

### Forward Proxy

Forward proxy стоит со стороны клиента.

Пример:

```
Client → Proxy → Internet
```

Используется для corporate access, filtering, anonymity.

---

### Round Robin

Round Robin — запросы идут по очереди на backend'ы.

```
backend1 → backend2 → backend3 → backend1
```

---

### Least Connections

Least Connections отправляет новый запрос на backend с меньшим числом активных соединений.

Полезно, когда запросы разной длительности.

---

### IP Hash

IP Hash выбирает backend по IP клиента.

Один и тот же клиент часто попадает на один и тот же backend.

---

### Sticky Sessions

Sticky sessions закрепляют клиента за одним backend.

Часто через cookie.

Минус: сложнее равномерно балансировать.

---

### Health Checks

Health checks проверяют, жив ли backend.

Если backend unhealthy, LB перестает слать туда трафик.

---

### Active Health Checks

Active health checks — LB сам регулярно проверяет backend.

Пример:

```
GET /health
```

---

### Passive Health Checks

Passive health checks — LB делает вывод по реальным ошибкам.

Например backend часто возвращает `500` или timeout.

---

### Connection Draining

Connection draining — graceful вывод backend из балансировки.

Новые запросы туда не идут, старые соединения завершаются спокойно.

---

## 14. Packet Capture

### Packet capture

Packet capture — захват сетевых пакетов.

Главный инструмент:

```
tcpdump
```

Пример:

```
tcpdump -i eth0
```

---

### Packet filtering

Packet filtering — фильтрация захваченных пакетов.

Примеры:

```
tcpdump -i eth0 port 443
tcpdump -i eth0 host 1.1.1.1
tcpdump -i eth0 tcp
```

---

### PCAP

PCAP — файл с сохраненными пакетами.

Запись:

```
tcpdump -i eth0 -w dump.pcap
```

Открыть можно в Wireshark.

---

## 15. Docker Networking

### bridge network

Bridge network — сеть Docker по умолчанию.

Контейнеры получают private IP и ходят наружу через NAT.

---

### host network

Host network — контейнер использует сеть хоста напрямую.

Нет отдельного network namespace.

```
docker run --network host nginx
```

---

### none network

None network — контейнер без сети.

Есть только loopback.

```
docker run --network none alpine
```

---

### overlay network

Overlay network соединяет контейнеры на разных Docker hosts.

Используется в Docker Swarm.

---

### docker0

`docker0` — bridge-интерфейс Docker на хосте.

Обычно выглядит как:

```
172.17.0.1
```

---

### Port publishing

Port publishing пробрасывает порт хоста в контейнер.

Пример:

```
docker run -p 8080:80 nginx
```

Значит:

```
host:8080 → container:80
```

---

### Container DNS

Docker дает контейнерам DNS.

В user-defined bridge контейнеры могут резолвить друг друга по имени.

Пример:

```
curl http://backend:8080
```

---

### NAT в Docker

Docker использует NAT, чтобы контейнеры могли ходить наружу.

Также NAT используется для `-p`.

```
host port → container IP:port
```

---

## 16. Kubernetes Networking

### Pod IP

Pod IP — IP адрес Pod'а.

Каждый Pod получает свой IP.

В Kubernetes обычно:

```
Pod-to-Pod communication без NAT
```

---

### Node IP

Node IP — IP адрес Kubernetes-ноды.

На ноде работают pods, kubelet, kube-proxy.

---

### ClusterIP

ClusterIP — внутренний IP Service.

Доступен только внутри кластера.

```
my-service.default.svc.cluster.local
```

---

### NodePort

NodePort открывает порт на каждой ноде.

Пример:

```
NodeIP:30080 → Service → Pods
```

---

### LoadBalancer

LoadBalancer создает внешний cloud load balancer.

Типично в AWS/GCP/Azure.

```
External LB → Service → Pods
```

---

### Headless Service

Headless Service без ClusterIP.

```
clusterIP: None
```

DNS возвращает IP конкретных Pod'ов.

Полезно для StatefulSet.

---

### Endpoints

Endpoints — список Pod IP, куда Service может слать трафик.

Service сам по себе не хранит backend'ы, он использует Endpoints.

---

### EndpointSlice

EndpointSlice — более современная и масштабируемая версия Endpoints.

Используется для больших кластеров.

---

### kube-proxy

`kube-proxy` настраивает правила, чтобы Service IP работали.

Может использовать:

- iptables;
- IPVS;
- nftables в новых реализациях.

---

### iptables mode

В iptables mode kube-proxy создает iptables-правила для Service routing.

Трафик на ClusterIP перенаправляется на Pod IP.

---

### IPVS mode

IPVS mode использует Linux IPVS load balancing.

Обычно лучше масштабируется, чем iptables mode.

---

### CNI

CNI — Container Network Interface.

Плагин, который настраивает сеть Pod'ов.

Примеры:

- Calico
- Cilium
- Flannel

---

### Calico

Calico — CNI-плагин.

Часто используется для:

- Pod networking;
- NetworkPolicy;
- BGP routing.

---

### Cilium

Cilium — CNI на базе eBPF.

Может делать:

- networking;
- NetworkPolicy;
- observability;
- service mesh-like функции.

---

### Flannel

Flannel — простой CNI для Pod networking.

Часто используется для базовых кластеров.

---

### CoreDNS

CoreDNS — DNS внутри Kubernetes.

Резолвит Service names:

```
service.namespace.svc.cluster.local
```

---

### Ingress

Ingress — объект Kubernetes для HTTP/HTTPS routing внутрь кластера.

Пример:

```
example.com/api → api-serviceexample.com/web → web-service
```

---

### Ingress Controller

Ingress Controller — реальный компонент, который исполняет Ingress.

Примеры:

- nginx ingress controller;
- Traefik;
- HAProxy;
- AWS ALB Controller.

---

### NetworkPolicy

NetworkPolicy — firewall rules для Pod'ов.

Можно ограничить:

- кто может ходить к Pod;
- куда Pod может ходить.

---

### externalTrafficPolicy

`externalTrafficPolicy` управляет тем, как Service принимает внешний трафик.

```
externalTrafficPolicy: Cluster
```

может отправить трафик на Pod на другой ноде.

```
externalTrafficPolicy: Local
```

сохраняет real client IP, но шлет только на локальные Pod'ы.

---

## 17. Cloud Networking

### VPC

VPC — изолированная виртуальная сеть в cloud.

Пример:

```
10.0.0.0/16
```

---

### Public subnet

Public subnet — подсеть с маршрутом в Internet Gateway.

Инстансы могут быть доступны из интернета, если есть public IP и firewall разрешает.

---

### Private subnet

Private subnet — подсеть без прямого входа из интернета.

Для выхода наружу обычно используется NAT Gateway.

---

### Route table

Route table определяет маршруты в subnet.

Пример:

```
0.0.0.0/0 → Internet Gateway10.0.0.0/16 → local
```

---

### Internet Gateway

Internet Gateway дает VPC доступ в интернет.

Нужен для public subnet.

---

### NAT Gateway

NAT Gateway позволяет private instances выходить в интернет.

Но интернет не может напрямую зайти к ним обратно.

---

### Security Group

Security Group — stateful firewall на уровне instance/ENI.

Если разрешил входящий трафик, обратный ответ разрешается автоматически.

---

### Network ACL

Network ACL — stateless firewall на уровне subnet.

Нужно явно разрешать inbound и outbound.

---

### VPN basics

VPN соединяет сети через зашифрованный туннель.

Пример:

```
office network ↔ cloud VPC
```

---

### VPC Peering

VPC Peering соединяет две VPC напрямую.

Обычно без transitive routing.

То есть:

```
A ↔ BB ↔ C
```

не значит:

```
A ↔ C
```

---

### Transit Gateway basics

Transit Gateway — центральный hub для соединения многих VPC/VPN.

Упрощает large-scale cloud networking.

---

### PrivateLink basics

PrivateLink дает private-доступ к сервису без выхода в публичный интернет.

Часто используется для managed services.

---

### Cloud DNS

Cloud DNS — DNS-сервис облака.

Примеры:

- Route 53
- Cloud DNS
- Azure DNS

Используется для public/private DNS zones.

---

## 18. Performance

### Latency

Latency — задержка между запросом и ответом.

Измеряется в ms.

---

### RTT

RTT — Round Trip Time.

Время туда и обратно.

`ping` показывает RTT.

---

### Throughput

Throughput — реальная скорость передачи данных.

Например:

```
100 Mbps реально передается
```

---

### Bandwidth

Bandwidth — максимальная теоретическая пропускная способность канала.

Например:

```
1 Gbps link
```

---

### Packet loss

Packet loss — потеря пакетов.

Симптомы:

- retransmissions;
- лаги;
- плохой throughput;
- обрывы соединений.

---

### Jitter

Jitter — изменение задержки во времени.

Особенно важно для:

- VoIP;
- video calls;
- gaming;
- streaming.

---

### TCP backlog

TCP backlog — очередь входящих соединений.

Если backlog переполнен, клиенты могут получать timeout/refused.

---

### Socket buffers

Socket buffers — буферы приема/отправки в ОС.

Если маленькие — может страдать throughput.

Если забиты — приложение/сеть не успевает обрабатывать данные.

---

### Interface saturation

Interface saturation — сетевой интерфейс уперся в лимит.

Пример:

```
1 Gbps интерфейс загружен почти на 100%
```

Симптомы:

- packet drops;
- latency;
- retransmissions.

---

## 19. Troubleshooting

### DNS doesn't resolve

Домен не резолвится.

Проверить:

```
dig example.com
resolvectl status
cat /etc/resolv.conf
dig @8.8.8.8 example.com
```

Если по `8.8.8.8` работает, проблема в локальном resolver.

---

### Connection refused

`Connection refused` значит: хост доступен, но порт закрыт или сервис не слушает.

Проверить:

```
ss -tulpn
nc -vz host port
```

---

### Connection timeout

`Connection timeout` значит: ответа нет.

Причины:

- firewall drop;
- routing issue;
- service недоступен;
- security group;
- packet loss.

---

### No route to host

`No route to host` значит, что система не знает, как дойти до адреса, или маршрут запрещен.

Проверить:

```
ip route
ping gateway
```

---

### Network unreachable

`Network unreachable` — нет маршрута до сети.

Часто проблема с:

- default route;
- interface down;
- неправильной сетью.

---

### TLS handshake failed

TLS handshake failed может быть из-за:

- неправильного сертификата;
- несовместимой TLS версии;
- неправильного SNI;
- expired cert;
- firewall/proxy.

Проверить:

```
openssl s_client -connect example.com:443 -servername example.com
```

---

### Wrong certificate

Wrong certificate — сервер отдал сертификат не для этого домена.

Частые причины:

- неправильный SNI;
- неправильный ingress;
- не тот virtual host;
- старый cert.

---

### 502 Bad Gateway

`502` значит proxy/LB не получил нормальный ответ от upstream.

Причины:

- backend упал;
- backend вернул кривой ответ;
- connection refused;
- protocol mismatch.

---

### 503 Service Unavailable

`503` значит сервис временно недоступен.

Причины:

- нет healthy backends;
- приложение перегружено;
- maintenance;
- readiness probe failed.

---

### 504 Gateway Timeout

`504` значит proxy/LB ждал backend, но не дождался.

Причины:

- backend долго отвечает;
- network timeout;
- DB/API зависли;
- слишком маленький timeout на proxy.

---

### Service listens only on localhost

Сервис слушает только:

```
127.0.0.1
```

Тогда извне он недоступен.

Проверить:

```
ss -tulpn
```

Нужно слушать:

```
0.0.0.0
```

или конкретный внешний IP.

---

### Firewall blocks traffic

Firewall может блокировать входящий/исходящий трафик.

Проверить:

```
iptables -L -n -vnft list rulesetufw status
```

---

### Wrong routing

Wrong routing — пакет уходит не туда.

Проверить:

```
ip route get 8.8.8.8ip routetraceroute host
```

---

### MTU issues

MTU issues часто проявляются так:

- маленькие запросы работают;
- большие зависают;
- VPN странно ломается;
- TLS может зависать.

Проверка:

```
ping -M do -s 1472 host
```

---

### Packet loss

Packet loss — пакеты теряются.

Проверить:

```
ping hostmtr hosttcpdump
```

Симптомы:

- retransmissions;
- низкая скорость;
- нестабильные соединения.

---

### High latency

High latency — большая задержка.

Проверить:

```
pingmtrtraceroute
```

Причины:

- география;
- перегрузка сети;
- плохой routing;
- saturation.

---

### TIME_WAIT leak

Много `TIME_WAIT` обычно нормально, если много коротких TCP-соединений.

Проблема, если заканчиваются ephemeral ports.

Проверить:

```
ss -tan state time-wait | wc -l
```

---

### CLOSE_WAIT leak

Много `CLOSE_WAIT` — часто баг приложения.

Удаленная сторона закрыла соединение, а локальное приложение не закрыло socket.

Проверить:

```
ss -tan state close-wait
```

---

### Ephemeral port exhaustion

Ephemeral port exhaustion — закончились временные клиентские порты.

Симптомы:

- cannot assign requested address;
- connection failures;
- много TIME_WAIT.

Проверить:

```
cat /proc/sys/net/ipv4/ip_local_port_rangess -tan | wc -l
```

---

## 20. Interview Questions

### Что происходит после ввода `https://example.com` в браузере?

Кратко:

1. браузер проверяет cache;
2. делает DNS lookup;
3. получает IP;
4. открывает TCP connection;
5. делает TLS handshake;
6. отправляет HTTP request;
7. сервер возвращает HTTP response;
8. браузер рендерит страницу.

---

### TCP vs UDP

TCP:

- connection-oriented;
- reliable;
- ordered;
- есть retransmission;
- медленнее, но надежнее.

UDP:

- connectionless;
- без гарантии доставки;
- быстрее;
- приложение само решает надежность.

---

### Почему `ping` работает, а `curl` — нет?

Потому что `ping` использует ICMP, а `curl` использует TCP/HTTP/HTTPS.

Возможные причины:

- порт закрыт;
- сервис не слушает;
- firewall блокирует TCP;
- TLS/HTTP проблема;
- proxy проблема.

---

### Почему `curl` по IP работает, а по домену — нет?

Частые причины:

- DNS не работает;
- домен резолвится не туда;
- неправильный `/etc/hosts`;
- проблема с SNI/Host header;
- сертификат не подходит.

---

### Почему возникает `TIME_WAIT`?

`TIME_WAIT` появляется после закрытия TCP-соединения.

Нужен, чтобы старые пакеты не попали в новое соединение.

---

### Чем `TIME_WAIT` отличается от `CLOSE_WAIT`?

`TIME_WAIT` — нормальное состояние после закрытия соединения.

`CLOSE_WAIT` — приложение не закрыло socket после FIN от другой стороны.

Много `CLOSE_WAIT` чаще указывает на баг приложения.

---

### Что означает `Connection refused`?

Хост ответил, но порт закрыт.

То есть:

```
network reachable, service not listening
```

---

### Что означает `Connection timeout`?

Ответа нет.

Часто:

- firewall drop;
- route issue;
- host down;
- security group;
- packet loss.

---

### Как проверить, слушает ли сервис порт?

```
ss -tulpn
```

Пример:

```
ss -tulpn | grep  nginx
```

---

### Как проверить доступность порта?

```
nc -vz host port
```

или:

```
telnet host port
```

Для HTTP:

```
curl -v http://host:port
```

---

### Как проверить TLS-сертификат?

```
openssl s_client -connect example.com:443 -servername example.com
```

Или кратко:

```
curl -v https://example.com
```

---

### Что такое SNI?

SNI — поле в TLS handshake, где клиент говорит домен.

Нужно, чтобы один IP мог обслуживать много HTTPS-сайтов.

---

### Что такое NAT?

NAT меняет IP-адреса в пакетах.

Чаще всего:

```
private IP → public IP
```

---

### Чем SNAT отличается от DNAT?

SNAT меняет source IP.

```
10.0.0.5 → public IP
```

DNAT меняет destination IP.

```
public IP:80 → 10.0.0.5:80
```

---

### Что такое CIDR?

CIDR — запись сети через `/prefix`.

Пример:

```
192.168.1.0/24
```

`/24` значит первые 24 бита — сеть.

---

### Что такое MTU?

MTU — максимальный размер пакета без фрагментации.

Обычно Ethernet:

```
1500 bytes
```

---

### Как работает Kubernetes Service?

Service дает стабильный адрес для группы Pod'ов.

```
Client → Service → Pod
```

Service выбирает Pod'ы по labels.

---

### Как работает Ingress?

Ingress описывает HTTP/HTTPS routing.

```
example.com/api → api-serviceexample.com/web → web-service
```

Но реально работает через Ingress Controller.

---

### Что делает kube-proxy?

`kube-proxy` настраивает правила, чтобы Kubernetes Service направлял трафик на Pod'ы.

Работает через:

- iptables;
- IPVS.

---

### Что делает CNI?

CNI настраивает сеть Pod'ов.

Он отвечает за:

- выдачу Pod IP;
- маршрутизацию Pod-to-Pod;
- сетевые политики, если поддерживает.

---

### Как дебажить сетевую проблему в Kubernetes?

Базовый порядок:

```
kubectl get pod -o widekubectl get svckubectl get endpointskubectl get endpointslicekubectl describe podkubectl logs
```

Проверить DNS:

```
nslookup service.namespace.svc.cluster.local
```

Проверить доступность:

```
kubectl exec -it pod -- curl service:port
```

Проверить NetworkPolicy, kube-proxy, CNI и firewall на нодах