# Domain 1 - Design Secure Architectures (30%)

The biggest domain. Security shows up in almost every question, even ones that look like they
are about something else. Default mindset: least privilege, encrypt everything, never expose
what does not need exposing.

## What this domain tests

- Secure access to AWS resources (IAM, federation, roles).
- Secure workloads and applications (network isolation, secrets).
- Data protection (encryption at rest and in transit, key management).

---

## Identity and access (IAM)

- **Principle of least privilege.** Grant only what the task needs. This is the default correct answer whenever a question offers a broader vs narrower permission.
- **Users** = long-lived identities. **Groups** = collections of users for shared policies. **Roles** = temporary credentials assumed by a principal (EC2, Lambda, another account, a federated user).
- **Prefer roles over long-lived access keys.** An EC2 instance that needs S3 access should use an instance profile (role), never a hard-coded key.
- **Policy evaluation:** explicit `Deny` always wins. Default is implicit deny. An explicit `Allow` is needed to permit anything.
- **Policy types:** identity-based (attached to user/group/role), resource-based (attached to S3 bucket, SQS queue, etc.), permission boundaries, SCPs (Organizations), session policies.
- **Federation / SSO:** IAM Identity Center for workforce SSO; SAML/OIDC for external IdPs; Cognito for app end-users.

**Clue words:** "temporary credentials", "cross-account", "no hard-coded keys" -> IAM role.

---

## Network security

- **Security groups** are stateful and act at the instance/ENI level. Allow rules only.
- **NACLs** are stateless and act at the subnet level. Allow and deny rules; evaluated by rule number.
- **Private subnets** for anything that does not need direct inbound internet.
- **VPC endpoints** keep traffic to AWS services on the AWS network (no internet). Gateway endpoints for S3/DynamoDB, interface endpoints (PrivateLink) for most others.
- **WAF** protects layer-7 (SQL injection, XSS) on CloudFront, ALB, API Gateway. **Shield** protects against DDoS (Standard free, Advanced paid).

See [`decision-guides/networking.md`](../decision-guides/networking.md) and [`services/networking.md`](../services/networking.md).

---

## Data protection

- **Encryption at rest:** KMS-integrated services (S3, EBS, RDS, DynamoDB, etc.). SSE-S3, SSE-KMS, SSE-C for S3.
- **Encryption in transit:** TLS everywhere. ACM issues and auto-renews public certificates for free.
- **KMS:** customer managed keys (CMK) give you control over rotation and policy; AWS managed keys are simpler. **CloudHSM** when you need dedicated, single-tenant hardware or FIPS 140-2 Level 3.
- **Secrets Manager** vs **SSM Parameter Store:** Secrets Manager adds automatic rotation and is built for secrets; Parameter Store (SecureString) is cheaper for general config.
- **S3 protections:** Block Public Access, bucket policies, versioning, MFA delete, Object Lock (WORM).

**Clue words:** "rotate secrets automatically" -> Secrets Manager. "FIPS 140-2 Level 3 / dedicated HSM" -> CloudHSM. "control the encryption key" -> SSE-KMS with a CMK.

---

## Detection and governance

- **CloudTrail:** records API calls (the audit log of *who did what*).
- **Config:** tracks resource configuration and compliance over time.
- **GuardDuty:** threat detection from logs (ML-based).
- **Macie:** discovers and classifies sensitive data (PII) in S3.
- **Security Hub:** aggregates findings across the security services.
- **Inspector:** automated vulnerability scanning of EC2/ECR/Lambda.

**Clue words:** "who made this API call" -> CloudTrail. "find PII in S3" -> Macie. "continuous compliance of resource config" -> Config.

---

## Quick self-check

- Why prefer a role over an access key for EC2 -> S3?
- Security group vs NACL - which is stateful, which is per-subnet?
- SSE-S3 vs SSE-KMS vs SSE-C - who holds and manages the key in each?
- CloudTrail vs Config vs GuardDuty - what question does each answer?
