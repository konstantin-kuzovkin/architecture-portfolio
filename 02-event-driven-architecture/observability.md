# Event-Driven Observability

## Required Correlation

An event should be traceable through the system using:

• event ID;
• operation ID;
• correlation ID;
• trace ID.

## Consumer Metrics

Recommended metrics:

• consumer lag;
• processing latency;
• successful processing count;
• processing failure count;
• retry count;
• DLQ count;
• duplicate event count.

## Producer Metrics

Recommended metrics:

• published events;
• publish failures;
• publish latency;
• retry count.

## Alerts

Potential alerts:

```text id="h7ipgq"
Consumer lag above threshold

DLQ volume increasing

Repeated processing failures

Publish failure rate increasing

Unexpected duplicate rate

Consumer group unavailable
```

## Operational Question

The monitoring system should make it possible to answer:

> Is the problem in Kafka, the consumer, the producer or a downstream dependency?

This distinction significantly reduces troubleshooting time.

## Distributed Trace

Example:

```text id="5xj58s"
Service A
   │
   ▼
Kafka
   │
   ▼
Consumer
   │
   ▼
Service B
   │
   ▼
Database
```

The same correlation context should be propagated where technically appropriate.
