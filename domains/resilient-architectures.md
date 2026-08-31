# Domain 2 - Design Resilient Architectures (26%)

Resilience = the system keeps working (or recovers fast) when something fails. The exam wants
you to remove single points of failure and match recovery to the business requirement.

## What this domain tests

- Scalable and loosely coupled architectures.
- Highly available and fault-tolerant architectures.

---

## Availability vs durability (don't confuse them)

- **Durability** = will my data survive? (S3 = 11 nines of durability.)
- **Availability** = can I reach my system right now? (measured in nines of uptime.)

---

## Removing single points of failure

- **Multi-AZ** is the baseline for high availability. Spread compute across AZs behind a load balancer; run databases in Multi-AZ mode.
- **Multi-Region** is for disaster recovery and global availability - more complex and costly. Reach for it only when the requirement is regional failure survival or global low latency.
- **Decouple** components so one failing part does not take down the rest (queues, load balancers, stateless app tiers).

---

## Elastic and scalable

- **Auto Scaling Groups (ASG):** maintain a target capacity; scale on metrics (CPU, request count), schedules, or predictive scaling. Launch across multiple AZs.
- **Elastic Load Balancing:** ALB (HTTP/HTTPS, layer 7), NLB (TCP/UDP, layer 4, static IP), GWLB (third-party appliances). Health checks route traffic away from unhealthy targets.
- **Stateless design:** store session state externally (DynamoDB, ElastiCache) so any instance can serve any request and scaling is trivial.

See [`decision-guides/networking.md`](../decision-guides/networking.md).

---

## Decoupling patterns

- **SQS** (queue): buffer work, absorb spikes, retry with dead-letter queues. Consumer pulls.
- **SNS** (pub/sub): fan-out one message to many subscribers. Producer pushes.
- **EventBridge:** event bus with routing rules; integrates SaaS and AWS events.
- **Fan-out pattern:** SNS -> multiple SQS queues, each feeding a different consumer.

See [`decision-guides/messaging.md`](../decision-guides/messaging.md).

---

## Data resilience

- **RDS Multi-AZ:** synchronous standby in another AZ, automatic failover. For **availability**, not scaling.
- **RDS read replicas:** asynchronous, for **read scaling** (and can be promoted). Different job than Multi-AZ.
- **Aurora:** 6 copies of data across 3 AZs; self-healing storage; fast failover.
- **DynamoDB:** multi-AZ by default; Global Tables for multi-Region active-active.
- **S3:** already highly durable; add versioning and Cross-Region Replication for extra protection.
- **Backups:** RDS automated backups + snapshots; AWS Backup for centralized policy.

---

## Disaster recovery strategies (know the four)

| Strategy | RTO/RPO | Idea | Cost |
|----------|---------|------|------|
| Backup & Restore | Hours | Restore from backups after disaster | Lowest |
| Pilot Light | 10s of min | Core (e.g. DB) always running, scale up on failover | Low |
| Warm Standby | Minutes | Scaled-down full copy always running | Medium |
| Multi-Site Active/Active | Near zero | Full copy serving traffic in 2+ Regions | Highest |

- **RTO** = how long recovery takes. **RPO** = how much data loss is acceptable.
- Lower RTO/RPO -> more cost. Match the strategy to the stated requirement, not the fanciest option.

---

## Quick self-check

- Multi-AZ vs read replica - which is for availability, which for read scaling?
- Which DR strategy for "RTO of a few minutes, keep costs reasonable"?
- SQS vs SNS - pull vs push, one consumer vs many?
- What makes an app tier easy to auto scale? (statelessness)
