## Практика Python для DevOps/SRE

### Парсинг логов

Парсинг логов — чтение log-файла и извлечение нужных данных.

Например:

- timestamp;
    
- level;
    
- status code;
    
- IP;
    
- message.
    

```python
with open("app.log") as f:
    for line in f:
        print(line.strip())
```

---

### Поиск ошибок в логах

Можно искать строки с `ERROR`, `Exception`, `Traceback`.

```python
with open("app.log") as f:
    for line in f:
        if "ERROR" in line:
            print(line.strip())
```

Полезно для быстрого анализа инцидентов.

---

### Подсчет количества `ERROR`, `WARN`, `INFO`

Можно считать количество строк каждого уровня.

```python
counts = {"ERROR": 0, "WARN": 0, "INFO": 0}

with open("app.log") as f:
    for line in f:
        for level in counts:
            if level in line:
                counts[level] += 1

print(counts)
```

---

### Фильтрация логов по времени

Если в логах есть timestamp, можно отфильтровать нужный период.

```python
from datetime import datetime

start = datetime.fromisoformat("2026-07-09 10:00:00")

with open("app.log") as f:
    for line in f:
        ts = line[:19]
        log_time = datetime.fromisoformat(ts)

        if log_time >= start:
            print(line.strip())
```

---

### Фильтрация логов по статус-коду

Полезно для access logs.

```python
with open("access.log") as f:
    for line in f:
        if " 500 " in line:
            print(line.strip())
```

Так можно быстро найти HTTP 500.

---

### Парсинг access logs Nginx

Nginx access log обычно содержит IP, request, status code и user-agent.

Пример через regex:

```python
import re

pattern = r'(\S+) .* "(\w+) (.*?) HTTP.*" (\d{3})'

with open("access.log") as f:
    for line in f:
        match = re.search(pattern, line)
        if match:
            ip, method, path, status = match.groups()
            print(ip, method, path, status)
```

---

### Работа с JSON-логами

Если приложение пишет JSON-логи, их удобно читать через `json`.

```python
import json

with open("app.json.log") as f:
    for line in f:
        log = json.loads(line)
        print(log["level"], log["message"])
```

JSON-логи проще парсить, чем обычный текст.

---

### Чтение больших файлов построчно

Большие файлы нельзя полностью грузить в память.

Правильно:

```python
with open("big.log") as f:
    for line in f:
        process(line)
```

Так Python читает файл постепенно.

---

### HTTP healthcheck

Healthcheck проверяет, отвечает ли сервис.

```python
import requests

response = requests.get("http://localhost:8080/health", timeout=5)

if response.status_code == 200:
    print("OK")
else:
    print("FAIL")
```

---

### Проверка списка URL

Можно пройтись по списку endpoint'ов.

```python
import requests

urls = [
    "https://example.com/health",
    "https://api.example.com/status",
]

for url in urls:
    try:
        r = requests.get(url, timeout=5)
        print(url, r.status_code)
    except requests.RequestException as e:
        print(url, "ERROR", e)
```

---

### Работа с REST API

REST API обычно возвращает JSON.

```python
import requests

r = requests.get("https://api.example.com/users", timeout=5)
data = r.json()

print(data)
```

---

### Авторизация через token

Token обычно передают в HTTP header.

```python
import requests

token = "my-token"

headers = {
    "Authorization": f"Bearer {token}"
}

r = requests.get(
    "https://api.example.com/data",
    headers=headers,
    timeout=5,
)
```

В реальности token лучше брать из environment variable.

---

### Обработка pagination в API

API может отдавать данные страницами.

```python
import requests

page = 1

while True:
    r = requests.get(
        "https://api.example.com/items",
        params={"page": page},
        timeout=5,
    )

    data = r.json()

    if not data["items"]:
        break

    for item in data["items"]:
        print(item)

    page += 1
```

---

### Retry при ошибке запроса

Retry нужен, если API временно недоступен.

```python
import time
import requests

for attempt in range(3):
    try:
        r = requests.get("https://example.com", timeout=5)
        r.raise_for_status()
        break
    except requests.RequestException:
        time.sleep(2)
else:
    print("request failed after retries")
```

---

### Timeout для HTTP-запросов

Timeout обязателен, иначе скрипт может зависнуть.

```python
import requests

requests.get("https://example.com", timeout=5)
```

Для DevOps-скриптов не стоит делать HTTP-запросы без timeout.

---

### Парсинг JSON-ответа API

```python
import requests

r = requests.get("https://api.example.com/status", timeout=5)
data = r.json()

print(data["status"])
```

Если поле может отсутствовать:

```python
print(data.get("status", "unknown"))
```

---

### Генерация простого отчета

Можно собрать данные и вывести summary.

```python
report = {
    "errors": 12,
    "warnings": 5,
    "status": "degraded",
}

print("Report")
print("Errors:", report["errors"])
print("Warnings:", report["warnings"])
print("Status:", report["status"])
```

Отчет можно сохранить в файл, JSON или отправить в Slack.

---

### Автоматизация через CLI-аргументы

Для аргументов используют `argparse`.

```python
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--log-file", required=True)
parser.add_argument("--level", default="ERROR")

args = parser.parse_args()

print(args.log_file)
print(args.level)
```

Запуск:

```bash
python script.py --log-file app.log --level ERROR
```

---

### Запуск Linux-команд через `subprocess`

`subprocess` позволяет запускать внешние команды.

```python
import subprocess

result = subprocess.run(
    ["df", "-h"],
    capture_output=True,
    text=True,
)

print(result.stdout)
```

---

### Обработка exit code внешней команды

У команды есть return code.

```python
import subprocess

result = subprocess.run(["systemctl", "is-active", "nginx"])

if result.returncode == 0:
    print("nginx is active")
else:
    print("nginx is not active")
```

---

### Проверка свободного места на диске

Можно использовать стандартный модуль `shutil`.

```python
import shutil

usage = shutil.disk_usage("/")

free_gb = usage.free / 1024 / 1024 / 1024

print(f"Free: {free_gb:.2f} GB")
```

---

### Проверка запущенного процесса

Простой вариант через `pgrep`.

```python
import subprocess

result = subprocess.run(["pgrep", "nginx"])

if result.returncode == 0:
    print("process is running")
else:
    print("process is not running")
```

---

### Проверка systemd service

Через `systemctl is-active`.

```python
import subprocess

service = "nginx"

result = subprocess.run(
    ["systemctl", "is-active", "--quiet", service]
)

if result.returncode == 0:
    print(f"{service} is active")
else:
    print(f"{service} is not active")
```

---

### Проверка Docker container

Можно вызвать `docker ps`.

```python
import subprocess

result = subprocess.run(
    ["docker", "ps", "--format", "{{.Names}}"],
    capture_output=True,
    text=True,
)

containers = result.stdout.splitlines()

if "nginx" in containers:
    print("nginx container is running")
```

---

### Проверка Kubernetes Pod через `kubectl`

Можно получить pods через `kubectl`.

```python
import subprocess

result = subprocess.run(
    ["kubectl", "get", "pods", "-n", "default"],
    capture_output=True,
    text=True,
)

print(result.stdout)
```

Для автоматизации лучше использовать JSON output:

```python
kubectl get pods -o json
```

---

### Отправка уведомления в Slack/Telegram

Обычно отправляют HTTP POST.

Пример идеи:

```python
import requests

webhook_url = "https://hooks.slack.com/services/..."

payload = {
    "text": "Service nginx is down"
}

requests.post(webhook_url, json=payload, timeout=5)
```

Webhook URL нельзя хранить прямо в коде.

---

### Скрипт для cleanup старых файлов

Удалить файлы старше N дней.

```python
from pathlib import Path
import time

directory = Path("/var/log/myapp")
days = 7
now = time.time()

for file in directory.glob("*.log"):
    age_days = (now - file.stat().st_mtime) / 86400

    if age_days > days:
        file.unlink()
```

---

### Скрипт для backup

Простой backup через архив.

```python
import tarfile
from datetime import datetime

backup_name = f"backup-{datetime.now():%Y-%m-%d}.tar.gz"

with tarfile.open(backup_name, "w:gz") as tar:
    tar.add("/etc/nginx", arcname="nginx")
```

---

### Скрипт для проверки сертификата

Можно проверить срок действия TLS-сертификата.

```python
import ssl
import socket
from datetime import datetime

host = "example.com"
port = 443

context = ssl.create_default_context()

with socket.create_connection((host, port), timeout=5) as sock:
    with context.wrap_socket(sock, server_hostname=host) as ssock:
        cert = ssock.getpeercert()

expires = datetime.strptime(
    cert["notAfter"],
    "%b %d %H:%M:%S %Y %Z"
)

print("Expires:", expires)
```

---

### Скрипт для проверки DNS

Можно проверить, резолвится ли домен.

```python
import socket

domain = "example.com"

try:
    ip = socket.gethostbyname(domain)
    print(domain, ip)
except socket.gaierror:
    print("DNS resolution failed")
```

Для более продвинутой работы с DNS часто используют библиотеку `dnspython`.