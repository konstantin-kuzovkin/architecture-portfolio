Event-Driven Architecture with Kafka

Overview

This case study demonstrates the design of an event-driven integration architecture using Apache Kafka as the messaging platform.

The architecture focuses on reliable asynchronous communication between independently deployed services.

The case addresses message delivery semantics, consumer behaviour, duplicate processing, ordering, retries, dead-letter handling, schema evolution and observability.

> **Portfolio note:** This is a sanitized and reconstructed architecture case. It does not contain confidential information, production topic names, customer data or proprietary implementation details.

────────

Business Problem

A distributed platform contains multiple services that need to react to changes in business state.

Direct synchronous communication between all participants would create tight coupling and increase dependency on the availability and response time of downstream services.

An event-driven approach allows services to communicate asynchronously while remaining independently deployable.

────────

Architectural Goals

The solution should provide:

• asynchronous communication;
• loose coupling between producers and consumers;
• reliable event delivery;
• controlled duplicate processing;
• predictable ordering where required;
• independent consumer scaling;
• retry and failure handling;
• dead-letter processing;
• schema evolution;
• operational visibility.

────────

High-Level Architecture

```text id="35n3yq"
                    ┌──────────────────┐
                    │   Service A      │
                    │    Producer      │
                    └────────┬─────────┘
                             │
                             │ Event
                             ▼
                    ┌──────────────────┐
                    │      Kafka       │
                    │                  │
                    │     Topic        │
                    └───────┬──────────┘
                            │
              ┌─────────────┼─────────────┐
              ▼             ▼             ▼
       ┌────────────┐ ┌────────────┐ ┌────────────┐
       │ Consumer A │ │ Consumer B │ │ Consumer C │
       └────────────┘ └────────────┘ └────────────┘
```

────────

Why Events?

Events are used when the producer does not need to synchronously control the consumer’s processing.

For example:

```text id="y5td2s"
OrderCreated
PaymentCompleted
CustomerUpdated
TransferCompleted
DocumentSigned
```

The producer publishes the fact that something happened.

Consumers independently decide whether and how they should react.

────────

Kafka Responsibilities

Kafka provides the transport and persistence mechanism for events.

The architecture relies on Kafka for:

• durable event storage;
• partitioned event streams;
• consumer groups;
• offset management;
• scalable consumption;
• replay capability where appropriate.

Kafka does not provide the business semantics of the event itself.

────────

Event Contract

An event should contain a stable business contract.

Example:

```json
{
  "eventId": "unique-event-id",
  "eventType": "TransferCompleted",
  "eventVersion": 1,
  "occurredAt": "2026-01-01T12:00:00Z",
  "operationId": "operation-id",
  "correlationId": "correlation-id",
  "payload": {}
}
```

The exact payload is intentionally simplified for portfolio purposes.

────────

Delivery Semantics

The architecture assumes that consumers must be prepared for duplicate delivery.

Therefore, consumers should be designed to process events idempotently.

Conceptually:

```text id="k9qv0p"
Event
  │
  ▼
Consumer
  │
  ├── first delivery ──► process
  │
  └── duplicate ───────► ignore / return existing result
```

────────

Ordering

Kafka guarantees ordering within a partition.

Therefore, when business ordering matters, related events should use a consistent partitioning key.

Example:

```text id="0x7z8g"
operation_id
     │
     ▼
partition key
     │
     ▼
same Kafka partition
     │
     ▼
preserved order
```

Ordering across independent partitions is not assumed.

────────

Consumer Groups

Each logical consuming application uses its own consumer group.

For example:

```text id="vqip5b"
Topic: transfer-events

Group: audit-service
  └── Consumer instances

Group: notification-service
  └── Consumer instances

Group: analytics-service
  └── Consumer instances
```

Each consumer group receives the event stream independently.

────────

Retry

Temporary failures should not immediately result in message loss.

A retry strategy may use:

• retry attempts;
• backoff;
• delayed retry topics;
• controlled redelivery.

Retries must be bounded.

────────

Dead Letter Queue

Events that cannot be successfully processed after the configured retry policy may be redirected to a dead-letter flow.

```text id="48wv5m"
Kafka
 │
 ▼
Consumer
 │
 ├── success ───────► commit
 │
 └── failure
       │
       ▼
    retry
       │
       ├── success ──► commit
       │
       └── exhausted
               │
               ▼
              DLQ
```

The DLQ must be monitored and have an operational recovery process.

────────

Schema Evolution

Event contracts evolve over time.

The architecture therefore requires compatibility rules for producers and consumers.

A producer should not introduce breaking changes without considering existing consumers.

Possible strategies include:

• backward-compatible changes;
• additive fields;
• explicit event versions;
• migration periods for consumers.

────────

Observability

Each event should be traceable using identifiers such as:

• event ID;
• operation ID;
• correlation ID;
• trace ID;
• event type;
• event version.

Important metrics include:

• consumer lag;
• processing latency;
• processing failures;
• retry count;
• DLQ volume;
• duplicate event rate.

────────

Key Architectural Principles

1. Kafka is treated as an event transport platform, not as business logic.
2. Events represent facts that have occurred.
3. Consumers are independently responsible for processing events.
4. Consumers must tolerate duplicate delivery.
5. Ordering is guaranteed only within a partition.
6. Partition keys must reflect business ordering requirements.
7. Retries must be bounded.
8. Failed messages require controlled dead-letter handling.
9. Event contracts must support evolution.
10. Operational visibility is part of the architecture.

────────

What I Personally Contributed

The case reflects my experience and approach to designing Kafka-based integration solutions.

Key areas include:

• analysis of synchronous versus asynchronous interaction;
• definition of event contracts;
• Kafka topic and consumer interaction modelling;
• analysis of delivery semantics;
• idempotent consumer design;
• failure and retry scenarios;
• DLQ strategy;
• event ordering;
• schema evolution;
• observability requirements;
• technical documentation and integration standards.
