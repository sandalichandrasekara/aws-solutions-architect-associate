# Scenarios

Scenario-first practice. These are **not** exam questions - they're prompts to make you *reason*
like an architect. For each one: read the requirement, decide before you scroll, then compare.

**How to use:** cover the "Thinking" and "Answer" sections. Say your solution out loud, including
*why not* the alternatives. Then read. The gap between your reasoning and the answer is your study
list.

No brain dumps here. Every scenario is written to teach a trade-off.

---

## Scenario 1 - Sudden traffic spikes crash the app

> A web app on EC2 behind a load balancer struggles during flash sales. Orders are lost when the
> backend can't keep up. How do you make it absorb spikes without losing orders?

<details>
<summary>Thinking</summary>

- Losing orders = tight coupling between web tier and processing tier.
- Introduce a buffer so the frontend can accept orders faster than the backend processes them.
- Make the app tier elastic so it scales with load.
</details>

<details>
<summary>Answer</summary>

Put an **SQS queue** between the web tier and the processing tier. The web tier writes orders to
the queue (fast, durable); a fleet of workers in an **Auto Scaling Group** pulls and processes at
its own pace, scaling on queue depth. Add a **dead-letter queue** for failures. Now spikes fill
the queue instead of dropping orders.

**Domain:** Resilient (decoupling). See [`decision-guides/messaging.md`](../decision-guides/messaging.md).
</details>

---

## Scenario 2 - Private instances need to download OS patches

> Instances in a private subnet must fetch updates from the internet, but must never be reachable
> from the internet. What do you use?

<details>
<summary>Thinking</summary>

- Outbound internet access, no inbound. That's exactly NAT.
- IGW would make them internet-reachable - wrong.
</details>

<details>
<summary>Answer</summary>

A **NAT Gateway** in a public subnet, with the private subnet's route table sending `0.0.0.0/0`
to it. Outbound works; inbound is impossible. Deploy one NAT Gateway per AZ for resilience. If the
traffic is only to AWS services (S3, etc.), a **VPC endpoint** avoids NAT cost entirely.

**Domain:** Secure / Resilient. See [`services/networking.md`](../services/networking.md).
</details>

---

## Scenario 3 - Read-heavy database is the bottleneck

> A reporting-heavy relational app is slow. The database CPU spikes on reads; writes are fine.
> Cheapest effective fix?

<details>
<summary>Thinking</summary>

- Problem is *reads*, not availability. Multi-AZ won't help (standby isn't readable).
- Offload reads: replicas or a cache.
</details>

<details>
<summary>Answer</summary>

Add **RDS read replicas** and point reporting traffic at them. If the same queries repeat, add
**ElastiCache** (cache-aside) to serve hot reads from memory and cut DB load further. Do **not**
reach for Multi-AZ - that's availability, not read scaling.

**Domain:** High-Performing. See [`decision-guides/databases.md`](../decision-guides/databases.md).
</details>

---

## Scenario 4 - Store user uploads cheaply with unknown access patterns

> Users upload files accessed a lot at first, then rarely, unpredictably. You don't want to manage
> tiering by hand. Cheapest without hurting availability?

<details>
<summary>Thinking</summary>

- Object storage -> S3.
- Access pattern is unknown/changing and you don't want to manage it.
</details>

<details>
<summary>Answer</summary>

**S3 Intelligent-Tiering.** It auto-moves objects between frequent and infrequent tiers based on
actual access, with no retrieval fees for the move and no manual lifecycle tuning. If the pattern
were *known* (e.g. archive after 30 days), a lifecycle policy to Standard-IA/Glacier would be
cheaper.

**Domain:** Cost-Optimized. See [`decision-guides/storage.md`](../decision-guides/storage.md).
</details>

---

## Scenario 5 - Global users complain about latency

> A static-heavy web app hosted in one Region is slow for users on other continents. Improve it
> without re-architecting the backend.

<details>
<summary>Thinking</summary>

- Static content + global users + latency = edge caching.
</details>

<details>
<summary>Answer</summary>

Put **CloudFront** in front. It caches content at edge locations near users, terminates TLS, and
adds WAF/DDoS protection. For non-HTTP workloads or when you need static anycast IPs and fast
failover, you'd use **Global Accelerator** instead.

**Domain:** High-Performing. See [`decision-guides/networking.md`](../decision-guides/networking.md).
</details>

---

## Add your own

Fork this file and append scenarios as you hit tricky trade-offs in practice exams. Follow the
format: **requirement -> your reasoning -> answer with the *why not*.** Writing them is half the
learning.
