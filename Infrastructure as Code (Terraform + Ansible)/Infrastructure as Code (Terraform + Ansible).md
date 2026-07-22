[[Infrastructure as Code (Terraform + Ansible). Конспект]]
## 1. Основы IaC

- [x] Что такое Infrastructure as Code
- [x] Зачем нужен IaC
- [x] Imperative vs Declarative подход
- [x] Idempotency
- [x] Reproducible infrastructure
- [x] Version control для инфраструктуры

---

# Terraform

## 2. Основы Terraform

- [x] Что такое Terraform
- [x] Terraform architecture
- [x] Terraform CLI
- [x] Provider
- [x] Resource
- [x] Data Source
- [x] Configuration files (.tf)

---

## 3. Terraform Workflow

- [x] terraform init
- [x] terraform plan
- [x] terraform apply
- [x] terraform destroy
- [x] terraform validate
- [x] terraform fmt

---

## 4. Terraform Configuration

- [x] Variables
- [x] Outputs
- [x] Locals
- [x] Resource dependencies
- [x] References
- [x] Expressions
- [x] Functions basics

---

## 5. Terraform State

- [x] Что такое Terraform state
- [x] terraform.tfstate
- [x] Local state
- [x] Remote state
- [x] Backend
- [x] State locking
- [x] Почему нельзя терять state

---

## 6. Terraform Modules

- [x] Что такое Module
- [x] Root module
- [x] Child modules
- [x] Reusable infrastructure
- [x] Module registry basics

---

## 7. Terraform в Cloud

- [x] Terraform + AWS basics
- [x] Создание VPC
- [x] Создание EC2
- [x] Security Groups
- [x] IAM basics
- [x] Terraform + Kubernetes basics

---

# Ansible

## 8. Основы Ansible

- [x] Что такое Ansible
- [x] Configuration Management
- [x] Agentless architecture
- [x] SSH connection
- [x] Inventory
- [x] Hosts

---

## 9. Ansible Components

- [x] Playbook
- [x] Task
- [x] Module
- [x] Handler
- [x] Role
- [x] Variables
- [x] Facts

---

## 10. Ansible Workflow

- [x] ansible command
- [x] ansible-playbook
- [x] Check mode
- [x] Diff mode
- [x] Become (sudo)

---

## 11. Ansible Templates

- [x] Jinja2 basics
- [x] Template files
- [x] Variables inside templates
- [x] Configuration generation

---

## 12. Ansible Best Practices

- [x] Idempotent playbooks
- [x] Roles structure
- [x] Secrets через Ansible Vault
- [x] Inventory management
- [x] Tags

---

## 13. Terraform vs Ansible

- [x] Terraform создаёт инфраструктуру
- [x] Ansible конфигурирует инфраструктуру
- [x] Terraform vs Ansible use cases
- [x] Использование вместе

---

## 14. Interview Questions

- [x] Что такое IaC
- [x] Terraform vs Ansible
- [x] Declarative vs Imperative
- [x] Что такое Terraform state
- [x] Зачем нужен backend
- [x] Что делает terraform plan
- [x] Что такое provider
- [x] Что такое resource
- [x] Что такое Ansible inventory
- [x] Что такое playbook
- [x] Что такое idempotency
- [x] Почему Ansible не требует агента