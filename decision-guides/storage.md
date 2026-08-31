# Decision Guide: Storage

The question is almost always "which storage shape?" Answer the shape first, then the tier.

## Step 1 - Which shape?

| Shape | Service | Access | Use when |
|-------|---------|--------|----------|
| Object | S3 | HTTP API, internet | Files, backups, static assets, data lakes, logs |
| Block | EBS | Attached to one EC2, one AZ | A single instance needs a persistent disk |
| Block (ephemeral) | Instance store | Physical, temporary | Scratch/cache, lose-on-stop is OK |
| File | EFS | NFS, many instances, multi-AZ | Shared Linux file access across instances |
| File (Windows/HPC) | FSx | SMB / Lustre | Windows AD workloads or high-performance computing |

**Fast test:**
- Accessed over the internet as objects? -> **S3**
- One instance, one disk, database-like? -> **EBS**
- Many instances need the *same* files at once? -> **EFS**
- Windows/SMB or HPC/Lustre? -> **FSx**

---

## S3 vs EBS vs EFS (the classic)

| | S3 | EBS | EFS |
|--|----|----|-----|
| Type | Object | Block | File (NFS) |
| Attach | API from anywhere | 1 instance (Multi-Attach for io1/io2) | Many instances |
| Scope | Region (multi-AZ) | Single AZ | Multi-AZ |
| Scales | Unlimited | Provisioned per volume | Automatic |
| Typical use | Static assets, backups | Boot/DB volume | Shared content, home dirs |
| Cost model | Per GB + requests + transfer | Per GB provisioned | Per GB used |

**Trap:** "shared across multiple instances" rules out EBS (single-attach) -> think EFS.
**Trap:** "persistent low-latency disk for a database on one instance" -> EBS (gp3/io2), not EFS.

---

## S3 storage class selection

| Access pattern | Class |
|----------------|-------|
| Frequent | S3 Standard |
| Unknown / changing | Intelligent-Tiering |
| Infrequent, needs resilience | Standard-IA |
| Infrequent, can accept one AZ | One Zone-IA |
| Archive, retrieve in minutes | Glacier Instant / Flexible |
| Archive, retrieve in hours, cheapest | Glacier Deep Archive |

Use **lifecycle policies** to move data down these tiers automatically over time.

---

## EBS volume type selection

| Need | Type |
|------|------|
| General purpose default | gp3 |
| High IOPS, latency-sensitive DB | io2 (io1) |
| Big sequential throughput (logs, big data) | st1 |
| Cold, infrequent, cheapest | sc1 |

---

## Decision flow

```
Object over HTTP?              -> S3 (pick class by access pattern)
Single instance, block disk?   -> EBS (gp3 default; io2 for high IOPS)
Temporary/scratch, ok to lose? -> Instance store
Shared files, many instances?  -> EFS (Linux) or FSx (Windows/HPC)
```
