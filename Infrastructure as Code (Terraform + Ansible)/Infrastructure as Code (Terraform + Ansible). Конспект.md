# Infrastructure as Code

## 1. Основы IaC

### Что такое Infrastructure as Code

Infrastructure as Code, или IaC — подход, при котором инфраструктура описывается кодом.

Например:

- servers;

- networks;

- databases;

- Kubernetes resources;

- firewall rules;

- cloud resources.


---

### Зачем нужен IaC

IaC нужен, чтобы инфраструктура была:

- повторяемой;

- версионируемой;

- проверяемой через review;

- автоматизированной;

- менее зависимой от ручных действий.


---

### Imperative vs Declarative подход

Imperative — ты описываешь шаги.

```text
создай сервер
поставь nginx
измени конфиг
перезапусти сервис
```

Declarative — ты описываешь желаемое состояние.

```text
должен быть сервер с nginx
```

Terraform больше declarative, Ansible чаще imperative, но с idempotency.

---

### Idempotency

Idempotency — повторный запуск не должен ломать систему или делать лишние изменения.

Например, если nginx уже установлен, Ansible не должен ставить его заново каждый раз.

---

### Reproducible infrastructure

Reproducible infrastructure — инфраструктуру можно воспроизвести из кода.

Например, создать одинаковое окружение:

```text
dev
stage
prod
```

Это снижает риск “у меня руками настроено иначе”.

---

### Version control для инфраструктуры

IaC-код хранят в Git.

Это дает:

- историю изменений;

- pull request review;

- rollback;

- audit;

- понимание, кто и когда изменил инфраструктуру.


---

# Terraform

## 2. Основы Terraform

### Что такое Terraform

Terraform — инструмент для создания и управления инфраструктурой как кодом.

Обычно используется для:

- cloud resources;

- networks;

- servers;

- databases;

- Kubernetes resources;

- IAM.


---

### Terraform architecture

Упрощенно:

```text
Terraform CLI -> Provider -> Cloud/API
```

Terraform читает `.tf` файлы, строит план изменений и применяет их через provider.

---

### Terraform CLI

Terraform CLI — командная утилита.

Основные команды:

```bash
terraform init
terraform plan
terraform apply
terraform destroy
```

---

### Provider

Provider — плагин для работы с конкретной платформой.

Примеры:

- AWS;

- Google Cloud;

- Azure;

- Kubernetes;

- Helm;

- Cloudflare.


---

### Resource

Resource — объект инфраструктуры, которым управляет Terraform.

Например:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-..."
  instance_type = "t3.micro"
}
```

---

### Data Source

Data Source читает существующие данные, но не создает ресурс.

Например, найти существующий AMI:

```hcl
data "aws_ami" "ubuntu" {
  most_recent = true
}
```

---

### Configuration files (.tf)

`.tf` файлы содержат Terraform configuration.

Обычно:

```text
main.tf
variables.tf
outputs.tf
providers.tf
versions.tf
```

---

## 3. Terraform Workflow

### `terraform init`

Инициализирует Terraform-проект.

```bash
terraform init
```

Скачивает providers, настраивает backend и подготавливает рабочую директорию.

---

### `terraform plan`

Показывает, что Terraform собирается изменить.

```bash
terraform plan
```

Plan не меняет инфраструктуру, только показывает будущие действия.

---

### `terraform apply`

Применяет изменения.

```bash
terraform apply
```

Terraform создаст, изменит или удалит ресурсы согласно plan.

---

### `terraform destroy`

Удаляет ресурсы, описанные в Terraform state.

```bash
terraform destroy
```

Опасная команда, особенно для production.

---

### `terraform validate`

Проверяет синтаксис и корректность конфигурации.

```bash
terraform validate
```

Не проверяет все cloud-ошибки, но ловит базовые проблемы.

---

### `terraform fmt`

Форматирует `.tf` файлы.

```bash
terraform fmt
```

Хорошая практика перед commit.

---

## 4. Terraform Configuration

### Variables

Variables позволяют передавать значения в Terraform configuration.

```hcl
variable "instance_type" {
  default = "t3.micro"
}
```

Использование:

```hcl
instance_type = var.instance_type
```

---

### Outputs

Outputs выводят значения после apply.

```hcl
output "instance_ip" {
  value = aws_instance.web.public_ip
}
```

Полезно для IP, DNS, IDs и других результатов.

---

### Locals

Locals — локальные вычисляемые значения.

```hcl
locals {
  app_name = "myapp"
}
```

Удобно, чтобы не повторять одинаковые выражения.

---

### Resource dependencies

Terraform сам строит зависимости между ресурсами через references.

Например:

```hcl
subnet_id = aws_subnet.public.id
```

Если нужно явно:

```hcl
depends_on = [aws_instance.web]
```

---

### References

References позволяют обращаться к другим ресурсам.

```hcl
aws_instance.web.id
aws_vpc.main.id
var.region
local.app_name
```

Так Terraform понимает связи между объектами.

---

### Expressions

Expressions — выражения внутри Terraform.

Например:

```hcl
name = "${var.env}-web"
```

Или условие:

```hcl
count = var.enabled ? 1 : 0
```

---

### Functions basics

Terraform имеет встроенные функции.

Примеры:

```hcl
length(var.subnets)
lower(var.name)
join(",", var.names)
file("config.json")
```

Функции помогают обрабатывать строки, списки, map и файлы.

---

## 5. Terraform State

### Что такое Terraform state

Terraform state — файл, где Terraform хранит информацию о созданных ресурсах.

Он связывает `.tf` код с реальными объектами в cloud.

---

### terraform.tfstate

`terraform.tfstate` — локальный state-файл.

В нем могут быть:

- resource IDs;

- attributes;

- outputs;

- иногда sensitive values.


Его нельзя случайно терять или публично коммитить.

---

### Local state

Local state хранится на машине пользователя.

Минусы:

- сложно работать в команде;

- можно потерять файл;

- нет нормального locking;

- риск конфликтов.


---

### Remote state

Remote state хранится удаленно.

Например:

- S3;

- Terraform Cloud;

- GCS;

- Azure Storage.


Это стандартный вариант для командной работы.

---

### Backend

Backend определяет, где хранится state.

Пример S3 backend:

```hcl
backend "s3" {
  bucket = "my-tf-state"
  key    = "prod/terraform.tfstate"
  region = "eu-central-1"
}
```

---

### State locking

State locking блокирует state во время apply.

Это нужно, чтобы два человека одновременно не изменили инфраструктуру.

Например, в AWS часто используют DynamoDB lock для S3 backend.

---

### Почему нельзя терять state

Если потерять state, Terraform перестанет понимать, какими ресурсами он управляет.

Последствия:

- дублирование ресурсов;

- невозможность нормального destroy;

- опасные изменения;

- ручной import ресурсов.


---

## 6. Terraform Modules

### Что такое Module

Module — переиспользуемый набор Terraform-конфигураций.

Например, module для:

- VPC;

- EC2;

- Kubernetes cluster;

- database;

- IAM role.


---

### Root module

Root module — основной Terraform-код, из которого запускают команды.

Это директория, где выполняется:

```bash
terraform plan
terraform apply
```

---

### Child modules

Child module — module, который вызывается из root module.

```hcl
module "vpc" {
  source = "./modules/vpc"
}
```

---

### Reusable infrastructure

Modules позволяют переиспользовать инфраструктурные шаблоны.

Например, один VPC module можно использовать для dev, stage и prod.

---

### Module registry basics

Terraform Module Registry — хранилище готовых modules.

Например, можно взять готовый AWS VPC module.

Но перед использованием в production module нужно понимать и ревьюить.

---

## 7. Terraform в Cloud

### Terraform + AWS basics

Terraform часто используют для AWS.

Пример provider:

```hcl
provider "aws" {
  region = "eu-central-1"
}
```

Через него создают VPC, EC2, IAM, RDS, EKS и другие ресурсы.

---

### Создание VPC

VPC — изолированная сеть в AWS.

Terraform может создавать:

- VPC;

- subnets;

- route tables;

- internet gateway;

- NAT gateway.


---

### Создание EC2

EC2 instance — виртуальный сервер в AWS.

Примерно:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-..."
  instance_type = "t3.micro"
}
```

---

### Security Groups

Security Group — firewall для AWS resources.

Обычно описывает:

- inbound rules;

- outbound rules;

- allowed ports;

- allowed CIDR или source SG.


---

### IAM basics

IAM управляет доступами в AWS.

Terraform может создавать:

- users;

- roles;

- policies;

- instance profiles;

- service permissions.


С IAM нужно соблюдать least privilege.

---

### Terraform + Kubernetes basics

Terraform может управлять Kubernetes resources через Kubernetes provider.

Например:

- namespaces;

- deployments;

- services;

- configmaps.


Но часто Kubernetes manifests лучше вести через Helm/GitOps, а Terraform оставить для инфраструктуры.

---

# Ansible

## 8. Основы Ansible

### Что такое Ansible

Ansible — инструмент для configuration management и automation.

Он используется для:

- настройки серверов;

- установки пакетов;

- изменения конфигов;

- деплоя приложений;

- выполнения команд на группах hosts.


---

### Configuration Management

Configuration Management — управление состоянием серверов.

Например:

- nginx установлен;

- config лежит на месте;

- service запущен;

- user создан;

- firewall настроен.


---

### Agentless architecture

Ansible не требует агента на managed hosts.

Обычно он подключается по SSH и выполняет задачи удаленно.

Это упрощает внедрение.

---

### SSH connection

Ansible чаще всего работает через SSH.

```text
control node -> SSH -> managed hosts
```

На удаленных Linux-хостах обычно нужен Python.

---

### Inventory

Inventory — список хостов, которыми управляет Ansible.

Пример:

```ini
[web]
web1.example.com
web2.example.com

[db]
db1.example.com
```

---

### Hosts

Hosts — серверы из inventory.

Playbook указывает, на каких hosts выполнять задачи.

```yaml
- hosts: web
  tasks:
    - name: install nginx
      apt:
        name: nginx
        state: present
```

---

## 9. Ansible Components

### Playbook

Playbook — YAML-файл со списком plays и tasks.

Пример:

```yaml
- hosts: web
  tasks:
    - name: install nginx
      apt:
        name: nginx
        state: present
```

---

### Task

Task — отдельное действие в playbook.

Например:

- установить пакет;

- скопировать файл;

- перезапустить service;

- создать user.


---

### Module

Module — готовая функция Ansible для выполнения действия.

Примеры:

- `apt`;

- `yum`;

- `copy`;

- `template`;

- `service`;

- `user`;

- `file`.


---

### Handler

Handler — task, который запускается только при уведомлении.

Например, перезапустить nginx только если config изменился.

```yaml
handlers:
  - name: restart nginx
    service:
      name: nginx
      state: restarted
```

---

### Role

Role — структурированный способ организовать playbook.

Обычно внутри:

```text
roles/nginx/
  tasks/
  handlers/
  templates/
  defaults/
  vars/
  files/
```

Roles делают Ansible-код переиспользуемым.

---

### Variables

Variables позволяют параметризовать playbooks.

```yaml
nginx_port: 80
```

Использование:

```yaml
listen {{ nginx_port }}
```

---

### Facts

Facts — информация о managed host.

Например:

- hostname;

- OS;

- IP;

- memory;

- CPU;

- disks.


Посмотреть можно через:

```bash
ansible all -m setup
```

---

## 10. Ansible Workflow

### `ansible` command

`ansible` — ad-hoc команда для быстрого выполнения действия.

```bash
ansible web -m ping
ansible web -a "uptime"
```

Полезно для быстрых проверок.

---

### `ansible-playbook`

`ansible-playbook` запускает playbook.

```bash
ansible-playbook -i inventory.ini site.yml
```

Это основной способ применять конфигурацию.

---

### Check mode

Check mode показывает, что изменится, но не применяет изменения.

```bash
ansible-playbook site.yml --check
```

Похоже на `terraform plan`, но не всегда идеально поддерживается всеми modules.

---

### Diff mode

Diff mode показывает разницу в изменяемых файлах.

```bash
ansible-playbook site.yml --diff
```

Полезно для шаблонов и конфигов.

---

### Become (sudo)

`become` позволяет выполнять tasks с повышенными правами.

```yaml
become: true
```

Обычно используется для установки пакетов и изменения системных файлов.

---

## 11. Ansible Templates

### Jinja2 basics

Ansible templates используют Jinja2.

Пример:

```jinja2
server {
  listen {{ nginx_port }};
}
```

Ansible подставит переменные и создаст итоговый config.

---

### Template files

Template files обычно лежат в директории `templates/`.

Например:

```text
templates/nginx.conf.j2
```

Применение:

```yaml
template:
  src: nginx.conf.j2
  dest: /etc/nginx/nginx.conf
```

---

### Variables inside templates

В template можно использовать variables.

```jinja2
worker_processes {{ nginx_worker_processes }};
```

Значения берутся из inventory, group_vars, host_vars, role defaults или playbook.

---

### Configuration generation

Templates используют для генерации конфигов.

Например:

- nginx;

- systemd unit;

- app config;

- prometheus config;

- logrotate config.


После изменения config обычно вызывают handler для restart/reload.

---

## 12. Ansible Best Practices

### Idempotent playbooks

Playbook должен быть idempotent.

Повторный запуск не должен каждый раз менять систему без причины.

Плохо:

```yaml
shell: echo "line" >> /etc/config
```

Лучше использовать modules типа `lineinfile`, `template`, `copy`.

---

### Roles structure

Roles помогают держать код чистым.

Типичная структура:

```text
roles/app/
  tasks/main.yml
  handlers/main.yml
  templates/
  defaults/main.yml
  vars/main.yml
```

---

### Secrets через Ansible Vault

Ansible Vault шифрует secrets.

```bash
ansible-vault encrypt secrets.yml
```

Используют для:

- passwords;

- tokens;

- private keys;

- credentials.


---

### Inventory management

Inventory лучше структурировать по окружениям.

Например:

```text
inventories/dev/
inventories/stage/
inventories/prod/
```

Переменные можно хранить в `group_vars` и `host_vars`.

---

### Tags

Tags позволяют запускать часть playbook.

```yaml
tags:
  - nginx
```

Запуск:

```bash
ansible-playbook site.yml --tags nginx
```

Удобно для больших playbooks.

---

## 13. Terraform vs Ansible

### Terraform создаёт инфраструктуру

Terraform хорошо подходит для provisioning.

Например:

- VPC;

- EC2;

- RDS;

- IAM;

- Kubernetes clusters;

- cloud resources.


Он управляет lifecycle ресурсов через state.

---

### Ansible конфигурирует инфраструктуру

Ansible хорошо подходит для настройки уже существующих servers.

Например:

- установить nginx;

- настроить config;

- создать users;

- запустить service;

- разложить файлы.


---

### Terraform vs Ansible use cases

Terraform:

- cloud infrastructure;

- network;

- managed services;

- declarative provisioning;

- stateful lifecycle.


Ansible:

- configuration management;

- OS setup;

- app deploy;

- imperative automation;

- server maintenance.


---

### Использование вместе

Частый вариант:

```text
Terraform создает servers/cloud resources
        ↓
Ansible настраивает OS и приложения
```

Например:

- Terraform создал EC2;

- Ansible поставил nginx и application config.


---

## 14. Interview Questions

### Что такое IaC

IaC — подход, где инфраструктура описана кодом и хранится в version control.

Это позволяет создавать, менять и ревьюить инфраструктуру как обычный код.

---

### Terraform vs Ansible

Terraform в основном создает инфраструктуру.

Ansible в основном конфигурирует серверы и приложения.

```text
Terraform -> provision
Ansible   -> configure
```

---

### Declarative vs Imperative

Declarative — описываем желаемое состояние.

Imperative — описываем конкретные шаги.

Terraform больше declarative, Ansible чаще описывает последовательность tasks.

---

### Что такое Terraform state

Terraform state — файл состояния, где Terraform хранит связь между кодом и реальными ресурсами.

Без state Terraform не понимает, чем он управляет.

---

### Зачем нужен backend

Backend нужен, чтобы хранить Terraform state.

Для команды обычно используют remote backend с locking.

Например:

- S3 + DynamoDB;

- Terraform Cloud;

- GCS;

- Azure Storage.


---

### Что делает `terraform plan`

`terraform plan` показывает будущие изменения.

Например:

- что будет создано;

- что изменится;

- что удалится.


Он не применяет изменения.

---

### Что такое provider

Provider — плагин Terraform для работы с API конкретной платформы.

Например:

- AWS provider;

- Kubernetes provider;

- Helm provider;

- Cloudflare provider.


---

### Что такое resource

Resource — объект инфраструктуры, которым управляет Terraform.

Например:

- EC2 instance;

- VPC;

- Security Group;

- Kubernetes namespace.


---

### Что такое Ansible inventory

Inventory — список managed hosts и групп.

Пример:

```ini
[web]
web1
web2
```

Ansible использует inventory, чтобы знать, куда подключаться.

---

### Что такое playbook

Playbook — YAML-файл с описанием задач Ansible.

Он говорит:

- на каких hosts запускаться;

- какие tasks выполнить;

- какие roles применить.


---

### Что такое idempotency

Idempotency — повторный запуск приводит к тому же состоянию и не делает лишних изменений.

Это важно для безопасной автоматизации.

---

### Почему Ansible не требует агента

Ansible подключается к managed hosts по SSH и выполняет modules удаленно.

Поэтому на серверах не нужен отдельный постоянный agent.