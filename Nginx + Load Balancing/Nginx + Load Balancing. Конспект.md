# Nginx / Load Balancing

## 1. Основы Nginx

### Что такое Nginx

Nginx — высокопроизводительный web server, reverse proxy и load balancer.

Он часто используется для:

- раздачи статических файлов;
    
- проксирования запросов к приложению;
    
- TLS termination;
    
- балансировки между backend-серверами;
    
- rate limiting и caching.
    

---

### Web Server

Как web server Nginx принимает HTTP-запросы и отдает файлы клиенту.

```text
Client -> Nginx -> /var/www/html/index.html
```

Пример:

```nginx
server {
    listen 80;
    root /var/www/html;
}
```

---

### Reverse Proxy

Reverse proxy принимает запрос клиента и передает его внутреннему backend.

```text
Client -> Nginx -> Application
```

Клиент обычно не знает адрес реального backend-сервера.

---

### Load Balancer

Как load balancer Nginx распределяет запросы между несколькими backend-серверами.

```text
              -> backend-1
Client -> Nginx -> backend-2
              -> backend-3
```

---

### Event-driven architecture
 
Nginx использует event-driven архитектуру.

Один worker может обслуживать множество соединений без создания отдельного процесса или thread на каждое соединение.

Это позволяет эффективно работать с большим количеством клиентов.

---

### Worker Processes

Worker processes обрабатывают реальные клиентские соединения и запросы.

Обычно используют:

```nginx
worker_processes auto;
```

Тогда Nginx выбирает количество workers примерно по количеству CPU cores.

---

### Worker Connections

`worker_connections` задает максимальное число соединений на один worker.

```nginx
events {
    worker_connections 1024;
}
```

Это не всегда означает 1024 клиентов: reverse proxy использует отдельные соединения к клиенту и backend.

---

### Master Process

Master process:

- читает конфигурацию;
    
- запускает workers;
    
- выполняет reload;
    
- управляет graceful shutdown;
    
- работает с privileged ports.
    

Запросы обрабатывают worker processes, а не master.

---

## 2. Конфигурация Nginx

### `/etc/nginx/nginx.conf`

`/etc/nginx/nginx.conf` — основной конфигурационный файл Nginx.

Обычно он включает дополнительные конфиги:

```nginx
include /etc/nginx/conf.d/*.conf;
```

В Debian/Ubuntu также часто используются:

```text
/etc/nginx/sites-available/
/etc/nginx/sites-enabled/
```

---

### `http`

Блок `http` содержит общие настройки HTTP-сервера.

```nginx
http {
    access_log /var/log/nginx/access.log;

    server {
        listen 80;
    }
}
```

Внутри могут быть `server`, `upstream`, log formats, timeouts и caching.

---

### `server`

`server` описывает virtual host.

```nginx
server {
    listen 80;
    server_name app.example.com;
}
```

На одном Nginx можно настроить много `server` блоков.

---

### `location`

`location` определяет обработку конкретного URI.

```nginx
location /api/ {
    proxy_pass http://backend;
}
```

Разные paths могут обслуживаться разными способами.

---

### `listen`

`listen` указывает IP и порт, на которых Nginx принимает соединения.

```nginx
listen 80;
listen 443 ssl;
```

Можно указать конкретный IP:

```nginx
listen 192.168.1.10:80;
```

---

### `server_name`

`server_name` определяет доменные имена virtual host.

```nginx
server_name app.example.com www.example.com;
```

Nginx сравнивает значение HTTP-заголовка `Host` с `server_name`.

---

### `root`

`root` задает директорию со статическими файлами.

```nginx
root /var/www/example;
```

Для запроса `/images/logo.png` Nginx будет искать:

```text
/var/www/example/images/logo.png
```

---

### `index`

`index` задает файл, который отдается при запросе директории.

```nginx
index index.html index.htm;
```

Для запроса `/` Nginx попробует найти `index.html`.

---

### `include`

`include` подключает другие конфигурационные файлы.

```nginx
include /etc/nginx/mime.types;
include /etc/nginx/conf.d/*.conf;
```

Помогает разделять большой config на несколько файлов.

---

### Проверка конфигурации через `nginx -t`

Перед reload нужно проверить конфигурацию:

```bash
nginx -t
```

Команда проверяет:

- синтаксис;
    
- существование файлов;
    
- сертификаты;
    
- корректность директив.
    

---

### Перезагрузка через `nginx -s reload`

Перезагрузить конфигурацию без остановки сервиса:

```bash
nginx -s reload
```

При systemd чаще используют:

```bash
systemctl reload nginx
```

Старые workers завершают текущие запросы, а новые начинают работать с новым config.

---

## 3. Обработка запросов

### Virtual Hosts

Virtual Hosts позволяют обслуживать несколько сайтов на одном Nginx.

```nginx
server {
    server_name app.example.com;
}

server {
    server_name api.example.com;
}
```

Выбор происходит по IP, порту и `Host` header.

---

### Выбор `server` блока

Сначала Nginx выбирает `listen` по IP и порту.

Затем сравнивает `Host` с `server_name`.

Если совпадение не найдено, используется default server для этого порта.

```nginx
listen 80 default_server;
```

---

### Location matching

После выбора `server` Nginx выбирает подходящий `location`.

Основные типы:

- exact;
    
- prefix;
    
- regex;
    
- named location.
    

Порядок matching важен и может влиять на результат.

---

### Exact match

Exact match задается через `=`.

```nginx
location = /health {
    return 200 "OK";
}
```

Он срабатывает только для точного URI `/health`.

---

### Prefix match

Обычный `location` использует prefix matching.

```nginx
location /api/ {
    proxy_pass http://backend;
}
```

Подойдет для `/api/users`, `/api/orders` и других URI с этим prefix.

---

### Regex match

Regex location задается через `~` или `~*`.

```nginx
location ~ \.php$ {
    proxy_pass http://php_backend;
}
```

`~` — чувствителен к регистру.

`~*` — не чувствителен к регистру.

---

### URI rewriting

URI rewriting изменяет URI перед дальнейшей обработкой.

Например:

```text
/old/page -> /new/page
```

Можно использовать `rewrite` или внутреннее перенаправление через `location`.

---

### Redirect

Redirect говорит клиенту выполнить новый запрос по другому URL.

Пример:

```nginx
return 301 https://$host$request_uri;
```

Клиент получает HTTP 301 и обращается по новому адресу.

---

### `return`

`return` быстро возвращает status code или redirect.

```nginx
return 404;
```

```nginx
return 301 https://$host$request_uri;
```

Для простого redirect обычно лучше `return`, чем `rewrite`.

---

### `rewrite`

`rewrite` изменяет URI через regex.

```nginx
rewrite ^/old/(.*)$ /new/$1 permanent;
```

Использовать нужно аккуратно: сложные rewrite rules могут создавать циклы и путаницу.

---

### Query parameters

Query parameters находятся после `?`.

```text
/search?q=nginx&page=2
```

Полный набор доступен через:

```nginx
$args
```

Отдельный параметр:

```nginx
$arg_q
```

---

## 4. Reverse Proxy

### Что такое Reverse Proxy


Reverse proxy стоит перед backend-серверами и принимает запросы клиентов.

Он может:

- скрывать backend;
    
- завершать TLS;
    
- балансировать запросы;
    
- добавлять headers;
    
- кешировать ответы;
    
- ограничивать traffic.
    

---

### Forward Proxy vs Reverse Proxy

Forward proxy работает от имени клиента.

```text
Client -> Forward Proxy -> Internet
```

Reverse proxy работает от имени серверов.

```text
Client -> Reverse Proxy -> Backend
```

---

### `proxy_pass`

`proxy_pass` задает backend, куда отправить запрос.

```nginx
location /api/ {
    proxy_pass http://127.0.0.1:8080;
}
```

Можно проксировать в upstream group:

```nginx
proxy_pass http://backend;
```

---

### `proxy_set_header`

`proxy_set_header` меняет или добавляет HTTP headers для backend.

```nginx
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
```

Без этого backend может видеть неправильный host или IP.

---

### `Host`

`Host` содержит доменное имя исходного запроса.

Обычно передают:

```nginx
proxy_set_header Host $host;
```

Это важно для virtual hosts и генерации правильных URLs на backend.

---

### `X-Real-IP`

`X-Real-IP` передает backend-сервису IP непосредственного клиента.

```nginx
proxy_set_header X-Real-IP $remote_addr;
```

Иначе backend часто увидит IP Nginx.

---

### `X-Forwarded-For`

`X-Forwarded-For` содержит цепочку IP-адресов proxy.

```nginx
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
```

Пример:

```text
client-ip, proxy-1-ip, proxy-2-ip
```

---

### `X-Forwarded-Proto`

`X-Forwarded-Proto` показывает исходный протокол клиента.

```nginx
proxy_set_header X-Forwarded-Proto $scheme;
```

Значение обычно `http` или `https`.

Полезно, если TLS завершается на Nginx, а backend получает HTTP.

---

### Proxy timeouts

Основные proxy timeouts:

```nginx
proxy_connect_timeout 5s;
proxy_send_timeout 30s;
proxy_read_timeout 30s;
```

- `proxy_connect_timeout` — подключение к backend;
    
- `proxy_send_timeout` — отправка запроса;
    
- `proxy_read_timeout` — ожидание данных от backend.
    

---

### Proxy buffering

Proxy buffering позволяет Nginx сначала принять ответ backend в буфер, а затем отдавать клиенту.

```nginx
proxy_buffering on;
```

Это снижает время, в течение которого медленный клиент держит backend-соединение.

Для streaming иногда buffering отключают:

```nginx
proxy_buffering off;
```

---

### WebSocket proxying

Для WebSocket нужно передать upgrade headers.

```nginx
location /ws/ {
    proxy_pass http://backend;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
}
```

Без этого соединение может не перейти в WebSocket mode.

---

## 5. Основы Load Balancing

### Что такое Load Balancer

Load Balancer распределяет traffic между несколькими backend-серверами.

Он также может:

- проверять здоровье backend;
    
- выполнять failover;
    
- завершать TLS;
    
- скрывать внутреннюю архитектуру.
    

---

### Зачем нужен Load Balancing

Load balancing нужен для:


- масштабирования;
    
- high availability;
    
- распределения нагрузки;
    
- maintenance без полного downtime;
    
- отказоустойчивости.
    

---

### Client-side vs Server-side Load Balancing

Client-side load balancing — клиент сам выбирает backend.

```text
Client -> service discovery -> backend
```

Server-side load balancing — клиент обращается к load balancer.

```text
Client -> Load Balancer -> backend
```

Nginx — server-side load balancer.

---

### Hardware vs Software Load Balancer

Hardware Load Balancer — специализированное устройство.

Пример: F5.

Software Load Balancer — программа на обычном сервере или VM.

Примеры:

- Nginx;
    
- HAProxy;
    
- Envoy;
    
- Traefik.
    

---

### Internal vs External Load Balancer

External Load Balancer принимает traffic из внешней сети или интернета.

Internal Load Balancer работает только внутри private network.

Например, internal LB может балансировать traffic между внутренними сервисами.

---

### Layer 4 Load Balancing

Layer 4 балансирует TCP/UDP-соединения.

Он смотрит в основном на:

- IP;
    
- port;
    
- protocol.
    

Не анализирует HTTP path или headers.

---

### Layer 7 Load Balancing

Layer 7 понимает HTTP/HTTPS.

Может маршрутизировать по:

- host;
    
- path;
    
- headers;
    
- cookies;
    
- HTTP method.
    

Nginx чаще используется как L7 load balancer.

---

### Stateless vs Stateful applications

Stateless application не хранит пользовательскую сессию локально на конкретном backend.

Любой запрос можно отправить на любую реплику.

Stateful application хранит состояние локально, поэтому может требовать sticky sessions.

Для масштабирования лучше делать приложение stateless.

---

## 6. Nginx Upstream

### `upstream`

`upstream` описывает группу backend-серверов.

```nginx
upstream backend {
    server 10.0.0.10:8080;
    server 10.0.0.11:8080;
}
```

Использование:

```nginx
proxy_pass http://backend;
```

---

### Backend servers

Backend servers — серверы приложения за Nginx.

Это могут быть:

- VM;
    
- containers;
    
- Kubernetes Services;
    
- application processes;
    
- Unix sockets.
    

---

### `server`

Внутри `upstream` директива `server` добавляет backend.

```nginx
upstream backend {
    server app1:8080;
    server app2:8080;
}
```

---

### `weight`

`weight` задает относительный вес backend.

```nginx
upstream backend {
    server app1:8080 weight=3;
    server app2:8080 weight=1;
}
```

Примерно 75% запросов пойдет на `app1`.

---

### `backup`

`backup` backend используется, когда основные backend недоступны.

```nginx
upstream backend {
    server app1:8080;
    server app2:8080 backup;
}
```

Это простой failover-механизм.

---

### `down`

`down` временно исключает backend из балансировки.

```nginx
server app2:8080 down;
```

Полезно при maintenance или диагностике.

---

### `max_fails`

`max_fails` задает число неудачных попыток, после которых backend временно считается недоступным.

```nginx
server app1:8080 max_fails=3 fail_timeout=30s;
```

---

### `fail_timeout`

`fail_timeout` определяет:

- период подсчета ошибок;
    
- время временного исключения backend.
    

```nginx
server app1:8080 max_fails=3 fail_timeout=30s;
```

---

### Connection reuse

Connection reuse позволяет повторно использовать TCP-соединения к backend.

Это уменьшает:

- TCP handshakes;
    
- latency;
    
- нагрузку на backend.
    

Для этого используют upstream keepalive.

---

### Keepalive connections

Пример upstream keepalive:

```nginx
upstream backend {
    server app1:8080;
    server app2:8080;
    keepalive 32;
}
```

Для HTTP backend обычно также нужно:

```nginx
proxy_http_version 1.1;
proxy_set_header Connection "";
```

---

## 7. Алгоритмы балансировки

### Round Robin

Round Robin — алгоритм по умолчанию.

Запросы распределяются по очереди:

```text
request 1 -> backend-1
request 2 -> backend-2
request 3 -> backend-3
```

Хорошо работает, если backend примерно одинаковые.

---

### Weighted Round Robin

Weighted Round Robin учитывает веса.

```nginx
upstream backend {
    server app1:8080 weight=3;
    server app2:8080 weight=1;
}
```

Используется, когда backend имеют разную мощность.

---

### Least Connections

Least Connections отправляет новый запрос на backend с наименьшим количеством активных соединений.

Подходит, если запросы имеют разную длительность.

---

### `least_conn`

В Nginx включается так:

```nginx
upstream backend {
    least_conn;

    server app1:8080;
    server app2:8080;
}
```

---

### IP Hash

IP Hash выбирает backend на основе IP клиента.

Один клиент обычно попадает на один backend.

Это простая форма session affinity.

---

### `ip_hash`

Включается так:

```nginx
upstream backend {
    ip_hash;

    server app1:8080;
    server app2:8080;
}
```

Плохо работает, если много пользователей приходят через один NAT/proxy.

---

### Hash-based balancing

Hash-based balancing выбирает backend по значению ключа.

Например:

```nginx
hash $request_uri;
```

Можно балансировать по:

- URI;
    
- header;
    
- cookie;
    
- произвольной переменной.
    

---

### Consistent Hashing basics

Consistent Hashing уменьшает количество перераспределенных ключей при добавлении или удалении backend.

```nginx
hash $request_uri consistent;
```

Полезно для:

- caches;
    
- sharded data;
    
- affinity по ключу.
    

---

### Когда использовать каждый алгоритм

Round Robin — одинаковые backend и простые запросы.

Weighted Round Robin — backend разной мощности.

Least Connections — запросы имеют разную длительность.

IP Hash — нужна простая affinity по клиенту.

Consistent Hashing — важно стабильно выбирать backend по ключу.

---

## 8. Health Checks и отказоустойчивость

### Active Health Checks

Active Health Check — load balancer сам периодически отправляет проверочный запрос backend.

```text
Nginx -> GET /health -> backend
```

Расширенные active checks доступны в коммерческом NGINX Plus и некоторых других балансировщиках.

---

### Passive Health Checks

Passive Health Check использует ошибки обычных пользовательских запросов.

Если backend несколько раз не отвечает, Nginx временно исключает его.

Настраивается через `max_fails` и `fail_timeout`.

---

### Health Check endpoint

Health endpoint — легкий endpoint для проверки состояния приложения.

Например:

```text
/health
/ready
/status
```

Он должен отвечать быстро и не выполнять тяжелую бизнес-логику.

---

### Failover

Failover — переключение traffic с отказавшего backend на рабочий.

Пример:

```text
backend-1 failed -> traffic goes to backend-2
```

---

### Backend недоступен

Причины:

- процесс не запущен;
    
- неправильный порт;
    
- firewall;
    
- DNS;
    
- приложение зависло;
    
- backend перегружен;
    
- network route отсутствует.
    

Проверить:

```bash
curl -v http://backend:8080/health
nc -vz backend 8080
```

---

### Retry на другой backend

Nginx может повторить запрос на другом backend при определенных ошибках.

Но retry небезопасен для некоторых non-idempotent запросов, например оплаты через POST.

---

### `proxy_next_upstream`

Определяет, когда попробовать другой backend.

```nginx
proxy_next_upstream error timeout http_502 http_503 http_504;
```

Можно ограничить число попыток:

```nginx
proxy_next_upstream_tries 2;
```

---

### Graceful reload

Graceful reload применяет новый config без обрыва активных запросов.

```bash
nginx -t && systemctl reload nginx
```

Новые workers получают новый config, старые завершают текущие соединения.

---

### Single Point of Failure

Один Nginx instance — Single Point of Failure.

Если он упадет, недоступны все backend-сервисы за ним.

---

### Несколько Load Balancer экземпляров

Для отказоустойчивости используют несколько экземпляров Nginx.

Перед ними могут быть:

- cloud load balancer;
    
- DNS balancing;
    
- VRRP/Keepalived;
    
- anycast;
    
- Kubernetes Service.
    

```text
Client -> HA endpoint -> Nginx-1 / Nginx-2 -> backends
```

---

## 9. Sessions

### Sticky Sessions

Sticky Sessions закрепляют клиента за определенным backend.

Это может быть нужно, если session state хранится локально.

Но такой подход усложняет масштабирование и failover.

---

### Session Affinity

Session Affinity — общее название закрепления клиента за backend.

Она может быть:

- cookie-based;
    
- IP-based;
    
- hash-based.
    

---

### Cookie-based affinity

Load balancer записывает cookie с идентификатором backend.

При следующих запросах клиент отправляется на тот же сервер.

Это обычно точнее, чем IP affinity.

---

### IP-based affinity

Backend выбирается по IP клиента.

В Nginx:

```nginx
ip_hash;
```

Проблема: много клиентов за одним NAT могут попасть на один backend.

---

### Проблемы Sticky Sessions

Недостатки:

- неравномерная нагрузка;
    
- потеря сессии при падении backend;
    
- сложнее autoscaling;
    
- сложнее deployment;
    
- state привязан к серверу.
    

---

### Хранение сессий вне приложения

Лучше хранить session state во внешнем shared storage.

Например:

- Redis;
    
- database;
    
- distributed cache.
    

Тогда любой backend может обработать запрос клиента.

---

### Redis для хранения сессий

Redis часто используют как быстрое общее хранилище сессий.

```text
Backend-1 ┐
Backend-2 ├-> Redis
Backend-3 ┘
```

Это позволяет приложению оставаться stateless на уровне реплик.

---

## 10. TLS / HTTPS

### TLS termination

TLS termination означает, что Nginx принимает HTTPS и расшифровывает traffic.

```text
Client HTTPS -> Nginx -> HTTP/HTTPS -> Backend
```

Сертификат и private key находятся на Nginx.

---

### TLS passthrough

TLS passthrough передает зашифрованный traffic backend без расшифровки.

```text
Client TLS -> Load Balancer -> Backend TLS
```

Обычно выполняется на Layer 4.

---

### SSL certificate

Certificate подтверждает принадлежность домена и содержит public key.

Он выдается CA или может быть self-signed.

---

### Private key

Private key используется сервером для TLS.

Он должен храниться защищенно.

Обычно права:

```bash
chmod 600 /etc/nginx/tls/server.key
```

Private key нельзя публиковать или коммитить в Git.

---

### Certificate chain

Certificate chain включает:

- server certificate;
    
- intermediate CA certificates;
    
- иногда root CA.
    

Если chain неполный, некоторые клиенты получат TLS error.

---

### `ssl_certificate`

Указывает файл сертификата или full chain.

```nginx
ssl_certificate /etc/nginx/tls/fullchain.pem;
```

---

### `ssl_certificate_key`

Указывает private key.

```nginx
ssl_certificate_key /etc/nginx/tls/privkey.pem;
```

---

### HTTP → HTTPS redirect

Пример redirect:

```nginx
server {
    listen 80;
    server_name example.com;

    return 301 https://$host$request_uri;
}
```

---

### SNI

SNI позволяет клиенту передать имя домена во время TLS handshake.

Благодаря этому один IP может обслуживать несколько HTTPS-сайтов с разными сертификатами.

---

### TLS между Nginx и backend

Nginx может подключаться к backend по HTTPS:

```nginx
proxy_pass https://backend;
```

При необходимости настраивают проверку CA:

```nginx
proxy_ssl_verify on;
proxy_ssl_trusted_certificate /etc/nginx/ca.pem;
```

---

### Обновление сертификатов без остановки

После замены файлов сертификата выполняют graceful reload:

```bash
nginx -t && systemctl reload nginx
```

Активные соединения обычно продолжают работать, а новые используют новый сертификат.

---

## 11. Static Content и Caching

### Раздача статических файлов

Пример:

```nginx
server {
    root /var/www/app;

    location / {
        try_files $uri $uri/ =404;
    }
} 
```

Nginx эффективно раздает HTML, CSS, JS, images и другие файлы.

---

### Browser caching

Browser caching говорит браузеру хранить ресурс локально.

Это уменьшает:

- количество запросов;
    
- latency;
    
- нагрузку на сервер.
    

Управляется через `Expires` и `Cache-Control`.

---

### `expires`

Пример:

```nginx
location /static/ {
    expires 7d;
}
```

Nginx добавит заголовки для кеширования ресурса на 7 дней.

---

### `Cache-Control`

Пример:

```nginx
add_header Cache-Control "public, max-age=604800";
```

Для чувствительных данных можно запретить кеш:

```nginx
add_header Cache-Control "no-store";
```

---

### Proxy Cache

Proxy Cache кеширует ответы backend на стороне Nginx.

```text
Client -> Nginx cache -> Backend
```

Это может значительно снизить нагрузку на приложение.

---

### Cache key

Cache key определяет уникальность кешируемого ответа.

Обычно учитываются:

- scheme;
    
- host;
    
- URI;
    
- query parameters.
    

Плохой cache key может смешать ответы разных пользователей.

---

### Cache invalidation

Cache invalidation — удаление или обновление устаревших данных.

Подходы:

- TTL;
    
- purge;
    
- versioned URLs;
    
- cache bypass;
    
- смена cache key.
    

---

### Gzip compression

Gzip уменьшает размер текстовых ответов.

```nginx
gzip on;
gzip_types text/plain text/css application/json application/javascript;
```

Это снижает network traffic, но использует CPU.

---

### Brotli basics

Brotli — современный алгоритм сжатия.

Часто дает лучшее сжатие, чем gzip.

В Nginx обычно требует дополнительный module.

---

## 12. Rate Limiting и ограничения

### Rate Limiting

Rate Limiting ограничивает количество запросов от клиента.

Используется для:

- защиты API;
    
- защиты login endpoint;
    
- снижения нагрузки;
    
- частичной защиты от abuse.
    

---

### `limit_req`

Сначала создается зона:

```nginx
limit_req_zone $binary_remote_addr zone=api:10m rate=10r/s;
```

Затем применяется:

```nginx
location /api/ {
    limit_req zone=api;
}
```

---

### Burst

`burst` позволяет временный всплеск запросов.

```nginx
limit_req zone=api burst=20 nodelay;
```

Без burst превышающие rate запросы сразу ограничиваются или задерживаются.

---

### Connection limiting

Connection limiting ограничивает количество одновременных соединений.

Это полезно против клиентов, удерживающих много connections.

---

### `limit_conn`

Создание зоны:

```nginx
limit_conn_zone $binary_remote_addr zone=clients:10m;
```

Применение:

```nginx
limit_conn clients 20;
```

---

### Request body size

Размер body важен для uploads и API requests.

Слишком большой body может перегрузить disk, memory или backend.

---

### `client_max_body_size`

Ограничение размера:

```nginx
client_max_body_size 10m;
```

При превышении Nginx возвращает `413 Request Entity Too Large`.

---

### Защита backend от перегрузки

Основные механизмы:

- rate limiting;
    
- connection limits;
    
- timeouts;
    
- caching;
    
- buffering;
    
- request body limits;
    
- retries с ограничениями;
    
- circuit breaker на уровне приложения/proxy.
    

---

## 13. Логи

### Access Log

Access log содержит информацию о запросах.

Обычно:

```text
/var/log/nginx/access.log
```

Там можно увидеть URI, status, client IP и request time.

---

### Error Log

Error log содержит ошибки Nginx и upstream.

Обычно:

```text
/var/log/nginx/error.log
```

Уровни:

- debug;
    
- info;
    
- notice;
    
- warn;
    
- error;
    
- crit;
    
- alert;
    
- emerg.
    

---

### Log Format

Формат задается через `log_format`.

```nginx
log_format main '$remote_addr $request '
                '$status $request_time '
                '$upstream_addr $upstream_response_time';
```

Подключение:

```nginx
access_log /var/log/nginx/access.log main;
```

---

### `$request_time`

`$request_time` — полное время обработки запроса Nginx.

Оно включает:

- чтение запроса;
    
- ожидание backend;
    
- отправку ответа клиенту.
    

---

### `$upstream_response_time`

`$upstream_response_time` — время ответа upstream.

Если оно большое, проблема часто в backend.

Если `$request_time` большой, а upstream быстрый, возможно медленный клиент или buffering.

---

### `$upstream_addr`

`$upstream_addr` показывает backend, который обработал запрос.

Пример:

```text
10.0.0.11:8080
```

Полезно для анализа балансировки.

---

### `$status`

`$status` — HTTP status, который Nginx вернул клиенту.

Например:

```text
200
404
502
504
```

---

### `$upstream_status`

`$upstream_status` — status от backend.

Если были retries, может содержать несколько значений:

```text
502, 200
```

Это означает: первый backend вернул 502, второй — 200.

---

### Поиск медленных запросов

Можно сортировать access log по `$request_time`.

Пример через `awk`, если время находится в последнем поле:

```bash
awk '$NF > 1 {print}' /var/log/nginx/access.log
```

Для нормального анализа лучше использовать structured JSON log format.

---

### Ротация логов

Nginx logs обычно ротируются через `logrotate`.

После переименования файла Nginx нужно переоткрыть log files:

```bash
nginx -s reopen
```

---

## 14. Производительность

### `worker_processes`

Обычно:

```nginx
worker_processes auto;
```

Это позволяет использовать доступные CPU cores.

Слишком много workers может увеличить context switching.

---

### `worker_connections`

Пример:

```nginx
events {
    worker_connections 4096;
}
```

Это лимит соединений на worker.

Он также ограничивается file descriptors.

---

### Максимальное количество соединений

Упрощенная оценка:

```text
worker_processes × worker_connections
```

Но для reverse proxy одно клиентское соединение может требовать второе соединение к backend.

Также действуют OS limits.

---

### Keepalive

Keepalive позволяет использовать одно TCP-соединение для нескольких HTTP-запросов.

Плюсы:

- меньше handshakes;
    
- ниже latency;
    
- меньше CPU.
    

Но слишком длинный keepalive держит соединения и file descriptors.

---

### Timeouts

Основные timeouts:

- `client_header_timeout`;
    
- `client_body_timeout`;
    
- `keepalive_timeout`;
    
- `proxy_connect_timeout`;
    
- `proxy_read_timeout`;
    
- `send_timeout`.
    

Слишком большие timeouts могут удерживать ресурсы, слишком маленькие — рвать нормальные запросы.

---

### Buffering

Buffering защищает backend от медленных клиентов.

Но большие buffers увеличивают memory usage.

Нужно учитывать:

- размер ответа;
    
- число соединений;
    
- streaming requirements.
    

---

### File descriptor limits

Каждое socket/file использует file descriptor.

Проверить:

```bash
ulimit -n
cat /proc/$(pidof nginx | awk '{print $1}')/limits
```

Можно настроить:

```nginx
worker_rlimit_nofile 65535;
```

Но нужен также соответствующий OS/systemd limit.

---

### `sendfile`

`sendfile` позволяет kernel эффективнее отдавать статические файлы.

```nginx
sendfile on;
```

Уменьшает копирование данных между kernel и userspace.

---

### Connection backlog

Backlog — очередь соединений, которые уже пришли, но еще не приняты приложением.

В Nginx можно указать:

```nginx
listen 80 backlog=4096;
```

Также важны kernel параметры:

```text
net.core.somaxconn
```

---

### Graceful shutdown

Graceful shutdown прекращает прием новых соединений, но дает завершиться текущим.

```bash
nginx -s quit
```

При systemd:

```bash
systemctl stop nginx
```

Поведение зависит от unit и конфигурации.

---

## 15. Nginx и Kubernetes

### Nginx Ingress Controller

Nginx Ingress Controller — Kubernetes controller, который использует Nginx для обработки Ingress traffic.

Он следит за Kubernetes API и автоматически генерирует Nginx config.

---

### Ingress Resource

Ingress Resource описывает routing rules.

```yaml
rules:
  - host: app.example.com
    http:
      paths:
        - path: /
          backend:
            service:
              name: app
              port:
                number: 80
```

---

### Service и Endpoints

Ingress Controller обычно отправляет traffic к Kubernetes Service или напрямую к его endpoints, в зависимости от реализации.

```text
Ingress -> Service -> EndpointSlice -> Pods
```

---

### Балансировка между Pod

Kubernetes Service или Ingress Controller распределяет traffic между Ready Pods.

Pods без успешной readiness probe должны быть исключены из endpoints.

---

### TLS termination в Ingress

Ingress Controller может завершать TLS.

Сертификат хранится в Kubernetes Secret:

```yaml
tls:
  - hosts:
      - app.example.com
    secretName: app-tls
```

---

### Ingress annotations

Annotations меняют поведение Ingress Controller.

Например:

- rewrite;
    
- proxy timeout;
    
- body size;
    
- rate limit;
    
- authentication.
    

Они зависят от конкретной реализации Ingress Controller.

---

### Nginx Ingress vs обычный Nginx

Обычный Nginx читает статический config из файлов.

Nginx Ingress Controller:

- следит за Kubernetes API;
    
- читает Ingress/Service/Secret;
    
- динамически генерирует config;
    
- работает как Kubernetes controller.
    

---

### Kubernetes Service Load Balancing

Service дает стабильный ClusterIP и направляет traffic к Pods.

Балансировка может реализовываться через:

- kube-proxy;
    
- iptables;
    
- IPVS;
    
- eBPF CNI.
    

---

### Cloud Load Balancer → Ingress → Service → Pods

Типичный путь:

```text
Client
  ↓
Cloud Load Balancer
  ↓
Nginx Ingress Controller
  ↓
Service
  ↓
Pods
```

Каждый уровень выполняет свою задачу.

---

## 16. Troubleshooting

### `400 Bad Request`

Возможные причины:

- некорректный HTTP request;
    
- слишком большие headers;
    
- неправильный `Host`;
    
- клиент отправил HTTP на HTTPS port;
    
- поврежденный cookie/header.
    

Проверить access и error logs.

---

### `403 Forbidden`

Причины:

- нет прав на файл;
    
- directory listing запрещен;
    
- deny rule;
    
- неправильный owner/permissions;
    
- SELinux/AppArmor;
    
- отсутствует index file.
    

---

### `404 Not Found`

Причины:

- файл не существует;
    
- неправильный `root`;
    
- неправильный `location`;
    
- backend вернул 404;
    
- rewrite изменил URI;
    
- запрос попал не в тот `server`.
    

---

### `413 Request Entity Too Large`

Request body превышает `client_max_body_size`.

Исправление:

```nginx
client_max_body_size 50m;
```

После изменения:

```bash
nginx -t && systemctl reload nginx
```

---

### `499 Client Closed Request`

499 — нестандартный Nginx status.

Он означает, что клиент закрыл соединение до получения ответа.

Причины:

- backend отвечает слишком долго;
    
- клиентский timeout;
    
- пользователь отменил запрос;
    
- промежуточный proxy оборвал соединение.
    

---

### `502 Bad Gateway`

502 означает, что Nginx не получил корректный ответ от backend.

Причины:

- connection refused;
    
- backend упал;
    
- неправильный порт;
    
- protocol mismatch;
    
- backend закрыл connection;
    
- DNS указывает неправильно;
    
- Unix socket недоступен.
    

---

### `503 Service Unavailable`

Причины:

- нет доступных backend;
    
- все upstream marked down;
    
- rate/connection limit;
    
- maintenance mode;
    
- приложение само вернуло 503.
    

---

### `504 Gateway Timeout`

504 означает, что Nginx подключился к backend, но не дождался ответа вовремя.

Причины:

- медленный backend;
    
- медленная БД;
    
- зависший запрос;
    
- маленький `proxy_read_timeout`;
    
- network latency.
    

---

### Connection refused

Connection refused значит на указанном IP:port никто не слушает или соединение явно отклоняется.

Проверить:

```bash
ss -lntp
nc -vz backend 8080
curl -v http://backend:8080
```

---

### Connection timeout

Timeout обычно означает:

- firewall drop;
    
- routing issue;
    
- NetworkPolicy;
    
- backend завис;
    
- сеть недоступна.
    

В отличие от connection refused, TCP-соединение не получает немедленного отказа.

---

### Backend недоступен

Проверить:

- процесс;
    
- port;
    
- health endpoint;
    
- firewall;
    
- DNS;
    
- routes;
    
- application logs;
    
- resource exhaustion.
    

---

### Неправильный `proxy_pass`

Особенно важно наличие `/`.

```nginx
location /api/ {
    proxy_pass http://backend/;
}
```

и

```nginx
location /api/ {
    proxy_pass http://backend;
}
```

могут передавать backend разные URI.

---

### Неправильный `Host` header

Backend может ожидать конкретный host.

Настроить:

```nginx
proxy_set_header Host $host;
```

Или конкретное значение:

```nginx
proxy_set_header Host backend.internal;
```

---

### Ошибка TLS handshake

Причины:

- expired certificate;
    
- hostname mismatch;
    
- нет intermediate certificate;
    
- unsupported TLS version;
    
- неправильный private key;
    
- HTTP идет на HTTPS port;
    
- backend certificate не доверен.
    

Проверить:

```bash
openssl s_client -connect example.com:443 -servername example.com
```

---

### Redirect loop

Redirect loop возникает, когда клиент постоянно перенаправляется между URL.

Частая причина: backend считает запрос HTTP, хотя клиент пришел по HTTPS.

Нужно передать:

```nginx
proxy_set_header X-Forwarded-Proto $scheme;
```

И правильно настроить trusted proxy в приложении.

---

### Неравномерное распределение нагрузки

Причины:

- sticky sessions;
    
- keepalive;
    
- разная длительность запросов;
    
- разные weights;
    
- один NAT IP при `ip_hash`;
    
- часть backend недоступна.
    

Проверять `$upstream_addr` в access logs.

---

### Медленные backend-серверы

Сравнить:

```text
$request_time
$upstream_response_time
```

Если оба большие — backend, скорее всего, медленный.

Проверить:

- CPU/RAM;
    
- DB queries;
    
- external dependencies;
    
- connection pools;
    
- locks;
    
- application traces.
    

---

### Исчерпание worker connections

Симптомы:

- ошибки `worker_connections are not enough`;
    
- новые клиенты не подключаются;
    
- много open connections.
    

Проверить:

- `worker_connections`;
    
- file descriptor limits;
    
- keepalive;
    
- long-lived connections;
    
- WebSockets.
    

---

### Проверка через `curl`

Полезные команды:

```bash
curl -v http://example.com
curl -I https://example.com
curl -H 'Host: app.example.com' http://127.0.0.1
curl -w '%{http_code} %{time_total}\n' -o /dev/null -s http://example.com
```

---

### Анализ access и error logs

Access log показывает:

- request;
    
- status;
    
- время;
    
- upstream;
    
- client.
    

Error log показывает:

- connection errors;
    
- timeout;
    
- config/runtime issues;
    
- permission errors.
    

Оба лога нужно анализировать вместе.

---

## 17. Практика

### Развернуть Nginx

Ubuntu/Debian:

```bash
apt update
apt install nginx
systemctl enable --now nginx
```

Проверить:

```bash
curl http://localhost
```

---

### Настроить Virtual Host

Пример:

```nginx
server {
    listen 80;
    server_name app.example.com;

    root /var/www/app;
    index index.html;
}
```

Проверить с `Host` header:

```bash
curl -H 'Host: app.example.com' http://127.0.0.1
```

---

### Раздать статический сайт

Создать файл:

```bash
mkdir -p /var/www/app
echo '<h1>Hello</h1>' > /var/www/app/index.html
```

Настроить `root` и проверить через browser или `curl`.

---

### Настроить Reverse Proxy

```nginx
server {
    listen 80;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

---

### Настроить несколько backend-серверов

```nginx
upstream app_backend {
    server 10.0.0.10:8080;
    server 10.0.0.11:8080;
}

server {
    listen 80;

    location / {
        proxy_pass http://app_backend;
    }
}
```

---

### Проверить Round Robin

На каждом backend вернуть свое имя:

```text
backend-1
backend-2
```

Затем:

```bash
for i in $(seq 1 6); do curl -s http://localhost; done
```

Ответы должны чередоваться.

---

### Настроить Least Connections

```nginx
upstream app_backend {
    least_conn;

    server 10.0.0.10:8080;
    server 10.0.0.11:8080;
}
```

Полезно протестировать долгими запросами.

---

### Настроить таймауты

```nginx
location / {
    proxy_pass http://app_backend;

    proxy_connect_timeout 3s;
    proxy_send_timeout 30s;
    proxy_read_timeout 30s;
}
```

---

### Настроить HTTPS

```nginx
server {
    listen 443 ssl;
    server_name app.example.com;

    ssl_certificate /etc/nginx/tls/fullchain.pem;
    ssl_certificate_key /etc/nginx/tls/privkey.pem;
}
```

---

### Настроить HTTP → HTTPS redirect

```nginx
server {
    listen 80;
    server_name app.example.com;

    return 301 https://$host$request_uri;
}
```

---

### Настроить Rate Limiting

```nginx
http {
    limit_req_zone $binary_remote_addr zone=api:10m rate=10r/s;

    server {
        location /api/ {
            limit_req zone=api burst=20 nodelay;
            proxy_pass http://app_backend;
        }
    }
}
```

---

### Настроить Access Logs

```nginx
log_format upstream_log
    '$remote_addr "$request" $status '
    'request_time=$request_time '
    'upstream=$upstream_addr '
    'upstream_time=$upstream_response_time';

access_log /var/log/nginx/access.log upstream_log;
```

---

### Найти причину `502`

Порядок:

- посмотреть error log;
    
- проверить backend через `curl`;
    
- проверить port через `ss`/`nc`;
    
- проверить `proxy_pass`;
    
- проверить DNS;
    
- проверить protocol HTTP/HTTPS;
    
- проверить backend logs.
    

---

### Найти причину `504`

Порядок:

- проверить `$upstream_response_time`;
    
- вызвать backend напрямую;
    
- проверить application/DB latency;
    
- проверить network;
    
- проверить `proxy_read_timeout`;
    
- не увеличивать timeout, не найдя root cause.
    

---

## 18. Interview Questions

### Что такое Nginx

Nginx — event-driven web server, reverse proxy и load balancer.

Он также умеет TLS termination, caching, rate limiting и раздачу static content.

---

### Web Server vs Reverse Proxy

Web Server сам отдает файлы или ответы клиенту.

Reverse Proxy принимает запрос и передает его другому серверу.

```text
Web Server:    Client -> Nginx -> File
Reverse Proxy: Client -> Nginx -> Backend
```

---

### Forward Proxy vs Reverse Proxy

Forward Proxy представляет клиентов перед внешними серверами.

Reverse Proxy представляет backend-серверы перед клиентами.

---

### Как Nginx обрабатывает соединения

Master process управляет workers.

Worker processes используют event loop и неблокирующий I/O.

Один worker может обслуживать множество соединений одновременно.

---

### Что такое Load Balancer

Load Balancer распределяет traffic между несколькими backend.

Он нужен для scaling, availability и failover.

---

### Layer 4 vs Layer 7 Load Balancing

Layer 4 работает с TCP/UDP и смотрит на IP/port.

Layer 7 понимает HTTP и может маршрутизировать по host, path, headers и cookies.

---

### Round Robin vs Least Connections

Round Robin отправляет запросы по очереди.

Least Connections выбирает backend с минимальным количеством активных соединений.

Least Connections полезен при запросах разной длительности.

---

### Что такое Sticky Sessions

Sticky Sessions закрепляют клиента за определенным backend.

Они нужны, если session state хранится локально, но усложняют scaling и failover.

Лучше хранить session state во внешнем storage.

---

### Что такое Health Check

Health Check проверяет, способен ли backend обрабатывать запросы.

Нездоровый backend должен быть исключен из балансировки.

---

### Active vs Passive Health Check

Active Health Check — load balancer специально отправляет проверки.

Passive Health Check — состояние определяется по ошибкам реальных запросов.

---

### Что такое TLS termination

TLS termination — расшифровка HTTPS на Nginx или другом load balancer.

После этого traffic к backend может идти по HTTP или снова по HTTPS.

---

### Зачем нужен `X-Forwarded-For`

`X-Forwarded-For` передает backend реальный IP клиента и цепочку proxy.

Без него backend обычно видит только IP Nginx.

---

### Причины `502 Bad Gateway`

Основные причины:

- backend не запущен;
    
- connection refused;
    
- неправильный port;
    
- неправильный protocol;
    
- backend закрыл соединение;
    
- DNS/Unix socket проблема;
    
- некорректный ответ backend.
    

---

### Причины `504 Gateway Timeout`

Основные причины:

- backend отвечает слишком долго;
    
- медленная БД;
    
- зависший запрос;
    
- network timeout;
    
- слишком маленький `proxy_read_timeout`.
    

---

### Как убрать Load Balancer как Single Point of Failure

Использовать несколько экземпляров load balancer.

Перед ними настроить:

- cloud LB;
    
- VRRP/Keepalived;
    
- DNS;
    
- anycast;
    
- Kubernetes Service.
    

Также нужны health checks и отказоустойчивая конфигурация.

---

### Как Nginx балансирует запросы между backend-серверами

Backend описываются в `upstream`.

Nginx выбирает backend по алгоритму:

- Round Robin;
    
- Least Connections;
    
- IP Hash;
    
- Hash;
    
- Weighted balancing.
    

При ошибке он может временно исключить backend и попробовать другой.