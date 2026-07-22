[[Kubernetes. Конспект]]
# Kubernetes

## 1. Основы

- [x] Что такое Kubernetes
- [x] Зачем нужен Kubernetes
- [x] Kubernetes architecture
- [x] Control Plane
- [x] Worker Node
- [x] Cluster
- [x] Declarative configuration
- [x] Desired state
- [x] Reconciliation loop
- [x] Kubernetes objects
- [x] Namespaces

---

## 2. Control Plane Components

- [x] kube-apiserver
- [x] etcd
- [x] kube-scheduler
- [x] kube-controller-manager
- [x] cloud-controller-manager
- [x] Как компоненты Control Plane взаимодействуют между собой
- [x] High Availability Control Plane

---

## 3. Node Components

- [x] kubelet
- [x] kube-proxy
- [x] Container Runtime
- [x] CRI
- [x] CNI
- [x] CSI
- [x] Node status
- [x] Node conditions

---

## 4. Pods

- [x] Что такое Pod
- [x] Pod lifecycle
- [x] Pod phases
- [x] Containers в одном Pod
- [x] Shared network namespace
- [x] Shared volumes
- [x] Init Containers
- [x] Sidecar containers
- [x] Multi-container Pod
- [x] Static Pods
- [x] Pod restartPolicy
- [x] Pod termination
- [x] Graceful shutdown
- [x] Termination grace period

---

## 5. Labels, Selectors и Annotations

- [x] Labels
- [x] Label selectors
- [x] MatchLabels
- [x] MatchExpressions
- [x] Annotations
- [x] Как Service находит Pods
- [x] Как Deployment управляет Pods

---

## 6. ReplicaSet и Deployment

- [x] ReplicaSet
- [x] Deployment
- [x] Replicas
- [x] Rolling Update
- [x] Recreate strategy
- [x] maxSurge
- [x] maxUnavailable
- [x] Rollout status
- [x] Rollout history
- [x] Rollback
- [x] Revision
- [x] Pause и resume rollout

---

## 7. StatefulSet

- [x] Что такое StatefulSet
- [x] StatefulSet vs Deployment
- [x] Stable Pod names
- [x] Stable network identity
- [x] Ordered deployment
- [x] Ordered termination
- [x] volumeClaimTemplates
- [x] Headless Service
- [x] Stateful applications
- [x] Update strategies

---

## 8. DaemonSet и Jobs

- [x] DaemonSet
- [x] По одному Pod на Node
- [x] Типичные применения DaemonSet
- [x] Job
- [x] CronJob
- [x] completions
- [x] parallelism
- [x] backoffLimit
- [x] restartPolicy для Job
- [x] CronJob concurrencyPolicy
- [x] successfulJobsHistoryLimit
- [x] failedJobsHistoryLimit

---

## 9. Services

- [x] Что такое Service
- [x] ClusterIP
- [x] NodePort
- [x] LoadBalancer
- [x] ExternalName
- [x] Headless Service
- [x] Service selector
- [x] Endpoints
- [x] EndpointSlice
- [x] targetPort
- [x] port
- [x] nodePort
- [x] Session affinity
- [x] externalTrafficPolicy
- [x] internalTrafficPolicy

---

## 10. Kubernetes Networking

- [x] Kubernetes network model
- [x] Pod-to-Pod communication
- [x] Pod-to-Service communication
- [x] Node-to-Pod communication
- [x] CNI
- [x] Container network namespace
- [x] Pod IP
- [x] Service IP
- [x] ClusterIP
- [x] kube-proxy
- [x] iptables mode
- [x] IPVS mode
- [x] eBPF basics
- [x] CoreDNS
- [x] Service DNS
- [x] Pod DNS
- [x] NetworkPolicy
- [x] Ingress traffic
- [x] Egress traffic

---

## 11. Ingress

- [x] Ingress
- [x] Ingress Controller
- [x] Ingress resource
- [x] Host-based routing
- [x] Path-based routing
- [x] TLS termination
- [x] Default backend
- [x] Nginx Ingress Controller
- [x] Ingress vs Service LoadBalancer
- [x] Gateway API basics
- [x] Troubleshooting Ingress

---

## 12. ConfigMap и Secret

- [x] ConfigMap
- [x] Secret
- [x] Secret не является шифрованием
- [x] Environment variables
- [x] envFrom
- [x] Volume mounts
- [x] Projected volumes
- [x] Immutable ConfigMap и Secret
- [x] Обновление конфигурации
- [x] Secret rotation basics
- [x] External Secrets basics
- [x] Sealed Secrets basics

---

## 13. Storage

- [x] Volumes
- [x] emptyDir
- [x] hostPath
- [x] PersistentVolume
- [x] PersistentVolumeClaim
- [x] StorageClass
- [x] Dynamic provisioning
- [x] Static provisioning
- [x] Access Modes
- [x] ReadWriteOnce
- [x] ReadOnlyMany
- [x] ReadWriteMany
- [x] Volume Binding Mode
- [x] Reclaim Policy
- [x] Retain
- [x] Delete
- [x] CSI
- [x] Volume expansion
- [x] Volume snapshots basics

---

## 14. Resource Management

- [x] CPU requests
- [x] CPU limits
- [x] Memory requests
- [x] Memory limits
- [x] Scheduling по requests
- [x] CPU throttling
- [x] OOMKilled
- [x] QoS classes
- [x] Guaranteed
- [x] Burstable
- [x] BestEffort
- [x] LimitRange
- [x] ResourceQuota
- [x] Ephemeral storage requests и limits

---

## 15. Probes и Health Checks

- [x] Liveness probe
- [x] Readiness probe
- [x] Startup probe
- [x] HTTP probe
- [x] TCP probe
- [x] Exec probe
- [x] initialDelaySeconds
- [x] periodSeconds
- [x] timeoutSeconds
- [x] failureThreshold
- [x] successThreshold
- [x] Неправильные probes
- [x] Probe-induced restart loop

---

## 16. Scheduling

- [x] Как работает kube-scheduler
- [x] nodeSelector
- [x] Node affinity
- [x] Pod affinity
- [x] Pod anti-affinity
- [x] Taints
- [x] Tolerations
- [x] Topology spread constraints
- [x] nodeName
- [x] PriorityClass
- [x] Pod priority
- [x] Preemption
- [x] Scheduler predicates и scoring на базовом уровне

---

## 17. Autoscaling

- [x] Horizontal Pod Autoscaler
- [x] HPA по CPU
- [x] HPA по memory
- [x] Custom metrics
- [x] External metrics
- [x] Metrics Server
- [x] Vertical Pod Autoscaler basics
- [x] Cluster Autoscaler
- [x] KEDA basics
- [x] HPA и requests
- [x] Scale-up и scale-down behavior

---

## 18. Security

- [x] Authentication
- [x] Authorization
- [x] RBAC
- [x] Role
- [x] ClusterRole
- [x] RoleBinding
- [x] ClusterRoleBinding
- [x] ServiceAccount
- [x] SecurityContext
- [x] runAsUser
- [x] runAsNonRoot
- [x] fsGroup
- [x] Linux capabilities
- [x] privileged container
- [x] allowPrivilegeEscalation
- [x] readOnlyRootFilesystem
- [x] seccomp
- [x] Pod Security Standards
- [x] NetworkPolicy
- [x] Admission Controllers
- [x] Image security basics
- [x] Secrets security
- [x] Least privilege

---

## 19. kubectl

- [x] kubectl get
- [x] kubectl describe
- [x] kubectl logs
- [x] kubectl exec
- [x] kubectl apply
- [x] kubectl delete
- [x] kubectl edit
- [x] kubectl patch
- [x] kubectl scale
- [x] kubectl rollout
- [x] kubectl top
- [x] kubectl explain
- [x] kubectl config
- [x] kubectl context
- [x] kubectl port-forward
- [x] kubectl cp
- [x] kubectl auth can-i
- [x] JSONPath
- [x] Custom columns
- [x] Field selectors
- [x] Label selectors
- [x] Dry run

---

## 20. YAML и API Objects

- [x] apiVersion
- [x] kind
- [x] metadata
- [x] spec
- [x] status
- [x] Kubernetes API groups
- [x] Object names
- [x] UID
- [x] ownerReferences
- [x] finalizers
- [x] Generation
- [x] ResourceVersion
- [x] Declarative vs imperative management

---

## 21. Helm и Package Management

- [x] Helm Chart
- [x] Release
- [x] values.yaml
- [x] Templates
- [x] helm install
- [x] helm upgrade
- [x] helm rollback
- [x] helm template
- [x] helm lint
- [x] Helm hooks basics
- [x] Helm dependencies
- [x] Debugging Helm release

---

## 22. Cluster Administration

- [x] kubeconfig
- [x] Contexts
- [x] Certificates
- [x] Cluster certificates
- [x] Certificate rotation basics
- [x] etcd backup
- [x] etcd restore
- [x] Node drain
- [x] Node cordon
- [x] Node uncordon
- [x] Node maintenance
- [x] Kubernetes upgrades basics
- [x] Version skew basics
- [x] Control Plane HA
- [x] Worker node lifecycle

---

## 23. Observability

- [x] Kubernetes events
- [x] Container logs
- [x] kubectl logs
- [x] Previous container logs
- [x] Metrics Server
- [x] Prometheus
- [x] kube-state-metrics
- [x] Node Exporter
- [x] Kubernetes dashboards basics
- [x] Application metrics
- [x] Cluster metrics
- [x] Audit logs basics
- [x] Distributed tracing basics

---

## 24. Troubleshooting Pods

- [x] Pod Pending
- [x] CrashLoopBackOff
- [x] ImagePullBackOff
- [x] ErrImagePull
- [x] CreateContainerConfigError
- [x] ContainerCreating
- [x] OOMKilled
- [x] Evicted
- [x] Completed
- [x] Terminating Pod
- [x] Init Container failed
- [x] Readiness probe failed
- [x] Liveness probe failed
- [x] Wrong command или entrypoint
- [x] Missing ConfigMap или Secret
- [x] Permission denied

---

## 25. Troubleshooting Networking

- [x] Service недоступен
- [x] Pod не видит другой Pod
- [x] Pod не видит Service
- [x] DNS не работает
- [x] CoreDNS проблемы
- [x] Нет Endpoints
- [x] Неправильный selector
- [x] Неправильный targetPort
- [x] Ingress возвращает 404
- [x] Ingress возвращает 502
- [x] NetworkPolicy блокирует трафик
- [x] NodePort недоступен
- [x] LoadBalancer Pending
- [x] Проверка через curl, nslookup, dig и nc
- [x] Debug Pod

---

## 26. Troubleshooting Storage

- [x] PVC Pending
- [x] PV не создается
- [x] StorageClass отсутствует
- [x] Неподходящий Access Mode
- [x] Volume не монтируется
- [x] Multi-Attach error
- [x] Permission denied на volume
- [x] Node affinity conflict у PV
- [x] Volume заполнен
- [x] StatefulSet не получает PVC

---

## 27. Troubleshooting Scheduling и Nodes

- [x] FailedScheduling
- [x] Insufficient CPU
- [x] Insufficient memory
- [x] Untolerated taint
- [x] NodeSelector mismatch
- [x] Affinity mismatch
- [x] Node NotReady
- [x] DiskPressure
- [x] MemoryPressure
- [x] PIDPressure
- [x] Pod eviction
- [x] Node unreachable
- [x] kubelet не работает
- [x] Container runtime не работает

---

## 28. Практика

- [x] Создать Deployment
- [x] Создать Service
- [x] Настроить Ingress
- [x] Передать конфигурацию через ConfigMap
- [x] Передать Secret
- [x] Подключить PVC
- [x] Создать StatefulSet
- [x] Создать DaemonSet
- [x] Создать Job и CronJob
- [x] Настроить probes
- [x] Настроить requests и limits
- [x] Настроить HPA
- [x] Настроить RBAC
- [x] Настроить NetworkPolicy
- [x] Выполнить rolling update
- [x] Выполнить rollback
- [x] Сломать selector Service и найти проблему
- [x] Сломать readiness probe и найти проблему
- [x] Создать CrashLoopBackOff и исправить
- [x] Создать OOMKilled и исправить
- [x] Создать PVC Pending и исправить
- [x] Выполнить cordon, drain и uncordon Node

---

## 29. Interview Questions

- [x] Что происходит после `kubectl apply`
- [x] Как Kubernetes создает Pod
- [x] Deployment vs StatefulSet
- [x] Deployment vs DaemonSet
- [x] Pod vs Container
- [x] Service vs Ingress
- [x] ClusterIP vs NodePort vs LoadBalancer
- [x] ConfigMap vs Secret
- [x] PV vs PVC vs StorageClass
- [x] Requests vs limits
- [x] Liveness vs readiness vs startup probe
- [x] Taints vs tolerations
- [x] Node affinity vs Pod affinity
- [x] Role vs ClusterRole
- [x] Как Service находит Pods
- [x] Как работает DNS внутри Kubernetes
- [x] Как работает rolling update
- [x] Почему Pod находится в Pending
- [x] Почему возникает CrashLoopBackOff
- [x] Почему возникает OOMKilled
- [x] Как диагностировать недоступный Service
- [x] Как диагностировать Node NotReady