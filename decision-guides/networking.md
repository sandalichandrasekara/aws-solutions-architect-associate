# Decision Guide: Networking

Two families of decisions dominate: which **load balancer**, and how to **connect/deliver**
traffic (VPCs, on-prem, and the edge).

## Load balancers: ALB vs NLB vs GWLB

| | ALB | NLB | GWLB |
|--|-----|-----|------|
| Layer | 7 (HTTP/HTTPS) | 4 (TCP/UDP/TLS) | 3 (gateway) |
| Routing | Host/path/header, content-based | Ultra-low latency, connection-based | To 3rd-party appliances |
| Static IP | No (use Global Accelerator) | Yes (elastic IP per AZ) | via appliances |
| Millions of req/s, low latency | No | Yes | - |
| WebSocket / HTTP/2 | Yes | - | - |
| Use when | Web apps, microservices, path routing | Extreme performance, TCP/UDP, static IP | Insert firewalls/IDS/IPS inline |

**Pick ALB** for HTTP(S) apps needing content-based routing.
**Pick NLB** for TCP/UDP, extreme throughput/low latency, or a static IP.
**Pick GWLB** to run third-party network appliances (firewalls) transparently.

---

## CloudFront vs Global Accelerator

| | CloudFront | Global Accelerator |
|--|-----------|--------------------|
| Purpose | Cache + deliver content (CDN) | Route to nearest healthy endpoint |
| Layer / protocol | HTTP(S) content | TCP/UDP (any) |
| Caches content? | Yes (edge caching) | No (just routing/acceleration) |
| Static anycast IPs | No | Yes |
| Use when | Static/dynamic web content near users | Non-HTTP apps, static IPs, fast failover |

**"Cache static content close to users"** -> CloudFront.
**"TCP/UDP, static IPs, global low latency + fast regional failover"** -> Global Accelerator.

---

## Connecting VPCs and on-prem

| Need | Reach for |
|------|-----------|
| Two VPCs, private, simple | VPC Peering (not transitive) |
| Many VPCs + on-prem at scale | Transit Gateway (hub-and-spoke) |
| Encrypted tunnel to on-prem, quick/cheap | Site-to-Site VPN |
| Dedicated, consistent, high bandwidth | Direct Connect (add VPN for encryption) |
| Reach AWS services privately, no internet | VPC endpoints / PrivateLink |
| Expose your service to other VPCs privately | PrivateLink (interface endpoint) |

**Trap:** a growing mesh of VPC peerings -> switch to **Transit Gateway**.
**Trap:** "consistent performance, not over the public internet" -> **Direct Connect**, not VPN.

---

## VPC endpoints

- **Gateway endpoint** - S3 and DynamoDB only, free, route-table based.
- **Interface endpoint (PrivateLink)** - most other services, ENI + private IP, hourly cost.

Use to let private subnets talk to AWS services without a NAT gateway or internet route.

---

## Security groups vs NACLs

| | Security Group | NACL |
|--|----------------|------|
| Scope | Instance / ENI | Subnet |
| State | Stateful | Stateless |
| Rules | Allow only | Allow + Deny, ordered |

Default to security groups; use NACLs for coarse subnet-level allow/deny (e.g. block an IP range).

---

## Route 53 routing policy selection

| Goal | Policy |
|------|--------|
| One resource | Simple |
| Lowest latency Region | Latency-based |
| Active/passive DR | Failover |
| Split traffic (canary/AB) | Weighted |
| Route by user location | Geolocation / Geoproximity |
| Return several healthy answers | Multivalue |
