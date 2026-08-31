# Databases

Match the data model to the service: relational (RDS/Aurora), key-value/document (DynamoDB),
in-memory (ElastiCache), plus specialized stores. Full comparison in
[`decision-guides/databases.md`](../decision-guides/databases.md).

## RDS (Relational Database Service)

Managed relational databases: MySQL, PostgreSQL, MariaDB, Oracle, SQL Server.

- AWS handles patching, backups, and failover; you keep the SQL engine and schema.
- **Multi-AZ:** synchronous standby in another AZ + automatic failover. For **availability**, not scaling. Standby is not readable.
- **Read replicas:** asynchronous copies for **read scaling**; can span Regions; can be promoted to standalone. Different job than Multi-AZ.
- **Backups:** automated (point-in-time restore) + manual snapshots.
- **Encryption** at rest via KMS; in transit via TLS.

**Use when:** you need relational/ACID with a familiar engine and managed operations.

---

## Aurora

AWS's cloud-native relational engine (MySQL- and PostgreSQL-compatible).

- Storage auto-scales; data kept as **6 copies across 3 AZs**, self-healing.
- Up to **15 read replicas**, fast failover.
- **Aurora Serverless** auto-scales capacity for intermittent/variable workloads.
- **Global Database** for cross-Region low-latency reads and DR.

**Use when:** you want RDS compatibility with more performance, scale, and resilience.

---

## DynamoDB

Fully managed serverless NoSQL key-value / document store.

- Single-digit millisecond latency at any scale; multi-AZ by default.
- **Capacity modes:** on-demand (spiky/unknown traffic) vs provisioned + auto scaling (predictable, cheaper at steady load).
- **Global Tables** - multi-Region, active-active replication.
- **DAX** - in-memory cache for microsecond reads.
- **Streams** - change data capture to trigger Lambda, etc.
- **TTL** - auto-expire items.

**Use when:** massive scale, key-value access patterns, serverless, unpredictable traffic.
**Avoid when:** you need complex joins/ad-hoc relational queries.

---

## ElastiCache

Managed in-memory cache - **Redis** or **Memcached**.

- Offloads read-heavy databases and stores sessions; sub-millisecond latency.
- **Redis** - richer features: persistence, replication, HA, pub/sub, sorted sets.
- **Memcached** - simple, multi-threaded, horizontal scale, no persistence.
- Common pattern: **cache-aside** (lazy loading) or write-through.

**Use when:** reduce DB load, speed up reads, store ephemeral session/leaderboard data.

---

## Specialized databases

| Service | Model / use |
|---------|-------------|
| Redshift | Data warehouse / OLAP analytics on large datasets |
| Neptune | Graph database (relationships, social, fraud) |
| DocumentDB | MongoDB-compatible document DB |
| Keyspaces | Managed Apache Cassandra |
| Timestream | Time-series data (IoT, metrics) |
| QLDB | Immutable, cryptographically verifiable ledger |

**Clue words:** "data warehouse / analytics / OLAP" -> Redshift. "highly connected / graph" -> Neptune. "time-series / IoT metrics" -> Timestream. "immutable ledger" -> QLDB.

---

## Quick self-check

- Multi-AZ vs read replica - availability vs read scaling; which standby is readable?
- DynamoDB on-demand vs provisioned - which for spiky vs steady traffic?
- Redis vs Memcached - which when you need persistence and HA?
- OLTP vs OLAP - which points you to Redshift?
