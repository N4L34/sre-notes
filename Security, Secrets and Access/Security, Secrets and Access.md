[[Security, Secrets and Access. Конспект]]



## 1. Основы безопасности

- [ ] CIA triad
- [ ] Confidentiality
- [ ] Integrity
- [ ] Availability
- [ ] Threat
- [ ] Vulnerability
- [ ] Exploit
- [ ] Risk
- [ ] Attack surface
- [ ] Defense in Depth
- [ ] Zero Trust basics
- [ ] Shared Responsibility Model

---

## 2. Authentication и Authorization

- [ ] Authentication
- [ ] Authorization
- [ ] Authentication vs Authorization
- [ ] Identification
- [ ] Accounting
- [ ] MFA
- [ ] SSO
- [ ] Session-based authentication
- [ ] Token-based authentication
- [ ] API keys
- [ ] Basic Authentication
- [ ] OAuth 2.0 basics
- [ ] OpenID Connect basics
- [ ] JWT basics

---

## 3. Access Control

- [ ] Least Privilege
- [ ] Need-to-know
- [ ] Deny by default
- [ ] RBAC
- [ ] ABAC
- [ ] ACL
- [ ] Policy-based access
- [ ] Separation of Duties
- [ ] Privilege Escalation
- [ ] Temporary access
- [ ] Access review
- [ ] Offboarding пользователей

---

## 4. IAM

- [ ] Что такое IAM
- [ ] Users
- [ ] Groups
- [ ] Roles
- [ ] Policies
- [ ] Service Accounts
- [ ] Machine Identity
- [ ] Role assumption
- [ ] Trust policy
- [ ] Permission policy
- [ ] Explicit Allow и Deny
- [ ] Long-lived vs Short-lived credentials
- [ ] Federation basics

---

## 5. Secrets Management

- [ ] Что такое Secret
- [ ] Пароли
- [ ] API tokens
- [ ] Private keys
- [ ] Certificates
- [ ] Database credentials
- [ ] Encryption keys
- [ ] Secrets lifecycle
- [ ] Secret rotation
- [ ] Secret expiration
- [ ] Secret revocation
- [ ] Dynamic secrets
- [ ] Secret versioning
- [ ] Centralized secrets storage

---

## 6. Хранение Secrets

- [ ] Почему нельзя хранить secrets в Git
- [ ] Почему нельзя хранить secrets в Docker image
- [ ] Почему нельзя хранить secrets в логах
- [ ] Environment variables для secrets
- [ ] Файлы с secrets
- [ ] Права доступа к secret-файлам
- [ ] Encryption at rest
- [ ] Encryption in transit
- [ ] Secret Manager
- [ ] HashiCorp Vault basics
- [ ] AWS Secrets Manager basics
- [ ] GCP Secret Manager basics
- [ ] Azure Key Vault basics

---

## 7. HashiCorp Vault

- [ ] Vault architecture
- [ ] Seal и Unseal
- [ ] Authentication methods
- [ ] Policies
- [ ] Tokens
- [ ] Leases
- [ ] Dynamic secrets
- [ ] Secret engines
- [ ] KV secrets engine
- [ ] Database secrets engine
- [ ] Transit secrets engine
- [ ] Secret rotation
- [ ] High Availability basics

---

## 8. Cryptography Basics

- [ ] Encryption
- [ ] Decryption
- [ ] Hashing
- [ ] Encoding
- [ ] Encryption vs Hashing
- [ ] Symmetric encryption
- [ ] Asymmetric encryption
- [ ] Public key
- [ ] Private key
- [ ] Digital signature
- [ ] Checksums
- [ ] Salt
- [ ] Key rotation
- [ ] KMS basics

---

## 9. TLS и Certificates

- [ ] TLS
- [ ] HTTPS
- [ ] TLS handshake
- [ ] Certificate Authority
- [ ] Root Certificate
- [ ] Intermediate Certificate
- [ ] Certificate chain
- [ ] Self-signed certificate
- [ ] CSR
- [ ] SAN
- [ ] SNI
- [ ] Certificate expiration
- [ ] Certificate renewal
- [ ] mTLS
- [ ] TLS termination
- [ ] TLS passthrough

---

## 10. Linux Security

- [ ] Users и Groups
- [ ] File permissions
- [ ] `chmod`
- [ ] `chown`
- [ ] `umask`
- [ ] `sudo`
- [ ] `su`
- [ ] Root access
- [ ] SUID и SGID
- [ ] Linux capabilities
- [ ] PAM basics
- [ ] SSH hardening
- [ ] SELinux basics
- [ ] AppArmor basics
- [ ] Audit logs
- [ ] `auditd`
- [ ] Security updates
- [ ] Fail2ban basics

---

## 11. SSH Security

- [ ] Public/private key authentication
- [ ] `authorized_keys`
- [ ] Отключение password authentication
- [ ] Отключение root login
- [ ] Ограничение SSH-доступа
- [ ] Bastion Host
- [ ] Jump Host
- [ ] SSH Agent
- [ ] Private key permissions
- [ ] Host key verification
- [ ] Ротация SSH-ключей

---

## 12. Network Security

- [ ] Firewall
- [ ] Security Groups
- [ ] Network ACL
- [ ] Ingress и Egress rules
- [ ] Allowlist и Blocklist
- [ ] Network segmentation
- [ ] Private networks
- [ ] Public и Private Subnets
- [ ] VPN basics
- [ ] Bastion Host
- [ ] WAF basics
- [ ] DDoS protection basics
- [ ] Rate Limiting
- [ ] Port scanning
- [ ] Минимизация открытых портов

---

## 13. Container Security

- [ ] Минимальные base images
- [ ] Trusted images
- [ ] Image scanning
- [ ] Vulnerability scanning
- [ ] Запуск не от root
- [ ] Read-only filesystem
- [ ] Linux capabilities
- [ ] Seccomp
- [ ] AppArmor
- [ ] Secrets в контейнерах
- [ ] Docker socket security
- [ ] Image signing basics
- [ ] Supply Chain Security basics

---

## 14. Kubernetes Security

- [ ] Kubernetes RBAC
- [ ] Role
- [ ] ClusterRole
- [ ] RoleBinding
- [ ] ClusterRoleBinding
- [ ] ServiceAccount
- [ ] Kubernetes Secrets
- [ ] Encryption Secrets at rest
- [ ] NetworkPolicy
- [ ] SecurityContext
- [ ] Pod Security Standards
- [ ] Privileged containers
- [ ] Admission Controllers
- [ ] Secrets Store CSI Driver basics
- [ ] External Secrets Operator basics
- [ ] Kubernetes Audit Logs
- [ ] Защита Kubernetes API

---

## 15. CI/CD Security

- [ ] Secrets в CI/CD
- [ ] Protected variables
- [ ] Masked variables
- [ ] Protected branches
- [ ] Protected environments
- [ ] Runner security
- [ ] Least Privilege для pipeline
- [ ] Dependency scanning
- [ ] Container scanning
- [ ] SAST
- [ ] DAST basics
- [ ] Secret scanning
- [ ] Software Composition Analysis
- [ ] Подпись артефактов
- [ ] Supply Chain Security

---

## 16. Cloud Security

- [ ] Cloud IAM
- [ ] Service Roles
- [ ] Instance Profiles
- [ ] Workload Identity
- [ ] Security Groups
- [ ] Private endpoints
- [ ] KMS
- [ ] Cloud Audit Logs
- [ ] Cloud Secrets Manager
- [ ] Storage bucket permissions
- [ ] Public access prevention
- [ ] Cross-account access
- [ ] Temporary credentials

---

## 17. Security Monitoring и Audit

- [ ] Audit Logs
- [ ] Authentication logs
- [ ] Authorization logs
- [ ] Failed login attempts
- [ ] Privilege changes
- [ ] Secret access logs
- [ ] Cloud audit logs
- [ ] Kubernetes audit logs
- [ ] Suspicious activity
- [ ] SIEM basics
- [ ] Security alerts
- [ ] Log retention
- [ ] Tamper protection

---

## 18. Vulnerability Management

- [ ] CVE
- [ ] CVSS
- [ ] Vulnerability scanner
- [ ] Patch management
- [ ] Dependency updates
- [ ] OS updates
- [ ] Image updates
- [ ] False positives
- [ ] Risk prioritization
- [ ] Remediation
- [ ] Compensating controls

---

## 19. Security Incident Response

- [ ] Security incident
- [ ] Detection
- [ ] Containment
- [ ] Eradication
- [ ] Recovery
- [ ] Postmortem
- [ ] Credential compromise
- [ ] Secret rotation после утечки
- [ ] Token revocation
- [ ] Изоляция скомпрометированного хоста
- [ ] Сохранение логов и evidence

---

## 20. Troubleshooting

- [ ] `Permission denied`
- [ ] Неправильные права на файл
- [ ] Неправильный IAM Policy
- [ ] Отсутствующий RBAC binding
- [ ] Истёкший сертификат
- [ ] Неправильная certificate chain
- [ ] TLS handshake failed
- [ ] Secret недоступен приложению
- [ ] Secret имеет неправильную версию
- [ ] Неправильный ServiceAccount
- [ ] Firewall блокирует соединение
- [ ] Security Group блокирует трафик
- [ ] Kubernetes NetworkPolicy блокирует трафик
- [ ] SSH key rejected
- [ ] Token expired
- [ ] Проверка Audit Logs

---

## 21. Практика

- [ ] Создать пользователя с минимальными правами
- [ ] Настроить SSH только по ключам
- [ ] Ограничить доступ через firewall
- [ ] Создать IAM Role
- [ ] Создать Least Privilege Policy
- [ ] Сохранить secret в Vault
- [ ] Настроить Secret Rotation
- [ ] Создать Kubernetes Role и RoleBinding
- [ ] Настроить NetworkPolicy
- [ ] Запустить контейнер не от root
- [ ] Выпустить TLS-сертификат
- [ ] Проверить сертификат через `openssl`
- [ ] Найти secret в Git-истории
- [ ] Отозвать скомпрометированный токен

---

## 22. Interview Questions

- [ ] Authentication vs Authorization
- [ ] RBAC vs ABAC
- [ ] Что такое Least Privilege
- [ ] Что такое IAM Role
- [ ] User vs Role vs Service Account
- [ ] Почему нельзя хранить secrets в Git
- [ ] Как безопасно передать secret приложению
- [ ] Что такое Secret Rotation
- [ ] Hashing vs Encryption
- [ ] Symmetric vs Asymmetric Encryption
- [ ] Как работает TLS
- [ ] Что такое mTLS
- [ ] Что такое Certificate Chain
- [ ] Security Group vs Network ACL
- [ ] Как защитить SSH
- [ ] Как хранить secrets в Kubernetes
- [ ] Почему Kubernetes Secret не полностью безопасен по умолчанию
- [ ] Role vs ClusterRole
- [ ] Как отреагировать на утечку токена
- [ ] Как реализовать Least Privilege в CI/CD