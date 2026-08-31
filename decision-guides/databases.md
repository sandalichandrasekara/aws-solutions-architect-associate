# Decision Guide: Databases

Match the data model and access pattern to the engine. Get the model right first, then worry
about scaling and cost.

## Step 1 - Which model?

| Model | Service | Use when |
|-------|---------|----------|
| Relational (OLTP) | RDS / Aurora | Structured data, joins, transactions, SQL |
| Key-value / document (NoSQL) | DynamoDB | Massive scale, simple access patterns, serverless |
| In-memory cache | ElastiCache / DAX | Speed up reads, sessions, leaderboards |
| Data warehouse (OLAP) | Redshift | Analytics/reporting on large datasets |
| Graph | Neptune | Highly connected data, relationships |
| Time-series | Timestream | IoT/metrics over time |
| Ledger | QLDB | Immutable, verifiable history |

---

## RDS vs Aurora vs DynamoDB

| | RDS | Aurora | DynamoDB |
|--|-----|--------|----------|
| Model | Relational | Relational (MySQL/PG compatible) | Key-value / document |
| Scaling | Read replicas | Up to 15 replicas, auto storage | Virtually unlimited |
| Resilience | Multi-AZ standby | 6 copies / 3 AZs | Multi-AZ by default |
| Serverless option | No | Aurora Serverless | Yes (on-demand) |
| Best for | Standard managed SQL | High-scale/perf SQL | Internet-scale NoSQL |

**Pick RDS** for a familiar engine (Oracle/SQL Server/MySQL/PG) with managed ops.
**Pick Aurora** when you want RDS compatibility but more performance/scale/resilience.
**Pick DynamoDB** for serverless, huge scale, simple key-based access, spiky traffic.

---

## Multi-AZ vs Read Replica (the classic trap)

| | Multi-AZ | Read Replica |
|--|----------|--------------|
| Purpose | Availability / failover | Read scaling |
| Replication | Synchronous | Asynchronous |
| Standby readable? | No | Yes |
| Failover | Automatic | Manual promote |
| Cross-Region? | (Aurora Global) | Yes |

**"Improve availability / survive AZ failure"** -> Multi-AZ.
**"Offload read traffic / scale reads"** -> read replicas.
You can use both together.

---

## DynamoDB capacity mode

- **On-demand** - spiky, unknown, or new workloads. No capacity planning. Pay per request.
- **Provisioned (+ auto scaling)** - predictable, steady traffic; cheaper at scale.
- Add **DAX** for microsecond cached reads; **Global Tables** for multi-Region active-active.

---

## Caching: ElastiCache vs DAX

- **ElastiCache (Redis/Memcached)** - general cache in front of any DB, plus sessions/queues.
  - Redis: persistence, HA, replication, pub/sub.
  - Memcached: simple, multi-threaded, no persistence.
- **DAX** - purpose-built cache **for DynamoDB only**, microsecond reads, drop-in.

---

## Decision flow

```
Need SQL / joins / transactions?
  -> Familiar engine, managed?         -> RDS
  -> Want max performance/scale/HA?    -> Aurora
Need internet-scale key-value NoSQL?   -> DynamoDB
Analytics / warehouse on big data?     -> Redshift
Just need to speed up reads?           -> ElastiCache (any DB) / DAX (DynamoDB)
Graph / time-series / ledger?          -> Neptune / Timestream / QLDB
```
