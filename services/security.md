# Security

Security is Domain 1 (30%) and threads through everything else. See
[`domains/secure-architectures.md`](../domains/secure-architectures.md) for the domain-level view;
this file is the service reference.

## IAM (Identity and Access Management)

- **Users** (long-lived), **groups** (share policies), **roles** (temporary, assumed).
- **Policies** are JSON: Effect, Action, Resource, Condition. Identity-based vs resource-based.
- **Least privilege** by default. **Explicit Deny** always overrides Allow.
- **Roles for services:** EC2 instance profile, Lambda execution role, cross-account roles - so nothing hard-codes keys.
- **IAM Identity Center** (successor to AWS SSO) for workforce SSO across accounts.
- **MFA** for privileged users; never use the root account for daily work.

---

## Key and secret management

- **KMS** - managed encryption keys; integrated with most services. Customer managed keys (CMK) give you rotation and policy control; AWS managed keys are simpler.
- **CloudHSM** - dedicated, single-tenant hardware security module; FIPS 140-2 Level 3.
- **Secrets Manager** - store secrets with **automatic rotation** (DB credentials, API keys).
- **SSM Parameter Store** - config + secrets (SecureString); cheaper, no built-in rotation.
- **ACM** - free public TLS certificates with auto-renewal for ELB, CloudFront, API Gateway.

**Clue words:** "rotate DB credentials automatically" -> Secrets Manager. "dedicated HSM / FIPS Level 3" -> CloudHSM. "control the KMS key policy and rotation" -> customer managed key.

---

## Network protection

- **Security groups** (stateful, instance) and **NACLs** (stateless, subnet).
- **WAF** - layer-7 protection (SQLi, XSS, rate limiting) on CloudFront/ALB/API Gateway.
- **Shield** - DDoS protection: Standard (free, automatic), Advanced (paid, 24/7 support + cost protection).
- **Network Firewall** - managed stateful firewall for VPC traffic.

---

## Detection, audit, and compliance

| Service | Answers the question |
|---------|----------------------|
| CloudTrail | Who made which API call, and when? |
| Config | Is my resource configuration compliant over time? |
| GuardDuty | Is there malicious/anomalous activity? (threat detection) |
| Macie | Is there sensitive data (PII) in my S3? |
| Inspector | Do my EC2/ECR/Lambda have known vulnerabilities? |
| Security Hub | One place to aggregate all security findings |
| Detective | Investigate/root-cause a finding |

---

## Data protection quick rules

- Encrypt at rest (KMS-integrated) and in transit (TLS) by default.
- S3: Block Public Access on, bucket policies, versioning, Object Lock for WORM.
- Prefer resource policies + least-privilege IAM over making things public.

---

## Quick self-check

- Explicit Deny vs Allow - which wins?
- Secrets Manager vs Parameter Store - what's the deciding feature? (auto rotation)
- KMS vs CloudHSM - when do you need single-tenant hardware?
- CloudTrail vs Config vs GuardDuty - one sentence each on what they answer.
