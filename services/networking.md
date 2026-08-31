# Networking

The VPC is the backbone of almost every architecture. Understand subnets, routing, and the two
firewalls (security groups vs NACLs), and a lot of the exam falls into place.

## VPC (Virtual Private Cloud)

Your isolated virtual network in a Region.

- **Subnets** live in a single AZ. **Public** subnet = has a route to an Internet Gateway. **Private** = no direct internet route.
- **CIDR blocks** define the IP range; subnets carve it up.
- **Route tables** control where traffic goes. **Internet Gateway (IGW)** connects a VPC to the internet.
- **NAT Gateway** lets private-subnet instances reach the internet *outbound* (patches, API calls) without being reachable inbound. Managed, HA within an AZ (deploy one per AZ for resilience). NAT Instance is the old self-managed alternative.

---

## The two firewalls

| | Security Group | NACL |
|--|----------------|------|
| Level | Instance / ENI | Subnet |
| State | Stateful (return traffic auto-allowed) | Stateless (must allow both directions) |
| Rules | Allow only | Allow and Deny |
| Evaluation | All rules | In rule-number order, first match wins |

Default posture: security groups are your primary control; NACLs add a coarse subnet-level layer.

---

## Connecting VPCs and on-prem

- **VPC Peering** - one-to-one private connection between two VPCs. Not transitive.
- **Transit Gateway** - hub-and-spoke that connects many VPCs and on-prem at scale. Use instead of a mesh of peerings.
- **VPN (Site-to-Site)** - encrypted tunnel over the internet to on-prem. Quick, cheaper, variable performance.
- **Direct Connect (DX)** - dedicated physical line to AWS. Consistent low latency and high bandwidth; use when performance/consistency matters. Combine with VPN for encryption.
- **PrivateLink / interface endpoints** - expose/consume services privately without internet.

**Clue words:** "many VPCs + on-prem, scalable" -> Transit Gateway. "dedicated, consistent, high-bandwidth link" -> Direct Connect. "encrypted tunnel, quick to set up" -> Site-to-Site VPN.

---

## VPC endpoints (keep traffic off the internet)

- **Gateway endpoints** - for **S3 and DynamoDB** only. Free. Added as a route table entry.
- **Interface endpoints (PrivateLink)** - ENI with a private IP for most other AWS services. Hourly + data cost.

Use these to let private subnets reach AWS services without a NAT gateway or internet.

---

## Route 53 (DNS)

- Registrar + authoritative DNS + health checks.
- **Routing policies:** simple, weighted, latency-based, failover, geolocation, geoproximity, multivalue.
- **Alias records** point to AWS resources (ELB, CloudFront, S3) at no cost, at the zone apex.

**Clue words:** "route users to the lowest-latency Region" -> latency-based. "active/passive DR" -> failover. "split traffic for A/B or canary" -> weighted.

---

## Content delivery and acceleration

- **CloudFront** - CDN caching at edge locations; TLS, WAF, DDoS protection. For HTTP(S) content.
- **Global Accelerator** - anycast static IPs over the AWS backbone; for TCP/UDP and fast regional failover.

See [`decision-guides/networking.md`](../decision-guides/networking.md).

---

## Quick self-check

- Public vs private subnet - what single thing decides it? (route to IGW)
- Security group vs NACL - stateful vs stateless, instance vs subnet.
- NAT Gateway vs Internet Gateway - which enables outbound-only for private subnets?
- Which VPC endpoint type is free and only for S3/DynamoDB?
- Transit Gateway vs VPC Peering - when does peering stop scaling?
