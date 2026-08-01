[[Nginx + Load Balancing. Конспект]]

## 1. Основы Nginx

- [x] Что такое Nginx
- [x] Web Server
- [x] Reverse Proxy
- [x] Load Balancer
- [x] Event-driven architecture
- [x] Worker Processes
- [x] Worker Connections
- [x] Master Process

---

## 2. Конфигурация Nginx

- [x] `/etc/nginx/nginx.conf`
- [x] `http`
- [x] `server`
- [x] `location`
- [x] `listen`
- [x] `server_name`
- [x] `root`
- [x] `index`
- [x] `include`
- [x] Проверка конфигурации через `nginx -t`
- [x] Перезагрузка через `nginx -s reload`

---

## 3. Обработка запросов

- [x] Virtual Hosts
- [x] Выбор `server` блока
- [x] Location matching
- [x] Exact match
- [x] Prefix match
- [x] Regex match
- [x] URI rewriting
- [x] Redirect
- [x] `return`
- [x] `rewrite`
- [x] Query parameters

---

## 4. Reverse Proxy

- [x] Что такое Reverse Proxy
- [x] Forward Proxy vs Reverse Proxy
- [x] `proxy_pass`
- [x] `proxy_set_header`
- [x] `Host`
- [x] `X-Real-IP`
- [x] `X-Forwarded-For`
- [x] `X-Forwarded-Proto`
- [x] Proxy timeouts
- [x] Proxy buffering
- [x] WebSocket proxying

---

## 5. Основы Load Balancing

- [x] Что такое Load Balancer
- [x] Зачем нужен Load Balancing
- [x] Client-side vs Server-side Load Balancing
- [x] Hardware vs Software Load Balancer
- [x] Internal vs External Load Balancer
- [x] Layer 4 Load Balancing
- [x] Layer 7 Load Balancing
- [x] Stateless vs Stateful applications

---

## 6. Nginx Upstream

- [x] `upstream`
- [x] Backend servers
- [x] `server`
- [x] `weight`
- [x] `backup`
- [x] `down`
- [x] `max_fails`
- [x] `fail_timeout`
- [x] Connection reuse
- [x] Keepalive connections

---

## 7. Алгоритмы балансировки

- [x] Round Robin
- [x] Weighted Round Robin
- [x] Least Connections
- [x] `least_conn`
- [x] IP Hash
- [x] `ip_hash`
- [x] Hash-based balancing
- [x] Consistent Hashing basics
- [x] Когда использовать каждый алгоритм

---

## 8. Health Checks и отказоустойчивость

- [x] Active Health Checks
- [x] Passive Health Checks
- [x] Health Check endpoint
- [x] Failover
- [x] Backend недоступен
- [x] Retry на другой backend
- [x] `proxy_next_upstream`
- [x] Graceful reload
- [x] Single Point of Failure
- [x] Несколько Load Balancer экземпляров

---

## 9. Sessions

- [x] Sticky Sessions
- [x] Session Affinity
- [x] Cookie-based affinity
- [x] IP-based affinity
- [x] Проблемы Sticky Sessions
- [x] Хранение сессий вне приложения
- [x] Redis для хранения сессий

---

## 10. TLS / HTTPS

- [x] TLS termination
- [x] TLS passthrough
- [x] SSL certificate
- [x] Private key
- [x] Certificate chain
- [x] `ssl_certificate`
- [x] `ssl_certificate_key`
- [x] HTTP → HTTPS redirect
- [x] SNI
- [x] TLS между Nginx и backend
- [x] Обновление сертификатов без остановки

---

## 11. Static Content и Caching

- [x] Раздача статических файлов
- [x] Browser caching
- [x] `expires`
- [x] `Cache-Control`
- [x] Proxy Cache
- [x] Cache key
- [x] Cache invalidation
- [x] Gzip compression
- [x] Brotli basics

---

## 12. Rate Limiting и ограничения

- [x] Rate Limiting
- [x] `limit_req`
- [x] Burst
- [x] Connection limiting
- [x] `limit_conn`
- [x] Request body size
- [x] `client_max_body_size`
- [x] Защита backend от перегрузки

---

## 13. Логи

- [x] Access Log
- [x] Error Log
- [x] Log Format
- [x] `$request_time`
- [x] `$upstream_response_time`
- [x] `$upstream_addr`
- [x] `$status`
- [x] `$upstream_status`
- [x] Поиск медленных запросов
- [x] Ротация логов

---

## 14. Производительность

- [x] `worker_processes`
- [x] `worker_connections`
- [x] Максимальное количество соединений
- [x] Keepalive
- [x] Timeouts
- [x] Buffering
- [x] File descriptor limits
- [x] `sendfile`
- [x] Connection backlog
- [x] Graceful shutdown

---

## 15. Nginx и Kubernetes

- [x] Nginx Ingress Controller
- [x] Ingress Resource
- [x] Service и Endpoints
- [x] Балансировка между Pod
- [x] TLS termination в Ingress
- [x] Ingress annotations
- [x] Nginx Ingress vs обычный Nginx
- [x] Kubernetes Service Load Balancing
- [x] Cloud Load Balancer → Ingress → Service → Pods

---

## 16. Troubleshooting

- [x] `400 Bad Request`
- [x] `403 Forbidden`
- [x] `404 Not Found`
- [x] `413 Request Entity Too Large`
- [x] `499 Client Closed Request`
- [x] `502 Bad Gateway`
- [x] `503 Service Unavailable`
- [x] `504 Gateway Timeout`
- [x] Connection refused
- [x] Connection timeout
- [x] Backend недоступен
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