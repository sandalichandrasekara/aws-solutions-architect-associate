# Domain 3 - Design High-Performing Architectures (24%)

Performance = right resource, right size, right place, with caching where it helps. The exam
rewards picking the managed/elastic option and putting data close to compute and users.

## What this domain tests

- High-performing and scalable storage, compute, database, and network solutions.

---

## High-performing compute

- **Right-size instances:** match the family to the workload (compute-, memory-, storage-optimized). Do not guess-and-oversize.
- **Serverless** (Lambda, Fargate) scales automatically with demand - strong default when the workload is bursty or event-driven.
- **Auto Scaling** keeps performance steady as load changes.
- **Placement groups:** cluster (low latency, high throughput within one AZ), spread (hardware isolation), partition (large distributed workloads like Hadoop).

---

## High-performing storage

- **S3** for scalable object storage; effectively unlimited throughput, scales with request rate.
- **EBS volume types:** gp3 (general purpose, baseline default), io1/io2 (provisioned IOPS for latency-sensitive DBs), st1 (throughput HDD), sc1 (cold HDD). Pick by IOPS vs throughput vs cost.
- **EFS** for shared file storage across many instances; **FSx** for specialized file systems (Windows, Lustre for HPC).
- **Instance store** = ephemeral, physically attached, very fast, lost on stop. Use for caches/scratch, never for durable data.

See [`decision-guides/storage.md`](../decision-guides/storage.md).

---

## High-performing databases

- **Read-heavy:** add read replicas or a cache (ElastiCache, DAX for DynamoDB).
- **Unpredictable/huge scale, key-value:** DynamoDB (single-digit ms, on-demand or provisioned + auto scaling).
- **Relational at scale:** Aurora (up to 15 read replicas, auto-scaling storage).
- **Caching removes load** from the database and cuts latency. Cache-aside is the common pattern.

See [`decision-guides/databases.md`](../decision-guides/databases.md).

---

## High-performing networking and delivery

- **CloudFront** (CDN): cache content at edge locations close to users. Great for static and dynamic content, plus a security layer (WAF, TLS).
- **Global Accelerator:** uses the AWS backbone and anycast IPs to route users to the nearest healthy endpoint - for non-HTTP or when you need static anycast IPs and fast failover.
- **Placement, endpoints, enhanced networking** reduce latency inside the VPC.
- **Route 53 routing policies:** latency-based, geolocation, weighted, failover - route users to the best endpoint.

**Clue words:** "cache static content near users" -> CloudFront. "TCP/UDP, static IPs, global low latency" -> Global Accelerator. "cache DynamoDB reads at microsecond latency" -> DAX.

---

## Caching cheat map

| Need | Reach for |
|------|-----------|
| Cache web/API content near users | CloudFront |
| Cache DB query results / sessions | ElastiCache (Redis/Memcached) |
| Cache DynamoDB reads (microseconds) | DAX |
| Cache API Gateway responses | API Gateway caching |

---

## Quick self-check

- gp3 vs io2 - when do you pay for provisioned IOPS?
- CloudFront vs Global Accelerator - which for HTTP static content, which for TCP/UDP + static IPs?
- Where would you add ElastiCache vs DAX?
- Which Route 53 policy sends users to the lowest-latency Region?
