[[Helm. Конспект]]
# Helm

## 1. Основы

- [ ] Что такое Helm
- [ ] Зачем нужен Helm
- [ ] Helm architecture
- [ ] Helm Chart
- [ ] Release
- [ ] Repository
- [ ] Chart Museum basics
- [ ] OCI Registry basics

---

## 2. Структура Chart

- [ ] Chart.yaml
- [ ] values.yaml
- [ ] templates/
- [ ] charts/
- [ ] .helmignore
- [ ] NOTES.txt
- [ ] _helpers.tpl
- [ ] CRDs directory

---

## 3. Templates

- [ ] Go Templates basics
- [ ] Variables
- [ ] Pipelines
- [ ] if
- [ ] else
- [ ] range
- [ ] with
- [ ] include
- [ ] template
- [ ] tpl
- [ ] default
- [ ] required
- [ ] quote
- [ ] toYaml
- [ ] nindent
- [ ] indent

---

## 4. Values

- [ ] values.yaml
- [ ] Default values
- [ ] Override values
- [ ] --set
- [ ] --values (-f)
- [ ] Multiple values files
- [ ] Global values
- [ ] Environment-specific values

---

## 5. Работа с Chart

- [ ] helm create
- [ ] helm package
- [ ] helm lint
- [ ] helm template
- [ ] helm install
- [ ] helm upgrade
- [ ] helm uninstall
- [ ] helm rollback
- [ ] helm history
- [ ] helm list
- [ ] helm status
- [ ] helm get

---

## 6. Dependencies

- [ ] Chart dependencies
- [ ] dependencies в Chart.yaml
- [ ] helm dependency update
- [ ] helm dependency build
- [ ] Subcharts
- [ ] Parent chart
- [ ] Global values
- [ ] Conditions
- [ ] Tags

---

## 7. Hooks

- [ ] Helm Hooks
- [ ] pre-install
- [ ] post-install
- [ ] pre-upgrade
- [ ] post-upgrade
- [ ] pre-delete
- [ ] Hook weights
- [ ] Hook delete policy

---

## 8. Debugging

- [ ] helm lint
- [ ] helm template
- [ ] helm install --dry-run
- [ ] helm install --debug
- [ ] helm get values
- [ ] helm get manifest
- [ ] helm get hooks
- [ ] Проверка rendered manifests

---

## 9. Best Practices

- [ ] DRY templates
- [ ] Использование _helpers.tpl
- [ ] Naming conventions
- [ ] Labels
- [ ] Annotations
- [ ] Версионирование Chart
- [ ] Semantic Versioning
- [ ] Не хранить секреты в values.yaml
- [ ] Структура production Chart

---

## 10. Практика

- [ ] Создать свой Chart
- [ ] Развернуть Deployment
- [ ] Развернуть Service
- [ ] Развернуть Ingress
- [ ] Использовать ConfigMap
- [ ] Использовать Secret
- [ ] Использовать PVC
- [ ] Добавить values.yaml
- [ ] Использовать template helpers
- [ ] Создать dependency
- [ ] Выполнить upgrade
- [ ] Выполнить rollback
- [ ] Debug failed release

---

## 11. Interview Questions

- [ ] Что такое Helm
- [ ] Chart vs Release
- [ ] Что такое values.yaml
- [ ] Как работают шаблоны Helm
- [ ] include vs template
- [ ] default vs required
- [ ] helm install vs helm upgrade
- [ ] Как работает rollback
- [ ] Что такое Subchart
- [ ] Как дебажить Helm Chart
- [ ] Почему используют Helm вместо обычных YAML
- [ ] Как организовать Helm Charts для production