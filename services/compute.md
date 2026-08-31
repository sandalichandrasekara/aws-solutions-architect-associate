# Compute

The engines that run your code. For the exam: know when to pick servers (EC2), containers
(ECS/EKS/Fargate), or functions (Lambda), and how each scales and gets priced.

## EC2 (Elastic Compute Cloud)

Virtual servers. The most flexible and most configurable compute.

- **Instance families:** general purpose (M, T), compute optimized (C), memory optimized (R, X), storage optimized (I, D), accelerated (P, G - GPU). Match family to workload.
- **Purchasing options:** On-Demand, Reserved/Savings Plans, Spot, Dedicated Hosts. See [`domains/cost-optimized.md`](../domains/cost-optimized.md).
- **AMI** = the image an instance boots from. **User data** = boot-time script for bootstrapping.
- **Instance profile** attaches an IAM role so the instance gets temporary credentials - never store keys on the box.
- **Storage:** EBS (durable, network-attached) vs instance store (ephemeral, physical, fast).
- **Auto Scaling Groups** keep desired capacity across AZs and scale on demand.

**Use when:** you need full OS control, specific software, or long-running/legacy workloads.

---

## Lambda

Run code without managing servers. Event-driven, pay per request + duration.

- Triggered by events (S3, API Gateway, DynamoDB Streams, EventBridge, SQS, etc.).
- Scales automatically and concurrently; you pay only while it runs.
- Limits to remember: max **15-minute** timeout; memory configurable (CPU scales with memory); `/tmp` scratch space; deployment package size limits.
- Good for glue code, APIs, stream/file processing, cron (via EventBridge).

**Use when:** short-lived, event-driven, bursty, or you want zero server management.
**Avoid when:** long-running (>15 min), needs persistent local state, or steady heavy load where reserved compute is cheaper.

---

## Containers - ECS, EKS, Fargate

- **ECS** - AWS-native container orchestration. Simpler if you're all-in on AWS.
- **EKS** - managed Kubernetes. Choose for Kubernetes portability/ecosystem.
- **Fargate** - serverless compute *for* containers (works with ECS and EKS). No EC2 to manage; pay per task. Choose when you don't want to manage the underlying nodes.
- **EC2 launch type** - you manage the container host instances; more control, more ops.

**Rule of thumb:** ECS+Fargate for "containers without server management"; EKS when the requirement literally says Kubernetes.

---

## Elastic Beanstalk

PaaS that provisions and manages the underlying resources (EC2, ASG, ELB) from your app code.
Good for developers who want to deploy without hand-building infrastructure. You keep control
of the resources underneath.

---

## Edge / other

- **Lightsail** - simple VPS with predictable pricing, for small/simple apps.
- **Batch** - managed batch computing at scale.
- **Outposts / Wavelength / Local Zones** - run AWS compute on-prem or closer to users.

---

## Decision snapshot

| Need | Reach for |
|------|-----------|
| Full OS control, legacy app | EC2 |
| Event-driven, short tasks, no servers | Lambda |
| Containers, no node management | ECS + Fargate |
| Kubernetes specifically | EKS |
| Deploy app without building infra | Elastic Beanstalk |
| Fault-tolerant batch, cheapest | EC2 Spot / Batch |
