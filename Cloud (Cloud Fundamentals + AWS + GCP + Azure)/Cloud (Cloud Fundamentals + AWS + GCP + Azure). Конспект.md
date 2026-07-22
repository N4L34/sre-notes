# Cloud

## 1. Cloud Fundamentals

### Что такое Cloud Computing

Cloud Computing — аренда вычислительных ресурсов через интернет.

Можно арендовать: 

- серверы;
    
- сети;
    
- диски;
    
- базы данных;
    
- Kubernetes;
    
- monitoring;
    
- serverless.
    

Идея: не покупать железо самому, а использовать ресурсы cloud provider.

---

### IaaS

IaaS — Infrastructure as a Service.

Ты арендуешь базовую инфраструктуру:

```text
VM
disk
network
load balancer
firewall
```

Пример: EC2, Compute Engine, Azure VM.

---

### PaaS

PaaS — Platform as a Service.

Cloud provider управляет платформой, а ты деплоишь приложение.

Примеры:

- managed database;
    
- app platform;
    
- serverless runtime;
    
- managed Kubernetes частично.
    

Меньше контроля, но меньше операционной работы.

---

### SaaS

SaaS — Software as a Service.

Готовое приложение как сервис.

Примеры:

- Gmail;
    
- GitHub;
    
- Slack;
    
- Google Docs.
    

Ты просто используешь продукт, не управляешь инфраструктурой.

---

### Public / Private / Hybrid Cloud

Public Cloud — инфраструктура cloud provider, например AWS/GCP/Azure.

Private Cloud — облако внутри компании.

Hybrid Cloud — связка private infrastructure и public cloud.

```text
on-prem + AWS/GCP/Azure
```

---

### Region

Region — географический регион cloud provider.

Например:

```text
eu-central-1
us-east-1
europe-west1
westeurope
```

Region выбирают по latency, законам, цене и доступности сервисов.

---

### Availability Zone

Availability Zone — отдельная зона внутри region.

Обычно это отдельный дата-центр или группа дата-центров.

```text
Region
├── AZ-a
├── AZ-b
└── AZ-c
```

Для отказоустойчивости ресурсы размещают в нескольких AZ.

---

### High Availability

High Availability — способность системы продолжать работать при сбоях.

Например:

- несколько replicas;
    
- несколько AZ;
    
- Load Balancer;
    
- managed database failover.
    

Цель — минимизировать downtime.

---

### Scalability

Scalability — способность системы выдерживать рост нагрузки.

Бывает:

- vertical scaling — увеличить размер сервера;
    
- horizontal scaling — добавить больше серверов/реплик.
    

---

### Elasticity

Elasticity — автоматическое масштабирование вверх и вниз под нагрузку.

Например:

```text
нагрузка выросла -> больше instances
нагрузка упала -> меньше instances
```

Это помогает экономить деньги.

---

### Shared Responsibility Model

Shared Responsibility Model — разделение ответственности между cloud provider и пользователем.

Cloud provider отвечает за физическую инфраструктуру.

Пользователь отвечает за:

- настройки доступа;
    
- данные;
    
- конфигурацию;
    
- приложения;
    
- сетевые правила.
    

---

### Pay-as-you-go

Pay-as-you-go — оплата по фактическому использованию.

Например:

- сколько часов работала VM;
    
- сколько GB хранится в storage;
    
- сколько запросов пришло в сервис;
    
- сколько traffic ушло наружу.
    

---

## 2. Compute

### Virtual Machine

Virtual Machine — виртуальный сервер в cloud.

Ты получаешь:

- CPU;
    
- RAM;
    
- disk;
    
- network;
    
- OS.
    

Примеры: EC2, Compute Engine, Azure VM.

---

### Images (AMI и аналоги)

Image — шаблон для создания VM.

В AWS это AMI.

Внутри image обычно есть:

- OS;
    
- предустановленные пакеты;
    
- настройки;
    
- иногда application runtime.
    

---

### Instance Types

Instance Type — размер и тип VM.

Например:

- general purpose;
    
- compute optimized;
    
- memory optimized;
    
- storage optimized;
    
- GPU instances.
    

Выбор зависит от workload.

---

### Autoscaling

Autoscaling автоматически меняет количество instances.

Например:

- CPU высокий → добавить VM;
    
- нагрузка упала → удалить лишние VM.
    

Используется для availability и экономии.

---

### Spot / Preemptible instances

Spot/Preemptible instances — дешевые VM, которые cloud provider может забрать обратно.

Плюсы:

- дешевле.
    

Минусы:

- могут быть остановлены в любой момент.
    

Подходят для batch jobs, CI runners, stateless workloads.

---

### Serverless basics

Serverless — модель, где не нужно управлять серверами напрямую.

Пример:

- AWS Lambda;
    
- Google Cloud Functions;
    
- Azure Functions.
    

Ты загружаешь код, provider запускает его по событиям.

---

## 3. Networking

### VPC / VNet

VPC/VNet — изолированная виртуальная сеть в cloud.

AWS/GCP обычно говорят VPC.

Azure говорит Virtual Network, или VNet.

Внутри создают subnets, routes, gateways, firewall rules.

---

### Subnets

Subnet — часть IP-диапазона VPC/VNet.

Например:

```text
VPC:    10.0.0.0/16
Subnet: 10.0.1.0/24
Subnet: 10.0.2.0/24
```

Subnets часто делят на public и private.

---

### Route Tables

Route Table определяет, куда отправлять traffic.

Пример:

```text
0.0.0.0/0 -> Internet Gateway
10.0.0.0/16 -> local
```

Без правильных routes сеть работать не будет.

---

### Internet Gateway

Internet Gateway дает VPC доступ в интернет.

Обычно нужен для public subnet.

```text
Public Subnet -> Route Table -> Internet Gateway -> Internet
```

---

### NAT Gateway

NAT Gateway позволяет private subnet выходить в интернет.

Но интернет не может напрямую подключиться обратно к private instances.

```text
Private VM -> NAT Gateway -> Internet
```

---

### Security Groups

Security Group — firewall на уровне ресурса/instance.

Обычно stateful.

Например:

- разрешить SSH 22 только с твоего IP;
    
- разрешить HTTP 80 от Load Balancer;
    
- разрешить PostgreSQL 5432 только от app servers.
    

---

### Network ACL

Network ACL — firewall на уровне subnet.

Обычно stateless.

Он проверяет inbound и outbound rules отдельно.

Используется реже, чем Security Groups.

---

### Load Balancer

Load Balancer распределяет traffic между несколькими backend instances/services.

```text
Client -> Load Balancer -> VM1/VM2/VM3
```

Нужен для high availability и масштабирования.

---

### Public vs Private Subnet

Public subnet имеет route в internet через Internet Gateway.

Private subnet не имеет прямого входящего доступа из интернета.

Обычно:

- Load Balancer — public subnet;
    
- app/db servers — private subnet.
    

---

## 4. Storage

### Object Storage

Object Storage хранит данные как objects в buckets.

Примеры:

- AWS S3;
    
- Google Cloud Storage;
    
- Azure Blob Storage.
    

Подходит для:

- backups;
    
- logs;
    
- images;
    
- static files;
    
- artifacts.
    

---

### Block Storage

Block Storage — диск для VM.

Примеры:

- AWS EBS;
    
- GCP Persistent Disk;
    
- Azure Managed Disk.
    

Подходит для filesystem, databases, VM disks.

---

### File Storage

File Storage — shared filesystem.

Примеры:

- AWS EFS;
    
- Google Filestore;
    
- Azure Files.
    

Подходит, когда нескольким серверам нужен общий filesystem.

---

### Storage Classes

Storage Classes — разные классы хранения по цене, скорости и доступности.

Например:

- hot storage;
    
- cool storage;
    
- archive;
    
- standard;
    
- infrequent access.
    

Чем дешевле хранение, тем дороже/медленнее доступ.

---

### Snapshots

Snapshot — снимок диска или volume.

Используется для:

- backup;
    
- restore;
    
- clone;
    
- миграций.
    

Важно помнить: snapshot — не всегда полноценная backup-стратегия сам по себе.

---

### Lifecycle Policies

Lifecycle Policy автоматически перемещает или удаляет данные.

Например:

```text
через 30 дней -> cheaper storage
через 365 дней -> archive
через 730 дней -> delete
```

Помогает экономить на storage.

---

## 5. Identity

### IAM

IAM — Identity and Access Management.

Управляет тем:

- кто имеет доступ;
    
- к каким ресурсам;
    
- какие действия разрешены.
    

Это один из самых важных security-компонентов в cloud.

---

### Users

User — identity для человека или иногда сервиса.

Сейчас для automation лучше чаще использовать roles/service accounts, а не long-lived users.

---

### Groups

Group — группа пользователей с общими правами.

Например:

- developers;
    
- admins;
    
- read-only;
    
- billing.
    

Права проще назначать группе, чем каждому user отдельно.

---

### Roles

Role — identity с набором permissions, которую можно временно принять.

Используется для:

- VM;
    
- Kubernetes workloads;
    
- CI/CD;
    
- cross-account access;
    
- services.
    

Roles обычно безопаснее long-lived access keys.

---

### Policies

Policy — документ с правилами доступа.

Например:

```text
Allow read from bucket
Deny delete database
Allow create logs
```

Policy прикрепляют к users, groups или roles.

---

### Least Privilege

Least Privilege — давать минимально необходимые права.

Плохо:

```text
admin access everywhere
```

Хорошо:

```text
только read конкретного bucket
только deploy в конкретный namespace
```

---

## 6. Monitoring

### Cloud Monitoring

Cloud Monitoring — сервис provider для метрик, логов и alerting.

Примеры:

- AWS CloudWatch;
    
- Google Cloud Monitoring;
    
- Azure Monitor.
    

---

### Metrics

Metrics — числовые показатели системы.

Примеры:

- CPU usage;
    
- memory usage;
    
- disk I/O;
    
- network traffic;
    
- request count;
    
- error rate.
    

---

### Logs

Logs — текстовые события от приложений и инфраструктуры.

Примеры:

- application logs;
    
- system logs;
    
- load balancer logs;
    
- audit logs.
    

Logs помогают расследовать инциденты.

---

### Alerts

Alerts уведомляют, когда что-то пошло не так.

Например:

- CPU > 90%;
    
- disk почти заполнен;
    
- 5xx выросли;
    
- instance down;
    
- database connections high.
    

---

### Audit Logs

Audit Logs показывают, кто и что сделал в cloud.

Например:

- кто удалил VM;
    
- кто изменил IAM policy;
    
- кто открыл security group;
    
- кто создал access key.
    

Очень важно для security и расследований.

---

## 7. Cloud Services

## AWS

### EC2

EC2 — виртуальные машины в AWS.

Используется для запуска серверов, приложений, worker'ов, bastion hosts и т.д.

---

### S3

S3 — object storage в AWS.

Используется для:

- backups;
    
- static files;
    
- logs;
    
- artifacts;
    
- data lake.
    

---

### VPC

VPC — изолированная сеть в AWS.

Внутри:

- subnets;
    
- route tables;
    
- internet gateway;
    
- NAT gateway;
    
- security groups;
    
- NACL.
    

---

### IAM

AWS IAM управляет доступами к AWS resources.

Основные сущности:

- users;
    
- groups;
    
- roles;
    
- policies.
    

---

### EBS

EBS — block storage для EC2 instances.

Это диск, который можно подключить к VM.

Подходит для filesystem, database storage и boot volumes.

---

### RDS

RDS — managed relational database service.

Поддерживает, например:

- PostgreSQL;
    
- MySQL;
    
- MariaDB;
    
- SQL Server;
    
- Oracle.
    

AWS управляет backups, patching, failover и maintenance.

---

### ELB

ELB — Elastic Load Balancing.

Основные типы:

- Application Load Balancer;
    
- Network Load Balancer;
    
- Gateway Load Balancer.
    

Распределяет traffic между targets.

---

### Auto Scaling

Auto Scaling автоматически меняет количество EC2 instances.

Например, в Auto Scaling Group.

```text
load high -> add instances
load low -> remove instances
```

---

### CloudWatch

CloudWatch — monitoring/logging сервис AWS.

Используется для:

- metrics;
    
- logs;
    
- alarms;
    
- dashboards;
    
- events.
    

---

## GCP

### Compute Engine

Compute Engine — виртуальные машины в Google Cloud.

Аналог AWS EC2 и Azure Virtual Machines.

---

### Cloud Storage

Cloud Storage — object storage в GCP.

Аналог AWS S3 и Azure Blob Storage.

---

### VPC

GCP VPC — виртуальная сеть.

Особенность GCP: VPC глобальная, а subnets региональные.

---

### IAM

GCP IAM управляет доступами.

Основные идеи:

- principals;
    
- roles;
    
- policies;
    
- service accounts.
    

---

### Cloud SQL

Cloud SQL — managed relational database service в GCP.

Поддерживает PostgreSQL, MySQL и SQL Server.

---

### Cloud Monitoring

Cloud Monitoring — monitoring-сервис в Google Cloud.

Используется для metrics, dashboards, alerting и observability.

---

## Azure

### Virtual Machines

Azure Virtual Machines — виртуальные машины в Azure.

Аналог EC2 и Compute Engine.

---

### Blob Storage

Blob Storage — object storage в Azure.

Подходит для backups, logs, static files, images, artifacts.

---

### Virtual Network

Azure Virtual Network, или VNet — виртуальная сеть в Azure.

Внутри создают subnets, route tables, NSG и другие сетевые компоненты.

---

### Azure AD / IAM

Azure AD сейчас называется Microsoft Entra ID.

Это identity platform для пользователей, групп, приложений и access management.

Для доступа к Azure resources также используется Azure RBAC.

---

### Azure SQL

Azure SQL — managed SQL database service.

Подходит для приложений, которым нужна managed relational database.

---

### Azure Monitor

Azure Monitor — monitoring и observability сервис Azure.

Собирает metrics, logs, alerts и diagnostic data.

---

## 8. Соответствие сервисов

### EC2 ↔ Compute Engine ↔ Azure VM

Все три сервиса — виртуальные машины.

```text
AWS EC2
GCP Compute Engine
Azure Virtual Machines
```

Используются для запуска серверов и приложений.

---

### S3 ↔ Cloud Storage ↔ Blob Storage

Все три сервиса — object storage.

```text
AWS S3
GCP Cloud Storage
Azure Blob Storage
```

Используются для файлов, backups, logs и artifacts.

---

### IAM ↔ IAM ↔ Azure AD

AWS IAM и GCP IAM управляют доступами к cloud resources.

Azure AD, сейчас Microsoft Entra ID, отвечает за identity, а Azure RBAC — за права на Azure resources.

---

### ELB ↔ Cloud Load Balancing ↔ Azure Load Balancer

Все это load balancing сервисы.

```text
AWS ELB
GCP Cloud Load Balancing
Azure Load Balancer
```

Но детали и типы балансировщиков отличаются у каждого provider.

---

### CloudWatch ↔ Cloud Monitoring ↔ Azure Monitor

Все три сервиса используются для monitoring/logging.

```text
AWS CloudWatch
GCP Cloud Monitoring
Azure Monitor
```

Они помогают смотреть metrics, logs, alerts и dashboards.

---

## 9. Практика

### Создать VM

Практика:

- выбрать image;
    
- выбрать instance type;
    
- выбрать network/subnet;
    
- настроить SSH;
    
- запустить VM.
    

После создания проверить доступ по SSH.

---

### Создать VPC

Практика:

- создать VPC/VNet;
    
- задать CIDR;
    
- создать subnets;
    
- настроить route tables;
    
- добавить internet/NAT gateway при необходимости.
    

---

### Настроить Security Group

Практика:

- разрешить SSH только со своего IP;
    
- разрешить HTTP/HTTPS от Load Balancer;
    
- закрыть все лишние порты.
    

Главная идея — минимально нужный доступ.

---

### Подключиться по SSH

Обычно нужно:

- public/private key;
    
- public IP или bastion;
    
- открытый порт 22;
    
- правильный user.
    

Пример:

```bash
ssh ubuntu@<public-ip>
```

---

### Создать Object Storage Bucket

Практика:

- создать bucket;
    
- загрузить файл;
    
- настроить access;
    
- включить versioning/lifecycle при необходимости.
    

Важно не делать bucket публичным без причины.

---

### Создать Load Balancer

Практика:

- создать backend instances;
    
- настроить health check;
    
- создать target group/backend pool;
    
- создать listener;
    
- проверить traffic.
    

---

### Настроить IAM Role

Практика:

- создать role;
    
- прикрепить минимальную policy;
    
- назначить role VM/service;
    
- проверить доступ.
    

Не использовать long-lived keys, если можно использовать role.

---

### Посмотреть Cloud Metrics

Практика:

- открыть monitoring;
    
- посмотреть CPU/network/disk;
    
- создать dashboard;
    
- настроить alert.
    

Метрики помогают понять состояние сервиса.

---

## 10. Interview Questions

### Что такое Cloud Computing

Cloud Computing — модель, где инфраструктура и сервисы арендуются через интернет.

Ты используешь compute, storage, network и managed services без покупки своего железа.

---

### IaaS vs PaaS vs SaaS

IaaS — аренда инфраструктуры: VM, disks, networks.

PaaS — платформа для приложения: managed runtime/database.

SaaS — готовое приложение: Gmail, GitHub, Slack.

---

### Region vs Availability Zone

Region — географический регион.

Availability Zone — отдельная зона внутри region.

Для high availability ресурсы размещают в нескольких AZ.

---

### Public vs Private Subnet

Public subnet имеет маршрут в интернет через Internet Gateway.

Private subnet не доступна напрямую из интернета.

Обычно public — для Load Balancer, private — для app/db.

---

### Security Group vs NACL

Security Group — firewall на уровне instance/resource, обычно stateful.

NACL — firewall на уровне subnet, обычно stateless.

Security Groups используются чаще.

---

### Object vs Block Storage

Object Storage хранит объекты в buckets.

Block Storage — диск для VM.

```text
Object -> S3/Cloud Storage/Blob
Block  -> EBS/Persistent Disk/Managed Disk
```

---

### Что такое IAM

IAM — система управления доступами.

Она определяет:

- кто имеет доступ;
    
- к каким ресурсам;
    
- какие действия разрешены.
    

---

### Что такое Load Balancer

Load Balancer распределяет traffic между несколькими backend'ами.

Он помогает с:

- high availability;
    
- scaling;
    
- health checks;
    
- отказоустойчивостью.
    

---

### EC2 vs Docker vs Kubernetes

EC2 — виртуальная машина.

Docker — запуск приложения в контейнере.

Kubernetes — оркестратор контейнеров.

```text
EC2 -> server
Docker -> container
Kubernetes -> manages containers
```

---

### Какие аналоги EC2 есть в GCP и Azure

Аналоги EC2:

```text
AWS EC2
GCP Compute Engine
Azure Virtual Machines
```

Все это сервисы виртуальных машин.