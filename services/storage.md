# Storage

Three shapes of storage: object (S3), block (EBS/instance store), file (EFS/FSx). Knowing which
shape a scenario needs is half the battle. Full comparison in
[`decision-guides/storage.md`](../decision-guides/storage.md).

## S3 (Simple Storage Service) - object storage

The most-tested storage service. Objects in buckets, accessed over HTTP(S) APIs.

- **Durability:** 11 nines (99.999999999%). Objects replicated across AZs within a Region.
- **Unlimited** storage; single object up to 5 TB.
- **Storage classes** (by access pattern and cost):
  - Standard - frequent access.
  - Intelligent-Tiering - unknown/changing access; auto-tiers. The "not sure" answer.
  - Standard-IA / One Zone-IA - infrequent (One Zone = one AZ, cheaper, less resilient - data lost if that AZ is destroyed).
  - Glacier Instant Retrieval / Flexible Retrieval / Deep Archive - archival, cheapest, retrieval latency grows.
- **Lifecycle policies:** auto-transition between classes and expire objects.
- **Versioning:** keep old versions; protects against overwrite/delete. Pair with MFA Delete.
- **Security:** Block Public Access (on by default), bucket policies, ACLs (legacy), encryption (SSE-S3 / SSE-KMS / SSE-C).
- **Replication:** Cross-Region (CRR) and Same-Region (SRR).
- **Static website hosting**, presigned URLs (temporary access), Object Lock (WORM/compliance).
- **Performance:** scales to very high request rates; use multipart upload for large objects; S3 Transfer Acceleration for long-distance uploads.

**Use when:** static assets, backups, data lakes, logs, anything object-shaped and internet-accessible.

---

## EBS (Elastic Block Store) - block storage

Network-attached disks for EC2. Persist independently of the instance lifecycle.

- **Tied to one AZ**; attach to instances in the same AZ. Snapshots (stored in S3) enable copy across AZ/Region.
- **Volume types:**
  - gp3 / gp2 - general purpose SSD (gp3 is the modern default; provision IOPS/throughput independently).
  - io1 / io2 - provisioned IOPS SSD for latency-sensitive, high-IOPS databases.
  - st1 - throughput-optimized HDD (big sequential workloads).
  - sc1 - cold HDD (infrequent, cheapest).
- Encryption via KMS; snapshots inherit encryption.
- One volume attaches to one instance (except io1/io2 Multi-Attach in a cluster).

**Use when:** a single instance needs a persistent, low-latency disk (boot volume, database storage).

---

## Instance store

Ephemeral block storage physically attached to the host. Very fast, but **data is lost on
stop/terminate**. Use for caches, buffers, scratch - never durable data.

---

## EFS (Elastic File System) - shared file storage

Managed NFS. Mount from **many** instances across AZs at once; grows/shrinks automatically.

- Linux-focused (NFS). Multi-AZ by design.
- Storage classes: Standard and IA, with lifecycle management.
- **Use when:** shared file access across many instances (web content, shared home dirs, CMS).

---

## FSx - specialized file systems

- **FSx for Windows File Server** - SMB, Active Directory, Windows workloads.
- **FSx for Lustre** - high-performance computing, ML, big data; integrates with S3.
- Also NetApp ONTAP and OpenZFS variants.

---

## Storage Gateway / DataSync / Snow

- **Storage Gateway** - hybrid: on-prem apps use AWS storage (File/Volume/Tape gateways).
- **DataSync** - fast, automated data transfer on-prem <-> AWS.
- **Snowball / Snowmobile** - physical devices to move huge datasets when the network is too slow.

---

## Quick self-check

- Object vs block vs file - which service for each shape?
- Why can't an EBS volume attach to an instance in another AZ directly?
- One writer vs many concurrent readers/writers - EBS or EFS?
- Which S3 class for "unknown access pattern"? For "archive, retrieval in hours is fine"?
