# Cheat Sheet

High-value facts and the clue words that map a question to an answer. 

## Clue words -> answer

| If the question says... | Reach for |
|-------------------------|-----------|
| Temporary credentials / no hard-coded keys / cross-account | IAM role |
| Rotate database credentials automatically | Secrets Manager |
| Cheaper config/secrets, no rotation needed | SSM Parameter Store |
| Dedicated HSM / FIPS 140-2 Level 3 | CloudHSM |
| Control the encryption key policy & rotation | KMS customer managed key |
| Who made this API call? | CloudTrail |
| Continuous resource-config compliance | AWS Config |
| Threat detection from logs | GuardDuty |
| Find PII / sensitive data in S3 | Macie |
| Vulnerability scanning of EC2/ECR/Lambda | Inspector |
| Decouple / buffer / absorb spikes | SQS |
| One event -> many subscribers (fan-out) | SNS |
| Route events by content / SaaS integration | EventBridge |
| Real-time stream, replay, multiple readers | Kinesis |
| Cache static content near users | CloudFront |
| TCP/UDP, static IPs, global low latency | Global Accelerator |
| Cache DynamoDB reads (microseconds) | DAX |
| Cache DB reads / sessions | ElastiCache |
| Unknown/changing S3 access pattern | S3 Intelligent-Tiering |
| Archive, retrieval in hours, cheapest | Glacier Deep Archive |
| Shared files across many instances (Linux) | EFS |
| Windows/SMB shared files | FSx for Windows |
| HPC / Lustre file system | FSx for Lustre |
| Improve availability / survive AZ failure (DB) | Multi-AZ |
| Scale reads (DB) | Read replicas |
| Batch, interruptible, cheapest compute | EC2 Spot |
| Steady 24/7 workload, lowest long-term cost | Reserved / Savings Plans |
| Many VPCs + on-prem at scale | Transit Gateway |
| Dedicated, consistent, high-bandwidth link | Direct Connect |
| HTTP path/host routing | ALB |
| Extreme performance, TCP/UDP, static IP | NLB |
| Data warehouse / OLAP analytics | Redshift |
| Graph relationships | Neptune |
| Immutable ledger | QLDB |

---

## Numbers worth memorizing

- **S3 durability:** 11 nines. Max object size: 5 TB. Multipart upload for large objects.
- **Lambda:** max timeout 15 minutes. CPU scales with memory.
- **DynamoDB:** single-digit ms latency; DAX -> microseconds.
- **Passing score:** 720/1000. 65 questions, 130 minutes.
- **EBS gp3** is the modern default; **io2** for high IOPS.
- **NAT Gateway:** one per AZ for HA; costs money while running.

---

## Concepts people mix up

- **Durability vs availability** - survives data loss vs reachable now.
- **Multi-AZ vs read replica** - availability vs read scaling. Standby is *not* readable.
- **Security group vs NACL** - stateful/instance vs stateless/subnet. Explicit deny only exists in NACLs.
- **IAM policy** - explicit Deny always wins; default is implicit deny.
- **Gateway vs interface endpoint** - gateway = S3/DynamoDB, free; interface = PrivateLink, ENI, paid.
- **SQS vs SNS** - pull/one-consumer vs push/many-subscribers.
- **SQS vs Kinesis** - message deleted after read vs retained/replayable.
- **CloudFront vs Global Accelerator** - caches HTTP content vs routes TCP/UDP with static IPs.
- **On-Demand vs Reserved vs Spot** - spiky vs steady-committed vs interruptible-cheap.

---

## DR strategies (cheapest -> most expensive)

1. **Backup & Restore** - restore after disaster (hours).
2. **Pilot Light** - core running, scale up on failover.
3. **Warm Standby** - scaled-down full copy always on.
4. **Multi-Site Active/Active** - full copies serving traffic (near-zero RTO/RPO).

Lower RTO/RPO -> higher cost. Pick what the requirement asks for, not the fanciest.

---

## Exam tactics

- Read the **last sentence first** - it tells you what's actually being optimized (cost? latency? availability?).
- Eliminate answers that don't meet a **hard requirement** (e.g. "must be serverless", "no internet").
- Watch qualifiers: **cheapest**, **most highly available**, **least operational overhead**, **real-time**.
- "Least operational overhead" usually favors **managed/serverless** options.
- Two answers technically work? Pick the one that best matches the *emphasized* requirement.
- Flag and move on. Don't burn 5 minutes on one question.
