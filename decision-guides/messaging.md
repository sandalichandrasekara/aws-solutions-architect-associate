# Decision Guide: Messaging & Decoupling

Decoupling is a core exam theme: put something between producers and consumers so failures and
spikes don't cascade. Know the four options and their shapes.

## The four at a glance

| Service | Pattern | Consumers | Ordering | Best for |
|---------|---------|-----------|----------|----------|
| SQS | Queue (pull) | One consumer per message | FIFO option | Decouple, buffer, absorb spikes |
| SNS | Pub/sub (push) | Many subscribers (fan-out) | FIFO option | Notify many endpoints at once |
| EventBridge | Event bus (push) | Many, rule-routed | - | Event-driven, SaaS/AWS integration |
| Kinesis | Streaming | Many, replayable | Per-shard | Real-time streams, analytics, replay |

---

## SQS - Simple Queue Service

- A queue that **buffers** messages; consumers **pull** and process, then delete.
- Absorbs traffic spikes; decouples producer speed from consumer speed.
- **Standard** (at-least-once, best-effort order, high throughput) vs **FIFO** (exactly-once, strict order, lower throughput).
- **Visibility timeout**, **dead-letter queues** for poison messages, **long polling** to cut cost.

**Use when:** decouple components, smooth out load, guarantee each message processed by one worker.

---

## SNS - Simple Notification Service

- **Pub/sub**: publish once, **push** to many subscribers (SQS, Lambda, HTTP, email, SMS).
- Core of the **fan-out pattern**: SNS -> multiple SQS queues, each feeding a different pipeline.

**Use when:** one event must notify/trigger many independent consumers.

---

## EventBridge

- Serverless **event bus** with content-based **routing rules** and schemas.
- Integrates AWS service events and third-party SaaS; can schedule events (cron).
- Richer routing/filtering than SNS; the modern choice for event-driven architectures.

**Use when:** route different event types to different targets based on content; SaaS/AWS event integration.

---

## Kinesis

- **Streaming** data platform for real-time ingestion and processing.
  - **Data Streams** - custom real-time processing; data **replayable** within retention.
  - **Data Firehose** - load streams into S3/Redshift/OpenSearch with minimal code.
  - **Data Analytics** - SQL/Flink over streams.
- Ordered per shard; multiple consumers can read the same stream.

**Use when:** high-volume real-time streams, analytics, or you need replay/multiple readers.

---

## SQS vs Kinesis (common confusion)

| | SQS | Kinesis |
|--|-----|---------|
| Model | Queue | Stream |
| Message after read | Deleted | Retained (replayable) |
| Consumers | One per message | Many read same data |
| Ordering | FIFO queues only | Per shard |
| Use | Decouple/work queue | Real-time analytics, replay, multiple pipelines |

---

## Decision flow

```
One producer must notify many consumers?      -> SNS (or EventBridge for routing)
Route events by content / SaaS integration?   -> EventBridge
Buffer work for a pool of workers?            -> SQS
Real-time stream, replay, multiple readers?   -> Kinesis
Fan-out to many independent pipelines?        -> SNS -> multiple SQS queues
```
