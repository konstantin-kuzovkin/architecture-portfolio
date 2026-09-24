# ADR-002: At-Least-Once Delivery with Idempotent Consumers

## Status

Accepted

## Context

The event-driven architecture uses Kafka for asynchronous communication between services.

Events may be delivered more than once due to retries, consumer restarts, network failures or processing failures.

The business operation must remain correct even when the same event is processed multiple times.

## Problem

The architecture must define the delivery and processing model for events while preventing duplicate delivery from causing duplicate business effects.

## Constraints

- Kafka is used as the event transport.
- Network and infrastructure failures are expected.
- Consumer restarts are possible.
- Events may be redelivered.
- Business operations may not be safely repeatable by default.

## Alternatives

### Alternative 1 — At-most-once delivery

Process each event at most once and accept possible event loss.

### Alternative 2 — Exactly-once processing as the primary architectural assumption

Rely on end-to-end exactly-once semantics for the complete business operation.

### Alternative 3 — At-least-once delivery with idempotent consumers

Allow event redelivery while ensuring repeated processing does not create an additional business effect.

## Evaluation Criteria

- Reliability
- Data loss risk
- Duplicate processing risk
- Operational complexity
- Failure recovery
- Scalability
- Compatibility with distributed business operations

## Decision

Use at-least-once event delivery combined with idempotent consumer processing.

Each consumer must identify duplicate events using an appropriate idempotency key or event identifier.

The consumer must ensure that processing the same event more than once does not create additional business effects.

Retry and Dead Letter Queue mechanisms are used for processing failures.

## Consequences

### Positive

- Reduced risk of silently losing events.
- Explicit handling of duplicate delivery.
- Clear recovery model.
- Consumers can tolerate retries and restarts.
- Failure handling remains observable and operationally manageable.

### Negative

- Consumers require idempotency logic.
- Additional storage or state may be required to track processed events.
- Duplicate delivery must be considered in testing.
- Business operations must be designed with repeated processing in mind.

## Rejected Alternatives

At-most-once delivery was rejected because event loss may lead to missing business actions.

Exactly-once was not selected as the primary business correctness assumption because transport-level processing guarantees do not automatically guarantee exactly-once business effects across independent systems.

## Related Case

[02 — Event-Driven Architecture](../02-event-driven-architecture/README.md)

## Producer side

Publication of events uses the transactional outbox. See [ADR-005](./ADR-005-transactional-outbox.md). The Kafka key is `operationId`, so events of one operation stay in order inside a partition. Consumers deduplicate by `eventId` in the same transaction as the business effect.
