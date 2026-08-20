[[Networking. Конспект - черновик.]]

## 1. Основы сетей

- [ ] IP address
- [ ] IPv4
- [ ] IPv6 basics
- [ ] Private IP ranges
- [ ] Public IP
- [ ] MAC address
- [ ] Subnet
- [ ] Subnet mask
- [ ] CIDR
- [ ] Broadcast address
- [ ] Network address
- [ ] Gateway
- [ ] Default gateway
- [ ] Port
- [ ] Socket
- [ ] Protocol
- [ ] MTU
- [ ] MSS
- [ ] TTL
- [ ] ARP
- [ ] NAT
- [ ] PAT

---

## 2. TCP/IP и OSI

- [ ] OSI model
- [ ] TCP/IP model
- [ ] L1 Physical
- [ ] L2 Data Link
- [ ] L3 Network
- [ ] L4 Transport
- [ ] L7 Application
- [ ] Ethernet
- [ ] Encapsulation
- [ ] Decapsulation
- [ ] Packet
- [ ] Frame
- [ ] Segment
- [ ] Datagram

---

## 3. Routing

- [ ] Routing table
- [ ] Static route
- [ ] Dynamic routing basics
- [ ] Default route
- [ ] Host route
- [ ] Network route
- [ ] Route metric
- [ ] Policy routing basics
- [ ] Asymmetric routing
- [ ] Blackhole route

---

## 4. TCP

- [ ] TCP handshake
- [ ] SYN
- [ ] SYN-ACK
- [ ] ACK
- [ ] FIN
- [ ] RST
- [ ] TCP states
- [ ] LISTEN
- [ ] ESTABLISHED
- [ ] TIME_WAIT
- [ ] CLOSE_WAIT
- [ ] FIN_WAIT
- [ ] SYN_SENT
- [ ] SYN_RECV
- [ ] Retransmission
- [ ] Window size
- [ ] Congestion control basics
- [ ] Slow start basics
- [ ] Keepalive
- [ ] Backlog
- [ ] Ephemeral ports
- [ ] Nagle algorithm basics

---

## 5. UDP

- [ ] UDP basics
- [ ] Connectionless protocol
- [ ] DNS over UDP
- [ ] QUIC basics
- [ ] Когда использовать UDP
- [ ] Ограничения UDP

---

## 6. ICMP

- [ ] Echo Request
- [ ] Echo Reply
- [ ] Destination Unreachable
- [ ] Time Exceeded
- [ ] Path MTU Discovery
- [ ] ICMP troubleshooting

---

## 7. DNS

- [ ] DNS architecture
- [ ] Recursive resolver
- [ ] Authoritative DNS
- [ ] DNS cache
- [ ] DNS propagation
- [ ] TTL
- [ ] A
- [ ] AAAA
- [ ] CNAME
- [ ] MX
- [ ] TXT
- [ ] NS
- [ ] PTR
- [ ] SOA
- [ ] SRV
- [ ] Reverse DNS
- [ ] Split-horizon DNS
- [ ] `/etc/hosts`
- [ ] `/etc/resolv.conf`
- [ ] `systemd-resolved`
- [ ] DNS over TLS basics

---

## 8. HTTP / HTTPS

- [ ] HTTP request
- [ ] HTTP response
- [ ] Methods
- [ ] GET
- [ ] POST
- [ ] PUT
- [ ] PATCH
- [ ] DELETE
- [ ] HEAD
- [ ] OPTIONS
- [ ] Headers
- [ ] Host header
- [ ] User-Agent
- [ ] Authorization
- [ ] Cookie
- [ ] Content-Type
- [ ] Content-Length
- [ ] Keep-Alive
- [ ] Status codes
- [ ] 2xx
- [ ] 3xx
- [ ] 4xx
- [ ] 5xx
- [ ] HTTP/1.1
- [ ] HTTP/2 basics
---

## 9. TLS / SSL

- [ ] TLS handshake
- [ ] Certificate
- [ ] Certificate chain
- [ ] Root CA
- [ ] Intermediate CA
- [ ] Public key
- [ ] Private key
- [ ] CSR
- [ ] SAN
- [ ] CN
- [ ] SNI
- [ ] TLS termination
- [ ] TLS passthrough
- [ ] mTLS basics
- [ ] Self-signed certificate
- [ ] Let's Encrypt basics

---

## 10. Linux Networking

- [ ] Интерфейсы
- [ ] Loopback
- [ ] Bridge
- [ ] Bonding basics
- [ ] VLAN basics
- [ ] Routing
- [ ] ARP cache
- [ ] Neighbor table

---

## 11. Проверка соединений

- [ ] Listening sockets
- [ ] Established connections
- [ ] TCP sockets
- [ ] UDP sockets

---

## 12. Firewall и NAT

- [ ] Stateful firewall
- [ ] Stateless firewall
- [ ] SNAT
- [ ] DNAT
- [ ] Masquerade
- [ ] Port forwarding
- [ ] Connection tracking


---

## 13. Load Balancing

- [ ] L4 Load Balancer
- [ ] L7 Load Balancer
- [ ] Reverse Proxy
- [ ] Forward Proxy
- [ ] Round Robin
- [ ] Least Connections
- [ ] IP Hash
- [ ] Sticky Sessions
- [ ] Health Checks
- [ ] Active Health Checks
- [ ] Passive Health Checks
- [ ] Connection Draining

---

## 14. Packet Capture

- [ ] Packet capture
- [ ] Packet filtering
- [ ] PCAP
---

## 15. Docker Networking

- [ ] bridge network
- [ ] host network
- [ ] none network
- [ ] overlay network
- [ ] docker0
- [ ] Port publishing
- [ ] Container DNS
- [ ] NAT в Docker

---

## 16. Kubernetes Networking

- [ ] Pod IP
- [ ] Node IP
- [ ] ClusterIP
- [ ] NodePort
- [ ] LoadBalancer
- [ ] Headless Service
- [ ] Endpoints
- [ ] EndpointSlice
- [ ] kube-proxy
- [ ] iptables mode
- [ ] IPVS mode
- [ ] CNI
- [ ] Calico
- [ ] Cilium
- [ ] Flannel
- [ ] CoreDNS
- [ ] Ingress
- [ ] Ingress Controller
- [ ] NetworkPolicy
- [ ] externalTrafficPolicy

---

## 17. Cloud Networking

- [ ] VPC
- [ ] Public subnet
- [ ] Private subnet
- [ ] Route table
- [ ] Internet Gateway
- [ ] NAT Gateway
- [ ] Security Group
- [ ] Network ACL
- [ ] VPN basics
- [ ] VPC Peering
- [ ] Transit Gateway basics
- [ ] PrivateLink basics
- [ ] Cloud DNS

---

## 18. Performance

- [ ] Latency
- [ ] RTT
- [ ] Throughput
- [ ] Bandwidth
- [ ] Packet loss
- [ ] Jitter
- [ ] TCP backlog
- [ ] Socket buffers
- [ ] Interface saturation

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