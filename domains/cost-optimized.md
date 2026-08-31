# Domain 4 - Design Cost-Optimized Architectures (20%)

The smallest domain, but easy points if you know the purchasing options and storage tiers. The
exam wants the cheapest option *that still meets the requirement* - not the cheapest overall.

## What this domain tests

- Cost-optimized storage, compute, database, and network solutions.

---

## Compute cost optimization

EC2 purchasing options - know when each wins:

| Option | Best for | Trade-off |
|--------|----------|-----------|
| On-Demand | Short-term, spiky, unpredictable | Highest per-hour cost |
| Reserved Instances / Savings Plans | Steady, predictable, long-term (1-3 yr) | Commitment |
| Spot Instances | Fault-tolerant, flexible, interruptible (batch, CI) | Can be reclaimed with 2-min notice |
| Dedicated Hosts | Licensing / compliance needing physical isolation | Most expensive |

- **Savings Plans** are usually the flexible modern answer for steady compute (covers EC2, Fargate, Lambda).
- **Spot** for interruption-tolerant work is the classic "reduce cost" answer.
- **Serverless / Fargate** avoids paying for idle capacity - pay per use.
- **Right-sizing and auto scaling** stop you paying for headroom you never use.

**Clue words:** "batch job, can tolerate interruption, cheapest" -> Spot. "steady 24/7 workload for 3 years" -> Reserved/Savings Plan.

---

## Storage cost optimization

- **S3 storage classes** by access pattern:
  - Standard - frequent access.
  - Standard-IA / One Zone-IA - infrequent (One Zone = cheaper, less resilient).
  - Glacier Instant / Flexible / Deep Archive - archival, cheapest, slower retrieval.
  - **Intelligent-Tiering** - unknown/changing access patterns; auto-moves objects. Default "not sure" answer.
- **Lifecycle policies** transition and expire objects automatically.
- **EBS:** move to gp3 from gp2 (cheaper for same/better performance); delete unattached volumes and stale snapshots.
- **Delete what you don't need:** unused Elastic IPs, idle load balancers, orphaned volumes.

See [`decision-guides/storage.md`](../decision-guides/storage.md).

---

## Database and network cost

- **Right-size and reserve** RDS for steady workloads (Reserved Instances).
- **DynamoDB on-demand** for spiky/unknown traffic; **provisioned + auto scaling** for predictable, cheaper at steady load.
- **Aurora Serverless** for intermittent/variable database load.
- **Data transfer is a hidden cost:** transfer *out* to the internet costs money; same-AZ traffic is cheapest; cross-Region and NAT gateway traffic add up. Use VPC endpoints and CloudFront to cut egress.

---

## Cost visibility tools

- **Cost Explorer** - visualize and analyze spend.
- **Budgets** - alert when spend/usage crosses a threshold.
- **Cost and Usage Report (CUR)** - detailed billing data.
- **Compute Optimizer** - right-sizing recommendations.
- **Trusted Advisor** - cost (and other) best-practice checks.

---

## Quick self-check

- Spot vs Reserved vs On-Demand - match each to a workload shape.
- Which S3 class for "access pattern is unknown and changing"?
- Where do surprise data-transfer costs come from, and how do you cut them?
- DynamoDB on-demand vs provisioned - which is cheaper at steady, predictable load?
