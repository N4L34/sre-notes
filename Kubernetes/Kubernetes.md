[[Kubernetes. Конспект]]
# Kubernetes

## 1. Основы

- [ ] Что такое Kubernetes
- [ ] Зачем нужен Kubernetes
- [ ] Kubernetes architecture
- [ ] Control Plane
- [ ] Worker Node
- [ ] Cluster
- [ ] Declarative configuration
- [ ] Desired state
- [ ] Reconciliation loop
- [ ] Kubernetes objects
- [ ] Namespaces

---

## 2. Control Plane Components

- [ ] kube-apiserver
- [ ] etcd
- [ ] kube-scheduler
- [ ] kube-controller-manager
- [ ] cloud-controller-manager
- [ ] Как компоненты Control Plane взаимодействуют между собой
- [ ] High Availability Control Plane

---

## 3. Node Components

- [ ] kubelet
- [ ] kube-proxy
- [ ] Container Runtime
- [ ] CRI
- [ ] CNI
- [ ] CSI
- [ ] Node status
- [ ] Node conditions

---

## 4. Pods

- [ ] Что такое Pod
- [ ] Pod lifecycle
- [ ] Pod phases
- [ ] Containers в одном Pod
- [ ] Shared network namespace
- [ ] Shared volumes
- [ ] Init Containers
- [ ] Sidecar containers
- [ ] Multi-container Pod
- [ ] Static Pods
- [ ] Pod restartPolicy
- [ ] Pod termination
- [ ] Graceful shutdown
- [ ] Termination grace period

---

## 5. Labels, Selectors и Annotations

- [ ] Labels
- [ ] Label selectors
- [ ] MatchLabels
- [ ] MatchExpressions
- [ ] Annotations
- [ ] Как Service находит Pods
- [ ] Как Deployment управляет Pods

---

## 6. ReplicaSet и Deployment

- [ ] ReplicaSet
- [ ] Deployment
- [ ] Replicas
- [ ] Rolling Update
- [ ] Recreate strategy
- [ ] maxSurge
- [ ] maxUnavailable
- [ ] Rollout status
- [ ] Rollout history
- [ ] Rollback
- [ ] Revision
- [ ] Pause и resume rollout

---

## 7. StatefulSet

- [ ] Что такое StatefulSet
- [ ] StatefulSet vs Deployment
- [ ] Stable Pod names
- [ ] Stable network identity
- [ ] Ordered deployment
- [ ] Ordered termination
- [ ] volumeClaimTemplates
- [ ] Headless Service
- [ ] Stateful applications
- [ ] Update strategies

---

## 8. DaemonSet и Jobs

- [ ] DaemonSet
- [ ] По одному Pod на Node
- [ ] Типичные применения DaemonSet
- [ ] Job
- [ ] CronJob
- [ ] completions
- [ ] parallelism
- [ ] backoffLimit
- [ ] restartPolicy для Job
- [ ] CronJob concurrencyPolicy
- [ ] successfulJobsHistoryLimit
- [ ] failedJobsHistoryLimit

---

## 9. Services

- [ ] Что такое Service
- [ ] ClusterIP
- [ ] NodePort
- [ ] LoadBalancer
- [ ] ExternalName
- [ ] Headless Service
- [ ] Service selector
- [ ] Endpoints
- [ ] EndpointSlice
- [ ] targetPort
- [ ] port
- [ ] nodePort
- [ ] Session affinity
- [ ] externalTrafficPolicy
- [ ] internalTrafficPolicy

---

## 10. Kubernetes Networking

- [ ] Kubernetes network model
- [ ] Pod-to-Pod communication
- [ ] Pod-to-Service communication
- [ ] Node-to-Pod communication
- [ ] CNI
- [ ] Container network namespace
- [ ] Pod IP
- [ ] Service IP
- [ ] ClusterIP
- [ ] kube-proxy
- [ ] iptables mode
- [ ] IPVS mode
- [ ] eBPF basics
- [ ] CoreDNS
- [ ] Service DNS
- [ ] Pod DNS
- [ ] NetworkPolicy
- [ ] Ingress traffic
- [ ] Egress traffic

---

## 11. Ingress

- [ ] Ingress
- [ ] Ingress Controller
- [ ] Ingress resource
- [ ] Host-based routing
- [ ] Path-based routing
- [ ] TLS termination
- [ ] Default backend
- [ ] Nginx Ingress Controller
- [ ] Ingress vs Service LoadBalancer
- [ ] Gateway API basics
- [ ] Troubleshooting Ingress

---

## 12. ConfigMap и Secret

- [ ] ConfigMap
- [ ] Secret
- [ ] Secret не является шифрованием
- [ ] Environment variables
- [ ] envFrom
- [ ] Volume mounts
- [ ] Projected volumes
- [ ] Immutable ConfigMap и Secret
- [ ] Обновление конфигурации
- [ ] Secret rotation basics
- [ ] External Secrets basics
- [ ] Sealed Secrets basics

---

## 13. Storage

- [ ] Volumes
- [ ] emptyDir
- [ ] hostPath
- [ ] PersistentVolume
- [ ] PersistentVolumeClaim
- [ ] StorageClass
- [ ] Dynamic provisioning
- [ ] Static provisioning
- [ ] Access Modes
- [ ] ReadWriteOnce
- [ ] ReadOnlyMany
- [ ] ReadWriteMany
- [ ] Volume Binding Mode
- [ ] Reclaim Policy
- [ ] Retain
- [ ] Delete
- [ ] CSI
- [ ] Volume expansion
- [ ] Volume snapshots basics

---

## 14. Resource Management

- [ ] CPU requests
- [ ] CPU limits
- [ ] Memory requests
- [ ] Memory limits
- [ ] Scheduling по requests
- [ ] CPU throttling
- [ ] OOMKilled
- [ ] QoS classes
- [ ] Guaranteed
- [ ] Burstable
- [ ] BestEffort
- [ ] LimitRange
- [ ] ResourceQuota
- [ ] Ephemeral storage requests и limits

---

## 15. Probes и Health Checks

- [ ] Liveness probe
- [ ] Readiness probe
- [ ] Startup probe
- [ ] HTTP probe
- [ ] TCP probe
- [ ] Exec probe
- [ ] initialDelaySeconds
- [ ] periodSeconds
- [ ] timeoutSeconds
- [ ] failureThreshold
- [ ] successThreshold
- [ ] Неправильные probes
- [ ] Probe-induced restart loop

---

## 16. Scheduling

- [ ] Как работает kube-scheduler
- [ ] nodeSelector
- [ ] Node affinity
- [ ] Pod affinity
- [ ] Pod anti-affinity
- [ ] Taints
- [ ] Tolerations
- [ ] Topology spread constraints
- [ ] nodeName
- [ ] PriorityClass
- [ ] Pod priority
- [ ] Preemption
- [ ] Scheduler predicates и scoring на базовом уровне

---

## 17. Autoscaling

- [ ] Horizontal Pod Autoscaler
- [ ] HPA по CPU
- [ ] HPA по memory
- [ ] Custom metrics
- [ ] External metrics
- [ ] Metrics Server
- [ ] Vertical Pod Autoscaler basics
- [ ] Cluster Autoscaler
- [ ] KEDA basics
- [ ] HPA и requests
- [ ] Scale-up и scale-down behavior

---

## 18. Security

- [ ] Authentication
- [ ] Authorization
- [ ] RBAC
- [ ] Role
- [ ] ClusterRole
- [ ] RoleBinding
- [ ] ClusterRoleBinding
- [ ] ServiceAccount
- [ ] SecurityContext
- [ ] runAsUser
- [ ] runAsNonRoot
- [ ] fsGroup
- [ ] Linux capabilities
- [ ] privileged container
- [ ] allowPrivilegeEscalation
- [ ] readOnlyRootFilesystem
- [ ] seccomp
- [ ] Pod Security Standards
- [ ] NetworkPolicy
- [ ] Admission Controllers
- [ ] Image security basics
- [ ] Secrets security
- [ ] Least privilege

---

## 19. kubectl

- [ ] kubectl get
- [ ] kubectl describe
- [ ] kubectl logs
- [ ] kubectl exec
- [ ] kubectl apply
- [ ] kubectl delete
- [ ] kubectl edit
- [ ] kubectl patch
- [ ] kubectl scale
- [ ] kubectl rollout
- [ ] kubectl top
- [ ] kubectl explain
- [ ] kubectl config
- [ ] kubectl context
- [ ] kubectl port-forward
- [ ] kubectl cp
- [ ] kubectl auth can-i
- [ ] JSONPath
- [ ] Custom columns
- [ ] Field selectors
- [ ] Label selectors
- [ ] Dry run

---

## 20. YAML и API Objects

- [ ] apiVersion
- [ ] kind
- [ ] metadata
- [ ] spec
- [ ] status
- [ ] Kubernetes API groups
- [ ] Object names
- [ ] UID
- [ ] ownerReferences
- [ ] finalizers
- [ ] Generation
- [ ] ResourceVersion
- [ ] Declarative vs imperative management

---

## 21. Helm и Package Management

- [ ] Helm Chart
- [ ] Release
- [ ] values.yaml
- [ ] Templates
- [ ] helm install
- [ ] helm upgrade
- [ ] helm rollback
- [ ] helm template
- [ ] helm lint
- [ ] Helm hooks basics
- [ ] Helm dependencies
- [ ] Debugging Helm release

---

## 22. Cluster Administration

- [ ] kubeconfig
- [ ] Contexts
- [ ] Certificates
- [ ] Cluster certificates
- [ ] Certificate rotation basics
- [ ] etcd backup
- [ ] etcd restore
- [ ] Node drain
- [ ] Node cordon
- [ ] Node uncordon
- [ ] Node maintenance
- [ ] Kubernetes upgrades basics
- [ ] Version skew basics
- [ ] Control Plane HA
- [ ] Worker node lifecycle

---

## 23. Observability

- [ ] Kubernetes events
- [ ] Container logs
- [ ] kubectl logs
- [ ] Previous container logs
- [ ] Metrics Server
- [ ] Prometheus
- [ ] kube-state-metrics
- [ ] Node Exporter
- [ ] Kubernetes dashboards basics
- [ ] Application metrics
- [ ] Cluster metrics
- [ ] Audit logs basics
- [ ] Distributed tracing basics

---

## 24. Troubleshooting Pods

- [ ] Pod Pending
- [ ] CrashLoopBackOff
- [ ] ImagePullBackOff
- [ ] ErrImagePull
- [ ] CreateContainerConfigError
- [ ] ContainerCreating
- [ ] OOMKilled
- [ ] Evicted
- [ ] Completed
- [ ] Terminating Pod
- [ ] Init Container failed
- [ ] Readiness probe failed
- [ ] Liveness probe failed
- [ ] Wrong command или entrypoint
- [ ] Missing ConfigMap или Secret
- [ ] Permission denied

---

## 25. Troubleshooting Networking

- [ ] Service недоступен
- [ ] Pod не видит другой Pod
- [ ] Pod не видит Service
- [ ] DNS не работает
- [ ] CoreDNS проблемы
- [ ] Нет Endpoints
- [ ] Неправильный selector
- [ ] Неправильный targetPort
- [ ] Ingress возвращает 404
- [ ] Ingress возвращает 502
- [ ] NetworkPolicy блокирует трафик
- [ ] NodePort недоступен
- [ ] LoadBalancer Pending
- [ ] Проверка через curl, nslookup, dig и nc
- [ ] Debug Pod

---

## 26. Troubleshooting Storage

- [ ] PVC Pending
- [ ] PV не создается
- [ ] StorageClass отсутствует
- [ ] Неподходящий Access Mode
- [ ] Volume не монтируется
- [ ] Multi-Attach error
- [ ] Permission denied на volume
- [ ] Node affinity conflict у PV
- [ ] Volume заполнен
- [ ] StatefulSet не получает PVC

---

## 27. Troubleshooting Scheduling и Nodes

- [ ] FailedScheduling
- [ ] Insufficient CPU
- [ ] Insufficient memory
- [ ] Untolerated taint
- [ ] NodeSelector mismatch
- [ ] Affinity mismatch
- [ ] Node NotReady
- [ ] DiskPressure
- [ ] MemoryPressure
- [ ] PIDPressure
- [ ] Pod eviction
- [ ] Node unreachable
- [ ] kubelet не работает
- [ ] Container runtime не работает

---

## 28. Практика

- [ ] Создать Deployment
- [ ] Создать Service
- [ ] Настроить Ingress
- [ ] Передать конфигурацию через ConfigMap
- [ ] Передать Secret
- [ ] Подключить PVC
- [ ] Создать StatefulSet
- [ ] Создать DaemonSet
- [ ] Создать Job и CronJob
- [ ] Настроить probes
- [ ] Настроить requests и limits
- [ ] Настроить HPA
- [ ] Настроить RBAC
- [ ] Настроить NetworkPolicy
- [ ] Выполнить rolling update
- [ ] Выполнить rollback
- [ ] Сломать selector Service и найти проблему
- [ ] Сломать readiness probe и найти проблему
- [ ] Создать CrashLoopBackOff и исправить
- [ ] Создать OOMKilled и исправить
- [ ] Создать PVC Pending и исправить
- [ ] Выполнить cordon, drain и uncordon Node

---

## 29. Interview Questions

- [ ] Что происходит после `kubectl apply`
- [ ] Как Kubernetes создает Pod
- [ ] Deployment vs StatefulSet
- [ ] Deployment vs DaemonSet
- [ ] Pod vs Container
- [ ] Service vs Ingress
- [ ] ClusterIP vs NodePort vs LoadBalancer
- [ ] ConfigMap vs Secret
- [ ] PV vs PVC vs StorageClass
- [ ] Requests vs limits
- [ ] Liveness vs readiness vs startup probe
- [ ] Taints vs tolerations
- [ ] Node affinity vs Pod affinity
- [ ] Role vs ClusterRole
- [ ] Как Service находит Pods
- [ ] Как работает DNS внутри Kubernetes
- [ ] Как работает rolling update
- [ ] Почему Pod находится в Pending
- [ ] Почему возникает CrashLoopBackOff
- [ ] Почему возникает OOMKilled
- [ ] Как диагностировать недоступный Service
- [ ] Как диагностировать Node NotReady