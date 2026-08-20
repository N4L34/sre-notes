# Security, Secrets and Access

## 1. Основы безопасности

### CIA triad

CIA triad — три базовых свойства информационной безопасности:

```text
Confidentiality
Integrity
Availability
```

Система должна защищать данные от чтения, изменения и недоступности.

---

### Confidentiality

Confidentiality — конфиденциальность данных.

Данные должны быть доступны только авторизованным пользователям и системам.

Механизмы:

- authentication;

- access control;

- encryption;

- masking;

- network segmentation.


---

### Integrity

Integrity — целостность данных.

Данные не должны быть незаметно изменены или повреждены.

Механизмы:

- checksums;

- digital signatures;

- audit logs;

- version control;

- access control.


---

### Availability

Availability — доступность системы и данных.

Сервис должен работать тогда, когда он нужен пользователям.

Механизмы:

- redundancy;

- backups;

- failover;

- monitoring;

- DDoS protection;

- disaster recovery.


---

### Threat

Threat — потенциальная причина ущерба.

Примеры:

- злоумышленник;

- malware;

- утечка credentials;

- DDoS;

- ошибка администратора;

- пожар в дата-центре.


---

### Vulnerability

Vulnerability — слабое место системы.

Примеры:

- устаревшая библиотека;

- открытый порт;

- слабый пароль;

- неправильная IAM policy;

- container под root.


Vulnerability еще не означает, что система уже взломана.

---

### Exploit

Exploit — способ или код, использующий vulnerability.

```text
Vulnerability -> Exploit -> Unauthorized action
```

Например, специально сформированный запрос может использовать ошибку в web-приложении.

---

### Risk

Risk — вероятность того, что threat использует vulnerability, умноженная на последствия.

Упрощенно:

```text
Risk = likelihood × impact
```

Высокий CVSS не всегда означает максимальный бизнес-риск: важен контекст системы.

---

### Attack surface

Attack surface — все точки, через которые систему можно атаковать.

Примеры:

- открытые порты;

- API endpoints;

- SSH;

- cloud console;

- dependencies;

- CI/CD;

- сотрудники;

- публичные buckets.


Чем меньше attack surface, тем проще защищать систему.

---

### Defense in Depth

Defense in Depth — несколько независимых уровней защиты.

Например:

```text
Firewall
  ↓
Authentication
  ↓
RBAC
  ↓
NetworkPolicy
  ↓
Application authorization
  ↓
Audit logs
```

Если один слой обойдут, остальные продолжают защищать систему.

---

### Zero Trust basics

Zero Trust — подход “никому не доверять автоматически”.

Основные идеи:

- всегда проверять identity;

- минимальные права;

- короткоживущие credentials;

- проверять контекст запроса;

- сегментировать сеть;

- постоянно логировать доступ.


Нахождение внутри корпоративной сети само по себе не дает доверия.

---

### Shared Responsibility Model

Shared Responsibility Model разделяет ответственность между provider и клиентом.

Например, cloud provider отвечает за:

- физический дата-центр;

- hardware;

- базовую cloud platform.


Клиент отвечает за:

- IAM;

- данные;

- network rules;

- приложения;

- secrets;

- конфигурацию ресурсов.


---

## 2. Authentication и Authorization

### Authentication

Authentication отвечает на вопрос:

```text
Кто ты?
```

Примеры:

- пароль;

- SSH key;

- certificate;

- token;

- biometric factor.


---

### Authorization

Authorization отвечает на вопрос:

```text
Что тебе разрешено?
```

Например:

- можно читать Pods;

- нельзя удалять Secrets;

- можно просматривать bucket;

- нельзя изменять IAM.


---

### Authentication vs Authorization

Authentication подтверждает identity.

Authorization проверяет permissions.

```text
Authentication -> user is Alice
Authorization  -> Alice may read logs
```

Сначала обычно проходит authentication, потом authorization.

---

### Identification

Identification — заявление о своей identity.

Например пользователь вводит:

```text
alice@example.com
```

После этого система должна подтвердить, что это действительно Alice.

---

### Accounting

Accounting — учет действий пользователя или системы.

Примеры:

- кто вошел;

- кто изменил policy;

- кто скачал secret;

- когда выполнялась операция.


Обычно реализуется через audit logs.

---

### MFA

MFA — Multi-Factor Authentication.

Используются несколько разных факторов:

- что пользователь знает: пароль;

- чем владеет: телефон или security key;

- чем является: биометрия.


MFA сильно снижает риск от украденного пароля.

---

### SSO

SSO — Single Sign-On.

Пользователь один раз входит через Identity Provider и получает доступ к нескольким системам.

Примеры IdP:

- Microsoft Entra ID;

- Okta;

- Keycloak;

- Google Workspace.


---

### Session-based authentication

При session-based authentication сервер хранит сессию.

```text
Login -> session created -> session cookie -> requests
```

Клиент обычно передает session ID в cookie.

Сессия может храниться в Redis или database.

---

### Token-based authentication

При token-based authentication клиент передает token с каждым запросом.

```http
Authorization: Bearer <token>
```

Сервер проверяет token и решает, разрешать ли запрос.

---

### API keys

API key — строка, идентифицирующая приложение или клиента API.

Плюсы:

- просто использовать;

- удобно для automation.


Минусы:

- часто long-lived;

- может не иметь user context;

- легко утечь.


API keys нужно ограничивать по правам, сроку и источнику.

---

### Basic Authentication

Basic Authentication передает username и password в HTTP header.

```http
Authorization: Basic base64(username:password)
```

Base64 не является шифрованием, поэтому Basic Auth должен использоваться только через HTTPS.

---

### OAuth 2.0 basics

OAuth 2.0 — framework для delegated authorization.

Он позволяет приложению получить ограниченный доступ от имени пользователя без передачи приложению пароля пользователя.

Основные сущности:

- Resource Owner;

- Client;

- Authorization Server;

- Resource Server;

- Access Token.


---

### OpenID Connect basics

OpenID Connect, или OIDC — слой authentication поверх OAuth 2.0.

OAuth отвечает про доступ, OIDC добавляет информацию о пользователе.

Ключевой объект — ID Token.

---

### JWT basics

JWT — JSON Web Token.

Обычно состоит из:

```text
header.payload.signature
```

JWT часто содержит claims:

- subject;

- issuer;

- audience;

- expiration;

- roles.


JWT обычно подписан, но не зашифрован: payload можно прочитать.

---

## 3. Access Control

### Least Privilege

Least Privilege — выдавать только минимально необходимые права.

Пример:

```text
Плохо:  AdministratorAccess
Хорошо: Read конкретного S3 bucket
```

Права нужно периодически пересматривать.

---

### Need-to-know

Need-to-know — доступ выдается только к информации, необходимой для конкретной работы.

Например, разработчику приложения может быть не нужен доступ к payroll database.

---

### Deny by default

Deny by default — если доступ явно не разрешен, он запрещен.

```text
Default: deny
Explicit rule: allow
```

Это безопаснее модели, где все разрешено по умолчанию.

---

### RBAC

RBAC — Role-Based Access Control.

Права выдаются через роли.

```text
User -> Role -> Permissions
```

Пример:

- Viewer;

- Developer;

- Administrator;

- DatabaseOperator.


---

### ABAC

ABAC — Attribute-Based Access Control.

Решение принимается по атрибутам:

- user department;

- resource environment;

- time;

- location;

- device state.


ABAC гибче RBAC, но сложнее в поддержке.

---

### ACL

ACL — Access Control List.

Это список того, кому какие действия разрешены для конкретного объекта.

Например:

```text
alice: read
bob: read, write
group-admins: full control
```

---

### Policy-based access

Policy-based access управляет доступом через декларативные policies.

Policy может учитывать:

- identity;

- resource;

- action;

- conditions;

- network;

- tags.


Примеры: AWS IAM policies и admission policies.

---

### Separation of Duties

Separation of Duties — разделение критических действий между несколькими людьми или ролями.

Например:

- один человек создает payment;

- другой подтверждает;

- третий проводит audit.


Это уменьшает риск злоупотреблений и ошибок.

---

### Privilege Escalation

Privilege Escalation — получение более высоких прав.

Бывает:

- vertical — user становится root/admin;

- horizontal — user получает доступ к данным другого user.


В контейнерах privilege escalation ограничивают через:

```yaml
allowPrivilegeEscalation: false
```

---

### Temporary access

Temporary access — доступ на ограниченное время.

Например:

- роль на 1 час;

- JIT access;

- temporary cloud credentials;

- временный SSH certificate.


Это безопаснее постоянных прав.

---

### Access review

Access review — периодическая проверка прав пользователей и сервисов.

Проверяют:

- нужны ли права;

- не слишком ли они широкие;

- активен ли пользователь;

- используются ли credentials;

- есть ли unused roles.


---

### Offboarding пользователей

При увольнении или смене роли нужно:

- отключить account;

- отозвать sessions и tokens;

- удалить SSH keys;

- убрать из groups;

- заблокировать VPN;

- передать ownership ресурсов;

- проверить shared credentials.


Offboarding должен быть автоматизирован.

---

## 4. IAM

### Что такое IAM

IAM — Identity and Access Management.

IAM управляет:

- identities;

- authentication;

- roles;

- permissions;

- policies;

- audit.


---

### Users

User — identity человека или legacy automation.

Для людей лучше использовать SSO/federation.

Для приложений лучше использовать roles или service accounts, а не постоянные user keys.

---

### Groups

Group объединяет пользователей с одинаковыми правами.

Например:

- developers;

- security;

- billing;

- read-only.


Права проще назначать group, чем каждому user отдельно.

---

### Roles

Role — набор permissions, который identity может временно получить.

Примеры:

- EC2 role;

- Kubernetes role;

- CI deploy role;

- cross-account role.


Role обычно не имеет постоянного пароля.

---

### Policies

Policy описывает разрешенные или запрещенные действия.

Примерная логика:

```text
Effect: Allow
Action: s3:GetObject
Resource: bucket/app/*
```

Policies должны быть максимально узкими.

---

### Service Accounts

Service Account — identity для приложения или workload.

Примеры:

- Kubernetes ServiceAccount;

- GCP Service Account;

- CI/CD service identity.


Service Account не должен использоваться людьми для обычной работы.

---

### Machine Identity

Machine Identity — identity для сервера, контейнера, Pod, pipeline или приложения.

Она нужна, чтобы машина могла безопасно обращаться к:

- API;

- database;

- cloud;

- secrets manager;

- Kubernetes API.


---

### Role assumption

Role assumption — временное получение прав роли.

Например:

```text
CI identity -> assume DeployRole -> temporary credentials
```

Это позволяет не хранить постоянные access keys.

---

### Trust policy

Trust policy определяет, кто может принять роль.

Например:

- конкретный AWS account;

- service;

- CI provider;

- Kubernetes workload identity.


Trust policy не определяет, что роль может делать после assumption.

---

### Permission policy

Permission policy определяет, какие действия разрешены роли.

```text
Trust policy      -> кто может стать ролью
Permission policy -> что роль может делать
```

---

### Explicit Allow и Deny

Explicit Allow разрешает действие.

Explicit Deny запрещает действие и обычно имеет приоритет над Allow.

```text
Explicit Deny > Explicit Allow > Default Deny
```

---

### Long-lived vs Short-lived credentials

Long-lived credentials действуют долго:

- static API key;

- постоянный password;

- access key.


Short-lived credentials имеют ограниченный TTL:

- temporary token;

- role session;

- certificate;

- Vault lease.


Short-lived credentials безопаснее.

---

### Federation basics

Federation позволяет использовать внешнего Identity Provider для доступа к системе.

Пример:

```text
Corporate IdP -> OIDC/SAML -> Cloud Role
```

Пользователю не нужен отдельный постоянный cloud password.

---

## 5. Secrets Management

### Что такое Secret

Secret — чувствительное значение, которое дает доступ или используется для криптографии.

Примеры:

- password;

- API token;

- private key;

- certificate key;

- database credential;

- encryption key.


---

### Пароли

Пароль используется для authentication.

Требования:

- не хранить в plaintext;

- использовать password manager;

- включать MFA;

- хранить password hashes, а не исходные пароли;

- не переиспользовать.


---

### API tokens

API token дает программный доступ к API.

Нужно ограничивать:

- permissions;

- expiration;

- allowed scopes;

- source network;

- environment.


---

### Private keys

Private key — секретная часть asymmetric cryptography.

Используется для:

- SSH;

- TLS;

- signing;

- authentication.


Private key нельзя передавать или хранить в открытом виде.

---

### Certificates

Certificate содержит public key и информацию об identity.

Сам certificate обычно не является secret.

Секретом является связанный private key.

---

### Database credentials

Database credentials:

- username;

- password;

- client certificate;

- token.


Лучше выдавать отдельные credentials каждому приложению с минимальными правами.

---

### Encryption keys

Encryption key используется для шифрования и расшифровки данных.

Ключи должны:

- храниться отдельно от данных;

- ротироваться;

- иметь контроль доступа;

- логировать использование.


---

### Secrets lifecycle

Secrets lifecycle включает:

```text
creation
distribution
usage
rotation
expiration
revocation
deletion
```

Нужно управлять secret на всех этапах.

---

### Secret rotation

Secret rotation — замена secret новым.

Правильная rotation обычно:

- создает новую версию;

- обновляет consumers;

- проверяет работу;

- отзывает старую версию.


---

### Secret expiration

Secret expiration — автоматическое прекращение действия после определенного времени.

Это ограничивает последствия утечки.

---

### Secret revocation

Secret revocation — немедленное отключение secret.

Используется при:

- утечке;

- увольнении;

- подозрительной активности;

- компрометации системы.


---

### Dynamic secrets

Dynamic secrets создаются автоматически на короткое время.

Например Vault может создать временного database user на один час.

После lease credential удаляется или блокируется.

---

### Secret versioning

Secret versioning хранит несколько версий secret.

Это помогает:

- безопасно ротировать;

- откатываться;

- управлять переходным периодом.


Старые версии нужно своевременно отключать.

---

### Centralized secrets storage

Centralized secrets storage — единое защищенное хранилище secrets.

Плюсы:

- audit;

- rotation;

- access policies;

- versioning;

- encryption;

- единая точка управления.


Примеры: Vault и cloud secret managers.

---

## 6. Хранение Secrets

### Почему нельзя хранить secrets в Git

Git хранит полную историю.

Даже после удаления secret может остаться:

- в старом commit;

- fork;

- clone;

- CI cache;

- backup.


Если secret попал в Git, его нужно отозвать и заменить.

---

### Почему нельзя хранить secrets в Docker image

Docker image состоит из layers.

Даже если удалить файл в следующем layer, secret может остаться в предыдущем.

Image также может попасть в registry или cache.

---

### Почему нельзя хранить secrets в логах

Логи:

- читают многие люди;

- долго хранятся;

- копируются в разные системы;

- попадают в backups;

- могут индексироваться.


Secret в логах нужно считать скомпрометированным.

---

### Environment variables для secrets

Environment variables часто используют для передачи secrets.

Плюсы:

- просто;

- не нужно записывать файл.


Минусы:

- могут попасть в process dump;

- debug output;

- `/proc`;

- crash report;

- конфигурацию workload.


Для особо чувствительных secrets лучше file mount или runtime retrieval.

---

### Файлы с secrets

Secrets можно передавать приложению через файлы.

Плюсы:

- можно ограничить permissions;

- проще обновлять mounted secret;

- не попадают в environment dump.


Пример пути:

```text
/run/secrets/db-password
```

---

### Права доступа к secret-файлам

Secret-файл должен быть доступен только нужному user.

```bash
chmod 600 secret.txt
chown app:app secret.txt
```

Directory тоже должна иметь ограниченные permissions.

---

### Encryption at rest

Encryption at rest — шифрование сохраненных данных.

Например:

- encrypted disk;

- encrypted database;

- encrypted object storage;

- encrypted etcd.


Оно защищает данные при краже storage или backup.

---

### Encryption in transit

Encryption in transit — шифрование данных при передаче.

Обычно используется TLS.

```text
Client --TLS--> Server
```

Защищает от перехвата и изменения traffic.

---

### Secret Manager

Secret Manager — специализированный сервис для хранения secrets.

Обычно поддерживает:

- encryption;

- IAM;

- versioning;

- rotation;

- audit logs;

- API access.


---

### HashiCorp Vault basics

Vault — централизованный secrets manager.

Он умеет:

- KV secrets;

- dynamic credentials;

- leases;

- encryption as a service;

- PKI;

- authentication methods;

- policies.


---

### AWS Secrets Manager basics

AWS Secrets Manager хранит и версионирует secrets.

Поддерживает:

- IAM access;

- KMS encryption;

- rotation;

- audit через CloudTrail;

- integration с AWS services.


---

### GCP Secret Manager basics

GCP Secret Manager хранит secret versions.

Доступ контролируется через GCP IAM.

Использование secrets логируется через cloud audit logs.

---

### Azure Key Vault basics

Azure Key Vault хранит:

- secrets;

- keys;

- certificates.


Доступ управляется через Azure RBAC или access policies.

---

## 7. HashiCorp Vault

### Vault architecture

Основные элементы Vault:

```text
Clients
  ↓
Vault API
  ↓
Auth methods + Policies
  ↓
Secret engines
  ↓
Encrypted storage backend
```

Vault шифрует данные перед записью в storage.

---

### Seal и Unseal

В sealed состоянии Vault не может расшифровать данные.

Unseal предоставляет Vault ключи, необходимые для доступа к master encryption key.

В production часто используют auto-unseal через KMS/HSM.

---

### Authentication methods

Auth methods позволяют clients войти в Vault.

Примеры:

- token;

- AppRole;

- Kubernetes;

- LDAP;

- OIDC;

- cloud IAM;

- TLS certificate.


---

### Policies

Vault policies определяют доступ к paths.

Примерная идея:

```text
path "secret/data/app/*" {
  capabilities = ["read"]
}
```

По умолчанию доступ запрещен.

---

### Tokens

Vault token подтверждает identity и несет policies.

Tokens могут быть:

- renewable;

- short-lived;

- periodic;

- orphan;

- root.


Root token нельзя использовать для обычной работы.

---

### Leases

Lease — срок действия выданного secret.

Vault отслеживает lease и может:

- продлить;

- отозвать;

- автоматически завершить.


Особенно полезно для dynamic credentials.

---

### Dynamic secrets

Vault создает credentials по запросу.

Например:

```text
App requests DB secret
Vault creates temporary DB user
TTL expires
Vault deletes user
```

---

### Secret engines

Secret engine — компонент Vault, работающий с определенным типом secrets.

Примеры:

- KV;

- Database;

- Transit;

- PKI;

- Cloud credentials.


---

### KV secrets engine

KV engine хранит обычные key-value secrets.

Пример:

```text
secret/data/prod/api
```

KV v2 поддерживает versioning.

---

### Database secrets engine

Database engine создает временные database credentials.

Vault подключается к database с административными правами и создает users по шаблону.

---

### Transit secrets engine

Transit engine выполняет cryptographic operations, но не хранит пользовательские данные.

Он умеет:

- encrypt;

- decrypt;

- sign;

- verify;

- rotate keys.


Приложение отправляет данные Vault и получает ciphertext.

---

### Secret rotation

Vault может помогать ротировать:

- root database credentials;

- encryption keys;

- dynamic credentials;

- certificates.


Для static secrets rotation часто требует внешней автоматизации.

---

### High Availability basics

Vault HA использует несколько instances.

Обычно:

- один active node;

- остальные standby;

- shared storage;

- load balancer;

- auto-unseal.


При падении active одна standby становится active.

---

## 8. Cryptography Basics

### Encryption

Encryption преобразует plaintext в ciphertext с использованием ключа.

```text
plaintext + key -> ciphertext
```

Процесс обратим при наличии правильного ключа.

---

### Decryption

Decryption преобразует ciphertext обратно в plaintext.

```text
ciphertext + key -> plaintext
```

---

### Hashing

Hashing преобразует данные в hash фиксированного размера.

```text
input -> hash
```

Hashing должен быть односторонним.

Используется для:

- password storage;

- integrity;

- checksums;

- content addressing.


---

### Encoding

Encoding меняет представление данных, но не защищает их.

Примеры:

- Base64;

- URL encoding;

- UTF-8.


Base64 легко декодируется и не является encryption.

---

### Encryption vs Hashing

Encryption обратимо с ключом.

Hashing должен быть необратимым.

```text
Encryption -> confidentiality
Hashing    -> integrity/password verification
```

---

### Symmetric encryption

Symmetric encryption использует один и тот же secret key для encryption и decryption.

Плюсы:

- быстро;

- подходит для больших объемов данных.


Пример алгоритма: AES.

---

### Asymmetric encryption

Asymmetric encryption использует пару ключей:

- public key;

- private key.


Применяется в:

- TLS;

- SSH;

- digital signatures;

- key exchange.


---

### Public key

Public key можно распространять.

Он используется для:

- проверки signature;

- encryption для владельца private key;

- authentication.


---

### Private key

Private key должен храниться в секрете.

Используется для:

- signing;

- decryption;

- authentication.


Компрометация private key требует немедленной rotation.

---

### Digital signature

Digital signature подтверждает:

- авторство;

- целостность;

- подлинность данных.


Упрощенно:

```text
Private key -> sign
Public key  -> verify
```

---

### Checksums

Checksum помогает обнаружить случайное изменение данных.

Пример:

```bash
sha256sum file.iso
```

Обычная checksum без подписи не подтверждает, кто создал файл.

---

### Salt

Salt — случайное значение, добавляемое перед hashing пароля.

Оно защищает от:

- rainbow tables;

- одинаковых hashes для одинаковых паролей.


Для каждого пароля нужен уникальный salt.

---

### Key rotation

Key rotation — замена cryptographic key.

Обычно:

- создается новая версия key;

- новые данные шифруются новым key;

- старые keys временно сохраняются для decryption;

- старые данные при необходимости перешифровываются.


---

### KMS basics

KMS — Key Management Service.

Он управляет encryption keys:

- создание;

- rotation;

- access control;

- audit;

- encrypt/decrypt operations.


Обычно ключи не выдаются приложению напрямую.

---

## 9. TLS и Certificates

### TLS

TLS защищает данные при передаче.

Он обеспечивает:

- encryption;

- integrity;

- server authentication;

- опционально client authentication.


---

### HTTPS

HTTPS — HTTP поверх TLS.

```text
HTTP + TLS = HTTPS
```

Обычно используется TCP port 443.

---

### TLS handshake

Упрощенный TLS handshake:

```text
ClientHello
ServerHello + certificate
Certificate verification
Key agreement
Encrypted connection
```

После handshake application traffic шифруется.

---

### Certificate Authority

Certificate Authority, или CA — организация или система, подписывающая certificates.

Клиент доверяет certificate, если доверяет CA в его chain.

---

### Root Certificate

Root Certificate — верхний certificate цепочки доверия.

Он обычно self-signed и хранится в trust store клиента.

Root private key должен быть максимально защищен.

---

### Intermediate Certificate

Intermediate Certificate подписан root CA или другим intermediate CA.

Именно intermediate CA обычно подписывает server certificates.

Это защищает root key от частого использования.

---

### Certificate chain

Certificate chain:

```text
Server certificate
  ↓ signed by
Intermediate CA
  ↓ signed by
Root CA
```

Сервер обычно отправляет server certificate и intermediate certificates.

---

### Self-signed certificate

Self-signed certificate подписан собственным private key.

Он не доверяется публичными клиентами автоматически.

Подходит для:

- тестов;

- внутренних систем с собственным CA;

- bootstrap.


---

### CSR

CSR — Certificate Signing Request.

Он содержит:

- public key;

- subject;

- SAN;

- подпись private key.


CSR отправляется CA для выпуска certificate.

---

### SAN

SAN — Subject Alternative Name.

Он содержит домены/IP, для которых действителен certificate.

Пример:

```text
DNS: app.example.com
DNS: api.example.com
```

Современные клиенты проверяют именно SAN.

---

### SNI

SNI позволяет клиенту передать hostname во время TLS handshake.

Это позволяет держать несколько HTTPS-сайтов с разными certificates на одном IP.

---

### Certificate expiration

Certificate имеет срок действия.

После expiration клиенты начинают отклонять соединение.

Сроки нужно мониторить заранее.

---

### Certificate renewal

Certificate renewal — выпуск нового certificate до истечения старого.

Обычно процесс автоматизируют через:

- ACME;

- cert-manager;

- cloud certificate manager;

- internal PKI.


---

### mTLS

mTLS — mutual TLS.

В обычном TLS клиент проверяет сервер.

В mTLS:

- клиент проверяет сервер;

- сервер проверяет client certificate.


Используется для service-to-service authentication.

---

### TLS termination

TLS termination — расшифровка HTTPS на load balancer, ingress или reverse proxy.

```text
Client HTTPS -> Proxy -> Backend
```

Дальше traffic может идти по HTTP или HTTPS.

---

### TLS passthrough

TLS passthrough передает encrypted traffic backend без расшифровки.

```text
Client TLS -> L4 Load Balancer -> Backend TLS
```

Load balancer не видит HTTP path и headers.

---

## 10. Linux Security

### Users и Groups

Linux users представляют identities.

Groups объединяют users для назначения permissions.

Команды:

```bash
id username
getent passwd
getent group
```

---

### File permissions

Базовые permissions:

```text
r = read
w = write
x = execute
```

Права задаются для:

- owner;

- group;

- others.


---

### `chmod`

`chmod` меняет permissions.

```bash
chmod 600 private.key
chmod 755 script.sh
```

`600` означает read/write только owner.

---

### `chown`

`chown` меняет owner и group.

```bash
chown app:app /opt/app/config
```

Рекурсивно:

```bash
chown -R app:app /opt/app
```

---

### `umask`

`umask` определяет, какие permissions будут убраны у новых файлов.

```bash
umask 027
```

Чем строже umask, тем меньше прав получают новые файлы.

---

### `sudo`

`sudo` выполняет команду с правами другого user, обычно root.

```bash
sudo systemctl restart nginx
```

Права задаются в `/etc/sudoers` и `/etc/sudoers.d/`.

---

### `su`

`su` переключает user.

```bash
su - app
```

Для администрирования чаще безопаснее использовать `sudo`, потому что действия лучше аудируются.

---

### Root access

Root имеет почти неограниченные права.

Рекомендации:

- не работать постоянно под root;

- использовать sudo;

- отключать удаленный root login;

- ограничивать root credentials;

- логировать privileged actions.


---

### SUID и SGID

SUID запускает executable с правами owner файла.

SGID запускает с правами group или наследует group у directory.

Поиск SUID:

```bash
find / -perm -4000 -type f 2>/dev/null
```

Неправильные SUID binaries могут привести к privilege escalation.

---

### Linux capabilities

Capabilities разбивают root privileges на части.

Например:

```text
CAP_NET_BIND_SERVICE
CAP_SYS_ADMIN
CAP_NET_ADMIN
```

Лучше выдавать отдельную capability, чем полный root.

---

### PAM basics

PAM — Pluggable Authentication Modules.

PAM управляет authentication для:

- login;

- SSH;

- sudo;

- su;

- password policies.


Конфиги находятся в `/etc/pam.d/`.

---

### SSH hardening

Базовые меры:

- key authentication;

- отключить root login;

- отключить passwords;

- ограничить users/groups;

- использовать firewall;

- включить logging;

- регулярно обновлять OpenSSH.


---

### SELinux basics

SELinux — Mandatory Access Control система.

Она назначает security context процессам и файлам и проверяет policies.

Режимы:

- Enforcing;

- Permissive;

- Disabled.


Для диагностики лучше временно использовать Permissive, а не полностью отключать.

---

### AppArmor basics

AppArmor ограничивает приложения через профили по путям файлов и операциям.

Режимы:

- enforce;

- complain.


Чаще встречается в Ubuntu/Debian.

---

### Audit logs

Audit logs Linux могут показывать:

- login;

- sudo;

- file access;

- permission changes;

- process execution;

- SELinux denials.


---

### `auditd`

`auditd` — Linux Audit daemon.

Он записывает security events.

Пример поиска:

```bash
ausearch -m USER_LOGIN
```

Просмотр summary:

```bash
aureport
```

---

### Security updates

Security updates закрывают известные vulnerabilities.

Нужно:

- отслеживать CVE;

- регулярно обновлять packages;

- тестировать patches;

- планировать reboot при kernel updates;

- не держать EOL distributions.


---

### Fail2ban basics

Fail2ban анализирует logs и временно блокирует IP после множества неудачных попыток.

Часто используется для SSH и web authentication.

Он не заменяет MFA, firewall и безопасную конфигурацию.

---

## 11. SSH Security

### Public/private key authentication

Клиент хранит private key.

Сервер хранит public key.

Во время authentication клиент доказывает владение private key, не передавая его серверу.

---

### `authorized_keys`

Public keys разрешенных пользователей находятся в:

```text
~/.ssh/authorized_keys
```

Permissions обычно:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

### Отключение password authentication

В `sshd_config`:

```text
PasswordAuthentication no
```

После изменения:

```bash
sshd -t
systemctl reload sshd
```

Перед отключением нужно проверить вход по ключу в отдельной сессии.

---

### Отключение root login

В `sshd_config`:

```text
PermitRootLogin no
```

Администратор входит под обычным user и использует `sudo`.

---

### Ограничение SSH-доступа

Можно ограничить:

- firewall rules;

- source IP;

- VPN;

- `AllowUsers`;

- `AllowGroups`;

- bastion host;

- time-based access.


---

### Bastion Host

Bastion Host — защищенный сервер для входа во внутреннюю сеть.

```text
Admin -> Bastion -> Private Server
```

Bastion должен иметь усиленный monitoring и минимальный набор сервисов.

---

### Jump Host

Jump Host — промежуточный SSH host.

Пример:

```bash
ssh -J user@bastion user@private-host
```

Термины Bastion и Jump Host часто используются близко по смыслу.

---

### SSH Agent

SSH Agent хранит загруженные private keys в памяти и выполняет операции подписи.

```bash
ssh-add ~/.ssh/id_ed25519
```

Это позволяет не вводить passphrase при каждом подключении.

---

### Private key permissions

OpenSSH отклоняет слишком открытый private key.

Обычно:

```bash
chmod 600 ~/.ssh/id_ed25519
```

Private key желательно защищать passphrase.

---

### Host key verification

SSH host key подтверждает identity сервера.

При первом подключении key сохраняется в:

```text
~/.ssh/known_hosts
```

Изменение host key может означать переустановку сервера или MITM-атаку.

---

### Ротация SSH-ключей

Процесс:

- добавить новый public key;

- проверить доступ;

- удалить старый key;

- отозвать доступ в bastion/IAM;

- проверить authorized_keys;

- обновить automation.


---

## 12. Network Security

### Firewall

Firewall разрешает или запрещает network traffic по правилам.

Учитываются:

- source;

- destination;

- protocol;

- port;

- state.


Примеры: nftables, iptables, cloud firewall.

---

### Security Groups

Security Group — stateful firewall на уровне cloud resource.

Если входящее соединение разрешено, ответный traffic обычно разрешается автоматически.

---

### Network ACL

Network ACL обычно работает на уровне subnet и является stateless.

Inbound и outbound traffic нужно разрешать отдельно.

---

### Ingress и Egress rules

Ingress rules управляют входящим traffic.

Egress rules управляют исходящим traffic.

Egress тоже нужно ограничивать: скомпрометированный сервис не должен иметь полный доступ в интернет.

---

### Allowlist и Blocklist

Allowlist разрешает только перечисленные источники или действия.

Blocklist запрещает известные плохие источники.

Allowlist обычно безопаснее, но сложнее в поддержке.

---

### Network segmentation

Network segmentation разделяет сеть на зоны.

Например:

```text
Public
Application
Database
Management
Security
```

Компрометация одной зоны не должна давать полный доступ ко всей сети.

---

### Private networks

Private network не маршрутизируется напрямую из интернета.

Внутренние сервисы и databases лучше размещать в private network.

---

### Public и Private Subnets

Public subnet имеет route к Internet Gateway.

Private subnet не имеет прямого входящего доступа из интернета.

Обычно:

- Load Balancer — public;

- application и database — private.


---

### VPN basics

VPN создает encrypted tunnel между клиентом или сетью и private network.

Типы:

- client VPN;

- site-to-site VPN.


VPN не заменяет authentication и least privilege.

---

### Bastion Host

Bastion дает контролируемый административный доступ к private resources.

Желательно:

- MFA;

- short-lived access;

- session recording;

- no public password login;

- IP allowlist.


---

### WAF basics

WAF — Web Application Firewall.

Он анализирует HTTP traffic и может блокировать:

- SQL injection patterns;

- XSS;

- bots;

- malicious paths;

- oversized requests.


WAF не заменяет исправление vulnerabilities в приложении.

---

### DDoS protection basics

DDoS protection включает:

- CDN;

- Anycast;

- rate limiting;

- traffic scrubbing;

- autoscaling;

- upstream filtering;

- managed anti-DDoS services.


---

### Rate Limiting

Rate Limiting ограничивает число запросов.

Можно ограничивать по:

- IP;

- user;

- API key;

- endpoint;

- tenant.


Особенно важно для login и дорогих API operations.

---

### Port scanning

Port scanning ищет открытые network ports.

Для проверки своих систем:

```bash
nmap <host>
```

Неиспользуемые ports нужно закрывать.

---

### Минимизация открытых портов

Открыты должны быть только необходимые ports.

Пример:

- 443 доступен пользователям;

- 22 только через bastion;

- database port только от application subnet.


---

## 13. Container Security

### Минимальные base images

Минимальный image содержит меньше packages и utilities.

Плюсы:

- меньше vulnerabilities;

- меньший attack surface;

- меньший размер;

- быстрее download.


---

### Trusted images

Images нужно брать из доверенных registries и publishers.

Важно:

- pin versions;

- проверять digest;

- контролировать source;

- не использовать неизвестные images.


---

### Image scanning

Image scanning проверяет packages и libraries внутри image на известные vulnerabilities.

Примеры инструментов:

- Trivy;

- Grype;

- cloud registry scanners.


---

### Vulnerability scanning

Vulnerability scanning нужно выполнять:

- при build;

- при push в registry;

- периодически после публикации image.


Новые CVE могут появиться уже после сборки.

---

### Запуск не от root

Container лучше запускать от non-root user.

Dockerfile:

```dockerfile
USER 10001
```

Kubernetes:

```yaml
runAsNonRoot: true
```

---

### Read-only filesystem

Read-only root filesystem запрещает изменение файлов image.

```yaml
readOnlyRootFilesystem: true
```

Для временных данных используют отдельные writable volumes.

---

### Linux capabilities

Лучше удалить все capabilities и вернуть только нужные.

```yaml
capabilities:
  drop:
    - ALL
```

---

### Seccomp

Seccomp ограничивает syscalls.

В Kubernetes:

```yaml
seccompProfile:
  type: RuntimeDefault
```

Это уменьшает возможности процесса при компрометации.

---

### AppArmor

AppArmor profile ограничивает доступ контейнера к files, network и другим ресурсам.

Он является дополнительным слоем поверх обычных Linux permissions.

---

### Secrets в контейнерах

Нельзя встраивать secrets в image.

Secrets лучше получать:

- через mounted files;

- secret manager;

- CSI driver;

- workload identity;

- runtime API.


---

### Docker socket security

`/var/run/docker.sock` дает почти root-доступ к Docker host.

Container с Docker socket может:

- запускать privileged containers;

- монтировать host filesystem;

- читать secrets других containers.


Его нельзя монтировать без крайней необходимости.

---

### Image signing basics

Image signing подтверждает происхождение и целостность image.

Перед deploy система может проверить:

- trusted signer;

- digest;

- policy;

- transparency log.


---

### Supply Chain Security basics

Supply Chain Security защищает путь:

```text
source code
dependencies
build system
artifact
registry
deployment
```

Механизмы:

- dependency pinning;

- SBOM;

- signing;

- isolated builds;

- provenance;

- trusted registries.


---

## 14. Kubernetes Security

### Kubernetes RBAC

Kubernetes RBAC контролирует доступ к API.

Основные объекты:

- Role;

- ClusterRole;

- RoleBinding;

- ClusterRoleBinding.


---

### Role

Role задает permissions внутри конкретного namespace.

Например, разрешить читать Pods в namespace `dev`.

---

### ClusterRole

ClusterRole задает:

- cluster-wide permissions;

- права на cluster-scoped resources;

- reusable набор permissions для namespace.


---

### RoleBinding

RoleBinding связывает Role или ClusterRole с identity внутри namespace.

Identity:

- user;

- group;

- ServiceAccount.


---

### ClusterRoleBinding

ClusterRoleBinding связывает ClusterRole на весь cluster.

Нельзя использовать его там, где достаточно namespace RoleBinding.

---

### ServiceAccount

ServiceAccount — identity Kubernetes workload.

Pod может использовать ServiceAccount token для доступа к Kubernetes API.

Для каждого приложения лучше создавать отдельный ServiceAccount.

---

### Kubernetes Secrets

Kubernetes Secret хранит чувствительные значения.

По умолчанию значения представлены в base64, а не зашифрованы.

Secret нужно защищать через RBAC и encryption at rest.

---

### Encryption Secrets at rest

API Server можно настроить на шифрование Secrets перед записью в etcd.

Нужен EncryptionConfiguration и безопасное управление encryption keys.

---

### NetworkPolicy

NetworkPolicy ограничивает Pod-to-Pod ingress и egress traffic.

Хорошая модель:

- default deny;

- разрешить только необходимые соединения.


Нужен CNI с поддержкой NetworkPolicy.

---

### SecurityContext

SecurityContext задает security-настройки Pod/container.

Например:

- user;

- group;

- capabilities;

- seccomp;

- read-only filesystem;

- privilege escalation.


---

### Pod Security Standards

Основные уровни:

- Privileged;

- Baseline;

- Restricted.


Для обычных workloads желательно стремиться к Restricted.

---

### Privileged containers

Privileged container получает почти полный доступ к host kernel.

Это высокий риск container escape и компрометации Node.

Использовать только для строго обоснованных системных компонентов.

---

### Admission Controllers

Admission Controllers проверяют запросы до сохранения объекта.

Они могут:

- запрещать privileged Pods;

- требовать labels;

- проверять images;

- добавлять security defaults;

- применять policies.


---

### Secrets Store CSI Driver basics

Secrets Store CSI Driver монтирует secrets из внешнего provider в Pod как files.

Источники:

- Vault;

- AWS;

- GCP;

- Azure.


Secret может не храниться как обычный Kubernetes Secret.

---

### External Secrets Operator basics

External Secrets Operator синхронизирует данные из внешнего secret manager в Kubernetes Secret.

В Git хранится ссылка и metadata, а не secret value.

---

### Kubernetes Audit Logs

Audit logs API Server показывают:

- кто сделал запрос;

- к какому resource;

- какое действие;

- результат;

- время.


Нужны для расследований и compliance.

---

### Защита Kubernetes API

Базовые меры:

- private endpoint;

- TLS;

- strong authentication;

- минимальный RBAC;

- audit logs;

- network allowlist;

- обновления;

- admission policies;

- отключение anonymous access.


---

## 15. CI/CD Security

### Secrets в CI/CD

Secrets CI/CD могут включать:

- registry credentials;

- cloud tokens;

- signing keys;

- kubeconfig;

- webhook tokens.


Их нужно хранить в CI secret store или внешнем secrets manager.

---

### Protected variables

Protected variables доступны только защищенным branches/tags.

Это предотвращает получение production secrets из feature branch.

---

### Masked variables

Masked variables скрываются в job logs.

Но masking не защищает от malicious script, который отправит secret во внешнюю систему.

---

### Protected branches

Protected branch ограничивает:

- direct push;

- force push;

- merge;

- pipeline permissions.


Production deploy обычно разрешается только из protected branch/tag.

---

### Protected environments

Protected environment ограничивает, кто может выполнить deploy.

Например, deploy в production разрешен только SRE и release managers.

---

### Runner security

Runner выполняет непроверенный код из repository.

Риски:

- кража secrets;

- доступ к host;

- доступ к соседним jobs;

- Docker socket;

- persistence между builds.


Untrusted pipelines лучше запускать в изолированных ephemeral runners.

---

### Least Privilege для pipeline

Pipeline должен иметь только нужные permissions.

Например:

- build job не нужен доступ к production;

- test job не нужен cloud admin;

- deploy role ограничена конкретным namespace.


---

### Dependency scanning

Dependency scanning проверяет libraries и packages на известные CVE.

Проверяются:

- npm;

- pip;

- Maven;

- Go modules;

- OS packages.


---

### Container scanning

Container scanning проверяет image на:

- vulnerable packages;

- malware;

- misconfiguration;

- secrets;

- запрещенные licenses.


---

### SAST

SAST — Static Application Security Testing.

Он анализирует source code без запуска приложения.

Ищет:

- unsafe functions;

- injection;

- hardcoded secrets;

- insecure patterns.


---

### DAST basics

DAST — Dynamic Application Security Testing.

Он тестирует запущенное приложение снаружи.

Может находить:

- injection;

- authentication issues;

- insecure headers;

- exposed endpoints.


---

### Secret scanning

Secret scanning ищет keys и tokens в:

- source code;

- Git history;

- commits;

- artifacts;

- logs;

- container images.


Примеры: Gitleaks, TruffleHog.

---

### Software Composition Analysis

SCA анализирует сторонние dependencies.

Он определяет:

- версии;

- CVE;

- licenses;

- transitive dependencies;

- доступные updates.


---

### Подпись артефактов

Artifact signing подтверждает происхождение и целостность:

- container image;

- package;

- binary;

- Helm chart.


Deployment policy может разрешать только signed artifacts.

---

### Supply Chain Security

CI/CD supply chain включает:

- source repository;

- dependencies;

- runner;

- build process;

- registry;

- deployment credentials.


Нужно защищать каждый этап, а не только конечный image.

---

## 16. Cloud Security

### Cloud IAM

Cloud IAM управляет доступом к cloud resources.

Права нужно выдавать через roles и short-lived credentials.

---

### Service Roles

Service Role — роль для cloud service.

Например VM получает роль для чтения конкретного bucket без static key.

---

### Instance Profiles

В AWS Instance Profile связывает IAM Role с EC2 instance.

Приложение получает temporary credentials через instance metadata.

---

### Workload Identity

Workload Identity связывает cloud identity с workload.

Например:

```text
Kubernetes ServiceAccount -> Cloud IAM Role
```

Это позволяет не хранить cloud access keys в Pod.

---

### Security Groups

Security Groups ограничивают network traffic к cloud resources.

Правила должны использовать:

- минимальные ports;

- конкретные sources;

- ссылки на другие Security Groups;

- отсутствие `0.0.0.0/0` без необходимости.


---

### Private endpoints

Private endpoint дает доступ к managed service через private network.

Например приложение обращается к object storage или secret manager без выхода в публичный интернет.

---

### KMS

Cloud KMS управляет encryption keys.

Используется для шифрования:

- disks;

- databases;

- secrets;

- object storage;

- application data.


---

### Cloud Audit Logs

Cloud audit logs записывают API actions.

Например:

- кто удалил VM;

- кто изменил IAM;

- кто прочитал secret;

- кто сделал bucket public.


---

### Cloud Secrets Manager

Cloud Secrets Manager централизованно хранит secrets и управляет доступом через IAM.

Предпочтительнее хранения secrets в VM config или CI variables.

---

### Storage bucket permissions

Bucket permissions должны разрешать доступ только нужным identities.

Нужно проверять:

- bucket policy;

- ACL;

- public access;

- cross-account grants;

- object ownership.


---

### Public access prevention

Public access prevention запрещает случайное открытие storage bucket в интернет.

Это полезный organization-level guardrail.

---

### Cross-account access

Cross-account access позволяет identity из одного account получить роль в другом.

Нужно ограничивать:

- trusted principal;

- external ID;

- allowed actions;

- session duration;

- conditions.


---

### Temporary credentials

Temporary credentials выдаются через:

- role assumption;

- federation;

- workload identity;

- metadata service.


Они безопаснее постоянных access keys.

---

## 17. Security Monitoring и Audit

### Audit Logs

Audit logs фиксируют административные и security-sensitive действия.

Они должны храниться централизованно и защищаться от изменения.

---

### Authentication logs

Authentication logs показывают:

- успешные login;

- failed login;

- MFA;

- token issuance;

- session creation.


---

### Authorization logs

Authorization logs показывают:

- access denied;

- policy decision;

- resource access;

- privilege check.


---

### Failed login attempts

Много failed login может означать:

- brute force;

- credential stuffing;

- неправильную automation;

- истекший пароль.


Нужны rate limiting и alerting.

---

### Privilege changes

Нужно логировать:

- добавление в admin group;

- изменение RoleBinding;

- назначение IAM policy;

- sudoers changes;

- creation of access key.


---

### Secret access logs

Secret manager должен логировать:

- кто читал secret;

- какую версию;

- когда;

- из какого источника;

- был ли доступ разрешен.


---

### Cloud audit logs

Cloud audit logs нужно собирать из всех accounts/projects/subscriptions в центральное защищенное хранилище.

---

### Kubernetes audit logs

Особенно важные события:

- чтение Secrets;

- изменение RBAC;

- запуск privileged Pod;

- exec в Pod;

- удаление resources;

- изменение admission configuration.


---

### Suspicious activity

Примеры:

- login из необычной страны;

- создание admin role;

- массовое чтение secrets;

- отключение audit logs;

- unusual egress;

- запуск crypto miner.


---

### SIEM basics

SIEM собирает и анализирует security logs.

Функции:

- correlation;

- search;

- alerting;

- dashboards;

- incident investigation;

- retention.


---

### Security alerts

Security alert должен содержать:

- что произошло;

- affected identity/resource;

- severity;

- evidence;

- рекомендуемое действие;

- ссылку на logs/runbook.


---

### Log retention

Security logs часто хранят дольше обычных application logs.

Retention зависит от:

- законодательства;

- compliance;

- incident response needs;

- стоимости.


---

### Tamper protection

Audit logs нужно защищать от изменения и удаления.

Механизмы:

- immutable storage;

- отдельный security account;

- write-only shipping;

- object lock;

- restrictive IAM;

- integrity checks.


---

## 18. Vulnerability Management

### CVE

CVE — публичный идентификатор известной vulnerability.

Пример формата:

```text
CVE-2026-12345
```

CVE не определяет автоматически риск именно для вашей системы.

---

### CVSS

CVSS — числовая оценка технической серьезности vulnerability.

Обычно учитывает:

- attack vector;

- complexity;

- privileges;

- user interaction;

- impact.


---

### Vulnerability scanner

Scanner ищет известные vulnerabilities в:

- hosts;

- packages;

- dependencies;

- container images;

- cloud configuration.


Scanner может давать false positives и требует анализа.

---

### Patch management

Patch management включает:

- inventory;

- оценку patches;

- тестирование;

- rollout;

- rollback;

- контроль результата.


---

### Dependency updates

Dependencies нужно регулярно обновлять.

Полезны:

- lock files;

- automated PR;

- tests;

- changelog review;

- version pinning.


---

### OS updates

OS updates закрывают vulnerabilities в:

- kernel;

- libraries;

- services;

- system tools.


Некоторые updates требуют reboot.

---

### Image updates

Container image нужно пересобирать при обновлении:

- base image;

- OS packages;

- runtime;

- application dependencies.


Перезапуск старого image не устанавливает новые patches.

---

### False positives

False positive — scanner сообщает vulnerability, которая фактически не применима.

Например:

- vulnerable code не используется;

- package только build-time;

- feature отключена;

- vendor backport исправил проблему без изменения версии.


Решение нужно документировать.

---

### Risk prioritization

Приоритет remediation зависит от:

- exploitability;

- internet exposure;

- asset criticality;

- privileges;

- available exploit;

- business impact;

- compensating controls.


---

### Remediation

Remediation — устранение vulnerability.

Варианты:

- update;

- patch;

- remove package;

- изменить config;

- ограничить доступ;

- заменить component.


---

### Compensating controls

Compensating control снижает риск, когда vulnerability нельзя исправить сразу.

Примеры:

- firewall;

- WAF rule;

- отключение функции;

- network isolation;

- дополнительный monitoring;

- запрет доступа.


Это временная мера, а не полноценный patch.

---

## 19. Security Incident Response

### Security incident

Security incident — событие, нарушающее confidentiality, integrity или availability.

Примеры:

- утечка token;

- malware;

- unauthorized access;

- data exposure;

- compromised host.


---

### Detection

Detection — обнаружение инцидента через:

- alerts;

- logs;

- user report;

- anomaly;

- scanner;

- threat intelligence.


---

### Containment

Containment ограничивает распространение инцидента.

Примеры:

- изолировать host;

- заблокировать account;

- отозвать token;

- закрыть network access;

- остановить malicious process.


---

### Eradication

Eradication удаляет root cause.

Например:

- удалить malware;

- закрыть vulnerability;

- удалить unauthorized user;

- пересобрать image;

- заменить compromised credentials.


---

### Recovery

Recovery возвращает систему в нормальную работу.

Нужно:

- восстановить из доверенного source;

- проверить integrity;

- включить monitoring;

- постепенно вернуть traffic;

- следить за повторной атакой.


---

### Postmortem

Postmortem описывает:

- timeline;

- impact;

- root cause;

- response;

- что сработало;

- что улучшить;

- action items.


Цель — улучшение системы, а не поиск виноватого.

---

### Credential compromise

При компрометации credential нужно:

- немедленно revoke;

- найти места использования;

- выпустить новый;

- проверить logs;

- определить масштаб доступа;

- проверить lateral movement;

- обновить dependent systems.


---

### Secret rotation после утечки

После утечки недостаточно удалить secret из Git или файла.

Нужно:

- отозвать старый;

- создать новый;

- обновить consumers;

- проверить использование старого;

- очистить history/logs, где возможно;

- расследовать доступ.


---

### Token revocation

Token revocation отключает token до его естественного expiration.

Если token нельзя отозвать отдельно, может понадобиться:

- сменить signing key;

- удалить session;

- отключить client;

- изменить credentials.


---

### Изоляция скомпрометированного хоста

Хост можно изолировать через:

- firewall;

- security group;

- отдельный VLAN;

- отключение Load Balancer target;

- cordon/drain;

- quarantine network.


Нельзя уничтожать evidence до его сохранения.

---

### Сохранение логов и evidence

Нужно сохранить:

- system logs;

- audit logs;

- cloud logs;

- memory/disk image при необходимости;

- process list;

- network connections;

- timeline;

- hashes файлов.


Evidence должно храниться с контролем целостности и доступа.

---

## 20. Troubleshooting

### `Permission denied`

Проверить:

- owner/group;

- file mode;

- parent directory permissions;

- SELinux/AppArmor;

- user процесса;

- mount options;

- ACL;

- capabilities.


```bash
id
ls -la
namei -l /path/to/file
```

---

### Неправильные права на файл

Проверить:

```bash
stat file
ls -l file
```

Исправить:

```bash
chown app:app file
chmod 600 file
```

Не использовать `chmod 777` как универсальное решение.

---

### Неправильный IAM Policy

Проверить:

- Action;

- Resource;

- Condition;

- explicit Deny;

- permission boundary;

- organization policy;

- session policy;

- trust policy.


Cloud audit logs часто показывают причину `AccessDenied`.

---

### Отсутствующий RBAC binding

Проверить:

```bash
kubectl auth can-i get pods \
  --as system:serviceaccount:dev:app
```

Посмотреть:

```bash
kubectl get role,rolebinding -n dev
kubectl get clusterrole,clusterrolebinding
```

---

### Истёкший сертификат

Проверить:

```bash
openssl x509 -in cert.pem -noout -dates
```

Удаленно:

```bash
openssl s_client -connect example.com:443 -servername example.com
```

Нужно выпустить новый certificate и reload service.

---

### Неправильная certificate chain

Симптомы:

- browser/client не доверяет certificate;

- `unable to get local issuer certificate`;

- часть клиентов работает, часть нет.


Сервер должен отдавать server certificate и необходимые intermediate certificates.

---

### TLS handshake failed

Причины:

- expired certificate;

- hostname mismatch;

- unsupported protocol/cipher;

- неправильный SNI;

- отсутствующий client certificate;

- неизвестный CA;

- HTTP на HTTPS port.


---

### Secret недоступен приложению

Проверить:

- IAM/RBAC;

- ServiceAccount;

- secret name/path;

- namespace;

- mount;

- environment variable;

- CSI/External Secrets status;

- network до secret manager.


---

### Secret имеет неправильную версию

Проверить:

- active/current version;

- alias;

- deployment configuration;

- cache приложения;

- rollout после rotation;

- отключена ли старая версия.


---

### Неправильный ServiceAccount

Проверить Pod:

```bash
kubectl get pod <pod> -o jsonpath='{.spec.serviceAccountName}'
```

Проверить permissions через `kubectl auth can-i`.

---

### Firewall блокирует соединение

Проверить:

- source/destination;

- port;

- protocol;

- direction;

- routing;

- host firewall;

- cloud firewall;

- logs firewall.


Инструменты:

```bash
nc -vz host 443
curl -v https://host
```

---

### Security Group блокирует трафик

Проверить:

- inbound rules target;

- outbound rules source;

- правильный port;

- source CIDR/SG;

- attachment SG к ресурсу;

- route/NACL.


---

### Kubernetes NetworkPolicy блокирует трафик

Проверить:

- Pod labels;

- namespace selectors;

- ingress/egress;

- DNS egress;

- CNI support;

- default deny policies.


```bash
kubectl get networkpolicy -A
```

---

### SSH key rejected

Причины:

- неправильный user;

- public key отсутствует;

- неправильные permissions;

- неправильный private key;

- `PubkeyAuthentication no`;

- SELinux context;

- key запрещен options.


Диагностика:

```bash
ssh -vvv user@host
```

---

### Token expired

Нужно:

- получить новый token;

- обновить automation;

- проверить clock synchronization;

- настроить refresh или role assumption;

- избегать hardcoded temporary token.


---

### Проверка Audit Logs

Audit logs помогают ответить:

- кто выполнил действие;

- когда;

- с какого IP;

- над каким resource;

- был ли запрос успешным;

- какая policy отказала.


---

## 21. Практика

### Создать пользователя с минимальными правами

Создать Linux user:

```bash
useradd -m appuser
```

Выдать только нужные directory permissions и не добавлять в sudo без необходимости.

---

### Настроить SSH только по ключам

В `sshd_config`:

```text
PubkeyAuthentication yes
PasswordAuthentication no
PermitRootLogin no
```

Проверить config:

```bash
sshd -t
```

---

### Ограничить доступ через firewall

Пример UFW:

```bash
ufw default deny incoming
ufw allow from 203.0.113.10 to any port 22
ufw allow 443/tcp
ufw enable
```

---

### Создать IAM Role

Практика:

- создать trust policy;

- создать permission policy;

- назначить role workload;

- получить temporary credentials;

- проверить доступ.


---

### Создать Least Privilege Policy

Сначала определить:

- конкретные Actions;

- конкретные Resources;

- необходимые Conditions.


Не использовать wildcard `*`, если можно указать точный ресурс.

---

### Сохранить secret в Vault

Пример:

```bash
vault kv put secret/myapp db_password='example'
```

Прочитать:

```bash
vault kv get secret/myapp
```

В реальной работе secret нельзя вставлять в shell history.

---

### Настроить Secret Rotation

Практика:

- создать новую версию;

- обновить приложение;

- проверить работу;

- отозвать старую;

- настроить audit и alerting.


---

### Создать Kubernetes Role и RoleBinding

Role:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: dev
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list"]
```

Затем связать ее с ServiceAccount через RoleBinding.

---

### Настроить NetworkPolicy

Практика:

- создать default deny;

- разрешить app доступ к database;

- разрешить DNS;

- проверить traffic через debug Pod.


---

### Запустить контейнер не от root

Dockerfile:

```dockerfile
RUN useradd -r -u 10001 app
USER 10001
```

Проверить:

```bash
docker exec <container> id
```

---

### Выпустить TLS-сертификат

Для теста можно создать self-signed certificate:

```bash
openssl req -x509 -newkey rsa:2048 \
  -keyout key.pem \
  -out cert.pem \
  -days 30 \
  -nodes
```

Для production нужен trusted CA или internal PKI.

---

### Проверить сертификат через `openssl`

Локальный файл:

```bash
openssl x509 -in cert.pem -noout -text
```

Удаленный сервер:

```bash
openssl s_client \
  -connect example.com:443 \
  -servername example.com
```

---

### Найти secret в Git-истории

Инструменты:

- Gitleaks;

- TruffleHog;

- repository secret scanning.


После обнаружения нужно сначала отозвать secret, а затем очищать history.

---

### Отозвать скомпрометированный токен

Шаги:

- revoke/disable token;

- создать новый при необходимости;

- обновить consumers;

- проверить audit logs;

- определить источник утечки;

- проверить несанкционированные действия.


---

## 22. Interview Questions

### Authentication vs Authorization

Authentication подтверждает identity.

Authorization определяет permissions.

```text
Authentication -> кто ты
Authorization  -> что тебе можно
```

---

### RBAC vs ABAC

RBAC выдает права через роли.

ABAC принимает решение по атрибутам пользователя, ресурса и контекста.

RBAC проще, ABAC гибче.

---

### Что такое Least Privilege

Least Privilege — выдача минимального набора прав, необходимого для выполнения задачи.

Права должны быть ограничены по:

- actions;

- resources;

- environment;

- time;

- source.


---

### Что такое IAM Role

IAM Role — identity с набором permissions, которую можно временно принять.

Обычно role используется workloads, services и federated users.

---

### User vs Role vs Service Account

User — identity человека или legacy automation.

Role — временный набор прав.

Service Account — identity приложения или workload.

---

### Почему нельзя хранить secrets в Git

Git сохраняет историю и распространяется через clones, forks, CI и backups.

Удаление secret из текущего файла не удаляет его из истории.

После утечки secret нужно отозвать.

---

### Как безопасно передать secret приложению

Предпочтительные варианты:

- secret manager;

- workload identity;

- mounted secret file;

- CSI driver;

- dynamic secret;

- short-lived credential.


Не встраивать secret в source code или image.

---

### Что такое Secret Rotation

Secret Rotation — безопасная замена secret новым.

Процесс включает выпуск новой версии, обновление consumers и отзыв старой версии.

---

### Hashing vs Encryption

Hashing — одностороннее преобразование.

Encryption — обратимое преобразование с ключом.

Пароли обычно хешируют, данные для последующего чтения шифруют.

---

### Symmetric vs Asymmetric Encryption

Symmetric encryption использует один общий secret key.

Asymmetric encryption использует public/private key pair.

Symmetric быстрее, asymmetric удобен для key exchange, authentication и signatures.

---

### Как работает TLS

TLS:

- согласовывает параметры;

- проверяет certificate;

- выполняет key agreement;

- создает session keys;

- шифрует дальнейший traffic;

- защищает целостность данных.


---

### Что такое mTLS

mTLS — TLS, где обе стороны предъявляют certificates.

Он дает взаимную authentication клиента и сервера.

---

### Что такое Certificate Chain

Certificate Chain связывает server certificate с доверенным root CA через intermediate CA.

Клиент проверяет подписи до доверенного root certificate.

---

### Security Group vs Network ACL

Security Group обычно stateful и работает на уровне resource.

Network ACL обычно stateless и работает на уровне subnet.

---

### Как защитить SSH

Основные меры:

- public key authentication;

- отключить passwords;

- отключить root login;

- bastion/VPN;

- firewall allowlist;

- MFA или SSH certificates;

- audit logs;

- rotation keys;

- регулярные updates.


---

### Как хранить secrets в Kubernetes

Безопаснее использовать:

- external secret manager;

- Secrets Store CSI Driver;

- External Secrets Operator;

- encryption at rest;

- минимальный RBAC;

- short-lived credentials;

- workload identity.


---

### Почему Kubernetes Secret не полностью безопасен по умолчанию

Secret хранит значения в base64, а не обязательно в encrypted form.

Пользователь с правами чтения Secret или доступа к etcd может получить исходное значение.

---

### Role vs ClusterRole

Role работает внутри одного namespace.

ClusterRole может давать cluster-wide permissions и доступ к cluster-scoped resources.

---

### Как отреагировать на утечку токена

Нужно:

- немедленно revoke token;

- выпустить новый;

- проверить audit logs;

- определить доступные ресурсы;

- найти несанкционированные действия;

- устранить источник утечки;

- обновить consumers.


---

### Как реализовать Least Privilege в CI/CD

Нужно:

- отдельные identities для jobs;

- короткоживущие credentials;

- ограниченные deploy roles;

- protected branches/environments;

- production secrets только для production job;

- ephemeral runners;

- запрет cloud admin;

- audit всех deploy actions.