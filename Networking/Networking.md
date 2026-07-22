[[Networking. Конспект - черновик.]]

## 1. Основы сетей

- [x] IP address
- [x] IPv4
- [x] IPv6 basics
- [x] Private IP ranges
- [x] Public IP
- [x] MAC address
- [x] Subnet
- [x] Subnet mask
- [x] CIDR
- [x] Broadcast address
- [x] Network address
- [x] Gateway
- [x] Default gateway
- [x] Port
- [x] Socket
- [x] Protocol
- [x] MTU
- [x] MSS
- [x] TTL
- [x] ARP
- [x] NAT
- [x] PAT

---

## 2. TCP/IP и OSI

- [x] OSI model
- [x] TCP/IP model
- [x] L1 Physical
- [x] L2 Data Link
- [x] L3 Network
- [x] L4 Transport
- [x] L7 Application
- [x] Ethernet
- [x] Encapsulation
- [x] Decapsulation
- [x] Packet
- [x] Frame
- [x] Segment
- [x] Datagram

---

## 3. Routing

- [x] Routing table
- [x] Static route
- [x] Dynamic routing basics
- [x] Default route
- [x] Host route
- [x] Network route
- [x] Route metric
- [x] Policy routing basics
- [x] Asymmetric routing
- [x] Blackhole route

---

## 4. TCP

- [x] TCP handshake
- [x] SYN
- [x] SYN-ACK
- [x] ACK
- [x] FIN
- [x] RST
- [x] TCP states
- [x] LISTEN
- [x] ESTABLISHED
- [x] TIME_WAIT
- [x] CLOSE_WAIT
- [x] FIN_WAIT
- [x] SYN_SENT
- [x] SYN_RECV
- [x] Retransmission
- [x] Window size
- [x] Congestion control basics
- [x] Slow start basics
- [x] Keepalive
- [x] Backlog
- [x] Ephemeral ports
- [x] Nagle algorithm basics

---

## 5. UDP

- [x] UDP basics
- [x] Connectionless protocol
- [x] DNS over UDP
- [x] QUIC basics
- [x] Когда использовать UDP
- [x] Ограничения UDP

---

## 6. ICMP

- [x] Echo Request
- [x] Echo Reply
- [x] Destination Unreachable
- [x] Time Exceeded
- [x] Path MTU Discovery
- [x] ICMP troubleshooting

---

## 7. DNS

- [x] DNS architecture
- [x] Recursive resolver
- [x] Authoritative DNS
- [x] DNS cache
- [x] DNS propagation
- [x] TTL
- [x] A
- [x] AAAA
- [x] CNAME
- [x] MX
- [x] TXT
- [x] NS
- [x] PTR
- [x] SOA
- [x] SRV
- [x] Reverse DNS
- [x] Split-horizon DNS
- [x] `/etc/hosts`
- [x] `/etc/resolv.conf`
- [x] `systemd-resolved`
- [x] DNS over TLS basics

---

## 8. HTTP / HTTPS

- [x] HTTP request
- [x] HTTP response
- [x] Methods
- [x] GET
- [x] POST
- [x] PUT
- [x] PATCH
- [x] DELETE
- [x] HEAD
- [x] OPTIONS
- [x] Headers
- [x] Host header
- [x] User-Agent
- [x] Authorization
- [x] Cookie
- [x] Content-Type
- [x] Content-Length
- [x] Keep-Alive
- [x] Status codes
- [x] 2xx
- [x] 3xx
- [x] 4xx
- [x] 5xx
- [x] HTTP/1.1
- [x] HTTP/2 basics
---

## 9. TLS / SSL

- [x] TLS handshake
- [x] Certificate
- [x] Certificate chain
- [x] Root CA
- [x] Intermediate CA
- [x] Public key
- [x] Private key
- [x] CSR
- [x] SAN
- [x] CN
- [x] SNI
- [x] TLS termination
- [x] TLS passthrough
- [x] mTLS basics
- [x] Self-signed certificate
- [x] Let's Encrypt basics

---

## 10. Linux Networking

- [x] Интерфейсы
- [x] Loopback
- [x] Bridge
- [x] Bonding basics
- [x] VLAN basics
- [x] Routing
- [x] ARP cache
- [x] Neighbor table

---

## 11. Проверка соединений

- [x] Listening sockets
- [x] Established connections
- [x] TCP sockets
- [ ] UDP sockets

---

## 12. Firewall и NAT

- [x] Stateful firewall
- [x] Stateless firewall
- [x] SNAT
- [x] DNAT
- [x] Masquerade
- [x] Port forwarding
- [x] Connection tracking


---

## 13. Load Balancing

- [x] L4 Load Balancer
- [x] L7 Load Balancer
- [x] Reverse Proxy
- [x] Forward Proxy
- [x] Round Robin
- [x] Least Connections
- [x] IP Hash
- [x] Sticky Sessions
- [x] Health Checks
- [x] Active Health Checks
- [x] Passive Health Checks
- [x] Connection Draining

---

## 14. Packet Capture

- [x] Packet capture
- [x] Packet filtering
- [x] PCAP
---

## 15. Docker Networking

- [x] bridge network
- [x] host network
- [x] none network
- [x] overlay network
- [x] docker0
- [x] Port publishing
- [x] Container DNS
- [x] NAT в Docker

---

## 16. Kubernetes Networking

- [x] Pod IP
- [x] Node IP
- [x] ClusterIP
- [x] NodePort
- [x] LoadBalancer
- [x] Headless Service
- [x] Endpoints
- [x] EndpointSlice
- [x] kube-proxy
- [x] iptables mode
- [x] IPVS mode
- [x] CNI
- [x] Calico
- [x] Cilium
- [x] Flannel
- [x] CoreDNS
- [x] Ingress
- [x] Ingress Controller
- [x] NetworkPolicy
- [x] externalTrafficPolicy

---

## 17. Cloud Networking

- [x] VPC
- [x] Public subnet
- [x] Private subnet
- [x] Route table
- [x] Internet Gateway
- [x] NAT Gateway
- [x] Security Group
- [x] Network ACL
- [x] VPN basics
- [x] VPC Peering
- [x] Transit Gateway basics
- [x] PrivateLink basics
- [x] Cloud DNS

---

## 18. Performance

- [x] Latency
- [x] RTT
- [x] Throughput
- [x] Bandwidth
- [x] Packet loss
- [x] Jitter
- [x] TCP backlog
- [x] Socket buffers
- [x] Interface saturation

---

## 19. Troubleshooting

- [ ] DNS doesn't resolve
- [ ] Connection refused
- [ ] Connection timeout
- [ ] No route to host
- [ ] Network unreachable
- [ ] TLS handshake failed
- [ ] Wrong certificate
- [ ] 502 Bad Gateway
- [ ] 503 Service Unavailable
- [ ] 504 Gateway Timeout
- [ ] Service listens only on localhost
- [ ] Firewall blocks traffic
- [ ] Wrong routing
- [ ] MTU issues
- [ ] Packet loss
- [ ] High latency
- [ ] TIME_WAIT leak
- [ ] CLOSE_WAIT leak
- [ ] Ephemeral port exhaustion

---

## 20. Interview Questions

- [ ] Что происходит после ввода `https://example.com` в браузере?
- [ ] TCP vs UDP
- [ ] Почему `ping` работает, а `curl` — нет?
- [ ] Почему `curl` по IP работает, а по домену — нет?
- [ ] Почему возникает `TIME_WAIT`?
- [ ] 
- [ ] Чем `TIME_WAIT` отличается от `CLOSE_WAIT`?
- [ ] Что означает `Connection refused`?
- [ ] Что означает `Connection timeout`?
- [ ] Как проверить, слушает ли сервис порт?
- [ ] Как проверить доступность порта?
- [ ] Как проверить TLS-сертификат?
- [ ] Что такое SNI?
- [ ] Что такое NAT?
- [ ] Чем SNAT отличается от DNAT?
- [ ] Что такое CIDR?
- [ ] Что такое MTU?
- [ ] Как работает Kubernetes Service?
- [ ] Как работает Ingress?
- [ ] Что делает kube-proxy?
- [ ] Что делает CNI?
- [ ] Как дебажить сетевую проблему в Kubernetes?