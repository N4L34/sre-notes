[[Helm. Конспект]]
# Helm

## 1. Основы

- [x] Что такое Helm
- [x] Зачем нужен Helm
- [x] Helm architecture
- [x] Helm Chart
- [x] Release
- [x] Repository
- [x] Chart Museum basics
- [x] OCI Registry basics

---

## 2. Структура Chart

- [x] Chart.yaml
- [x] values.yaml
- [x] templates/
- [x] charts/
- [x] .helmignore
- [x] NOTES.txt
- [x] _helpers.tpl
- [x] CRDs directory

---

## 3. Templates

- [x] Go Templates basics
- [x] Variables
- [x] Pipelines
- [x] if
- [x] else
- [x] range
- [x] with
- [x] include
- [x] template
- [x] tpl
- [x] default
- [x] required
- [x] quote
- [x] toYaml
- [x] nindent
- [x] indent

---

## 4. Values

- [x] values.yaml
- [x] Default values
- [x] Override values
- [x] --set
- [x] --values (-f)
- [x] Multiple values files
- [x] Global values
- [x] Environment-specific values

---

## 5. Работа с Chart

- [x] helm create
- [x] helm package
- [x] helm lint
- [x] helm template
- [x] helm install
- [x] helm upgrade
- [x] helm uninstall
- [x] helm rollback
- [x] helm history
- [x] helm list
- [x] helm status
- [x] helm get

---

## 6. Dependencies

- [x] Chart dependencies
- [x] dependencies в Chart.yaml
- [x] helm dependency update
- [x] helm dependency build
- [x] Subcharts
- [x] Parent chart
- [x] Global values
- [x] Conditions
- [x] Tags

---

## 7. Hooks

- [x] Helm Hooks
- [x] pre-install
- [x] post-install
- [x] pre-upgrade
- [x] post-upgrade
- [x] pre-delete
- [x] Hook weights
- [x] Hook delete policy

---

## 8. Debugging

- [x] helm lint
- [x] helm template
- [x] helm install --dry-run
- [x] helm install --debug
- [x] helm get values
- [x] helm get manifest
- [x] helm get hooks
- [x] Проверка rendered manifests

---

## 9. Best Practices

- [x] DRY templates
- [x] Использование _helpers.tpl
- [x] Naming conventions
- [x] Labels
- [x] Annotations
- [x] Версионирование Chart
- [x] Semantic Versioning
- [x] Не хранить секреты в values.yaml
- [x] Структура production Chart

---

## 10. Практика

- [x] Создать свой Chart
- [x] Развернуть Deployment
- [x] Развернуть Service
- [x] Развернуть Ingress
- [x] Использовать ConfigMap
- [x] Использовать Secret
- [x] Использовать PVC
- [x] Добавить values.yaml
- [x] Использовать template helpers
- [x] Создать dependency
- [x] Выполнить upgrade
- [x] Выполнить rollback
- [x] Debug failed release

---

## 11. Interview Questions

- [x] Что такое Helm
- [x] Chart vs Release
- [x] Что такое values.yaml
- [x] Как работают шаблоны Helm
- [x] include vs template
- [x] default vs required
- [x] helm install vs helm upgrade
- [x] Как работает rollback
- [x] Что такое Subchart
- [x] Как дебажить Helm Chart
- [x] Почему используют Helm вместо обычных YAML
- [x] Как организовать Helm Charts для production