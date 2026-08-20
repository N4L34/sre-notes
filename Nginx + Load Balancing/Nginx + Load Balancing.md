[[Nginx + Load Balancing. Конспект]]

## 1. Основы Nginx

- [ ] Что такое Nginx
- [ ] Web Server
- [ ] Reverse Proxy
- [ ] Load Balancer
- [ ] Event-driven architecture
- [ ] Worker Processes
- [ ] Worker Connections
- [ ] Master Process

---

## 2. Конфигурация Nginx

- [ ] `/etc/nginx/nginx.conf`
- [ ] `http`
- [ ] `server`
- [ ] `location`
- [ ] `listen`
- [ ] `server_name`
- [ ] `root`
- [ ] `index`
- [ ] `include`
- [ ] Проверка конфигурации через `nginx -t`
- [ ] Перезагрузка через `nginx -s reload`

---

## 3. Обработка запросов

- [ ] Virtual Hosts
- [ ] Выбор `server` блока
- [ ] Location matching
- [ ] Exact match
- [ ] Prefix match
- [ ] Regex match
- [ ] URI rewriting
- [ ] Redirect
- [ ] `return`
- [ ] `rewrite`
- [ ] Query parameters

---

## 4. Reverse Proxy

- [ ] Что такое Reverse Proxy
- [ ] Forward Proxy vs Reverse Proxy
- [ ] `proxy_pass`
- [ ] `proxy_set_header`
- [ ] `Host`
- [ ] `X-Real-IP`
- [ ] `X-Forwarded-For`
- [ ] `X-Forwarded-Proto`
- [ ] Proxy timeouts
- [ ] Proxy buffering
- [ ] WebSocket proxying

---

## 5. Основы Load Balancing

- [ ] Что такое Load Balancer
- [ ] Зачем нужен Load Balancing
- [ ] Client-side vs Server-side Load Balancing
- [ ] Hardware vs Software Load Balancer
- [ ] Internal vs External Load Balancer
- [ ] Layer 4 Load Balancing
- [ ] Layer 7 Load Balancing
- [ ] Stateless vs Stateful applications

---

## 6. Nginx Upstream

- [ ] `upstream`
- [ ] Backend servers
- [ ] `server`
- [ ] `weight`
- [ ] `backup`
- [ ] `down`
- [ ] `max_fails`
- [ ] `fail_timeout`
- [ ] Connection reuse
- [ ] Keepalive connections

---

## 7. Алгоритмы балансировки

- [ ] Round Robin
- [ ] Weighted Round Robin
- [ ] Least Connections
- [ ] `least_conn`
- [ ] IP Hash
- [ ] `ip_hash`
- [ ] Hash-based balancing
- [ ] Consistent Hashing basics
- [ ] Когда использовать каждый алгоритм

---

## 8. Health Checks и отказоустойчивость

- [ ] Active Health Checks
- [ ] Passive Health Checks
- [ ] Health Check endpoint
- [ ] Failover
- [ ] Backend недоступен
- [ ] Retry на другой backend
- [ ] `proxy_next_upstream`
- [ ] Graceful reload
- [ ] Single Point of Failure
- [ ] Несколько Load Balancer экземпляров

---

## 9. Sessions

- [ ] Sticky Sessions
- [ ] Session Affinity
- [ ] Cookie-based affinity
- [ ] IP-based affinity
- [ ] Проблемы Sticky Sessions
- [ ] Хранение сессий вне приложения
- [ ] Redis для хранения сессий

---

## 10. TLS / HTTPS

- [ ] TLS termination
- [ ] TLS passthrough
- [ ] SSL certificate
- [ ] Private key
- [ ] Certificate chain
- [ ] `ssl_certificate`
- [ ] `ssl_certificate_key`
- [ ] HTTP → HTTPS redirect
- [ ] SNI
- [ ] TLS между Nginx и backend
- [ ] Обновление сертификатов без остановки

---

## 11. Static Content и Caching

- [ ] Раздача статических файлов
- [ ] Browser caching
- [ ] `expires`
- [ ] `Cache-Control`
- [ ] Proxy Cache
- [ ] Cache key
- [ ] Cache invalidation
- [ ] Gzip compression
- [ ] Brotli basics

---

## 12. Rate Limiting и ограничения

- [ ] Rate Limiting
- [ ] `limit_req`
- [ ] Burst
- [ ] Connection limiting
- [ ] `limit_conn`
- [ ] Request body size
- [ ] `client_max_body_size`
- [ ] Защита backend от перегрузки

---

## 13. Логи

- [ ] Access Log
- [ ] Error Log
- [ ] Log Format
- [ ] `$request_time`
- [ ] `$upstream_response_time`
- [ ] `$upstream_addr`
- [ ] `$status`
- [ ] `$upstream_status`
- [ ] Поиск медленных запросов
- [ ] Ротация логов

---

## 14. Производительность

- [ ] `worker_processes`
- [ ] `worker_connections`
- [ ] Максимальное количество соединений
- [ ] Keepalive
- [ ] Timeouts
- [ ] Buffering
- [ ] File descriptor limits
- [ ] `sendfile`
- [ ] Connection backlog
- [ ] Graceful shutdown

---

## 15. Nginx и Kubernetes

- [ ] Nginx Ingress Controller
- [ ] Ingress Resource
- [ ] Service и Endpoints
- [ ] Балансировка между Pod
- [ ] TLS termination в Ingress
- [ ] Ingress annotations
- [ ] Nginx Ingress vs обычный Nginx
- [ ] Kubernetes Service Load Balancing
- [ ] Cloud Load Balancer → Ingress → Service → Pods

---

## 16. Troubleshooting

- [ ] `400 Bad Request`
- [ ] `403 Forbidden`
- [ ] `404 Not Found`
- [ ] `413 Request Entity Too Large`
- [ ] `499 Client Closed Request`
- [ ] `502 Bad Gateway`
- [ ] `503 Service Unavailable`
- [ ] `504 Gateway Timeout`
- [ ] Connection refused
- [ ] Connection timeout
- [ ] Backend недоступен
- [ ] Неправильный `proxy_pass`
- [ ] Неправильный `Host` header
- [ ] Ошибка TLS handshake
- [ ] Redirect loop
- [ ] Неравномерное распределение нагрузки
- [ ] Медленные backend-серверы
- [ ] Исчерпание worker connections
- [ ] Проверка через `curl`
- [ ] Анализ access и error logs

---

## 17. Практика

- [ ] Развернуть Nginx
- [ ] Настроить Virtual Host
- [ ] Раздать статический сайт
- [ ] Настроить Reverse Proxy
- [ ] Настроить несколько backend-серверов
- [ ] Проверить Round Robin
- [ ] Настроить Least Connections
- [ ] Настроить таймауты
- [ ] Настроить HTTPS
- [ ] Настроить HTTP → HTTPS redirect
- [ ] Настроить Rate Limiting
- [ ] Настроить Access Logs
- [ ] Найти причину `502`
- [ ] Найти причину `504`

---

## 18. Interview Questions

- [ ] Что такое Nginx
- [ ] Web Server vs Reverse Proxy
- [ ] Forward Proxy vs Reverse Proxy
- [ ] Как Nginx обрабатывает соединения
- [ ] Что такое Load Balancer
- [ ] Layer 4 vs Layer 7 Load Balancing
- [ ] Round Robin vs Least Connections
- [ ] Что такое Sticky Sessions
- [ ] Что такое Health Check
- [ ] Active vs Passive Health Check
- [ ] Что такое TLS termination
- [ ] Зачем нужен `X-Forwarded-For`
- [ ] Причины `502 Bad Gateway`
- [ ] Причины `504 Gateway Timeout`
- [ ] Как убрать Load Balancer как Single Point of Failure
- [ ] Как Nginx балансирует запросы между backend-серверами