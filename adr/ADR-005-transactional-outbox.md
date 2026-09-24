# ADR-005: Transactional Outbox for Event Publication

## Status

Accepted

## Context

The Transfer Service changes its database and must publish an event to Kafka (`TransferCompleted`, `TransferFailed`). These are two separate systems, so there is a dual-write problem: a crash between the two steps loses the event or publishes something that never committed. Events are used for notifications and analytics. Losing them silently is not acceptable.

## Problem

How to publish events so that a committed state change always produces an event, and no event exists for a rolled-back change?

## Constraints

- The database is PostgreSQL; the broker is Kafka.
- Consumers are (or will be) idempotent ([ADR-002](./ADR-002-at-least-once-with-idempotent-consumers.md)).
- Events of one operation must stay in order.
- Extra infrastructure should be avoided at the start.

## Alternatives

1. **Publish directly after commit.** Simple, but a crash after commit loses the event.
2. **Publish before commit.** Can publish an event for a change that later rolls back.
3. **Kafka transactions.** They make Kafka-to-Kafka processing atomic. They cannot include a database update in the same atomic step, so the dual write remains.
4. **Transactional outbox with a polling relay (chosen first).** Event is written in the same DB transaction; a relay publishes it.
5. **Transactional outbox with log-based CDC (for example Debezium).** Same outbox table, but changes are read from the database log.

## Evaluation

| Criterion | 1 | 2 | 3 | 4 | 5 |
|---|---|---|---|---|---|
| No lost events after a crash | No | Yes | No | Yes | Yes |
| No phantom events | Yes | No | Yes | Yes | Yes |
| Extra infrastructure | None | None | None | None | CDC platform |
| Publication latency | Lowest | Lowest | Low | Polling interval | Low |
| Load on the database | None | None | None | Some | Low |
| Operational complexity | Low | Low | Medium | Low | Higher |

## Decision

Use the transactional outbox. Start with a polling relay (option 4). Move to CDC (option 5) if the outbox lag or the database load exceeds the limits in the NFR document. The outbox table and consumer deduplication are described in [outbox.md](../02-event-driven-architecture/outbox.md).

The Kafka key is `operationId`. Delivery is at-least-once; consumers deduplicate by `eventId`.

## Consequences

Positive:
- A committed state change always produces an event.
- No new infrastructure at the start.
- The same table can later be read by CDC without changing the service.

Negative:
- Duplicates are possible, so every consumer needs idempotency.
- The relay adds latency (up to the polling interval) and load on the database.
- The outbox table needs cleanup and monitoring (lag alert at p95 > 5 s).

## Rejected alternatives

- Options 1 and 2 were rejected because they can lose or invent events.
- Option 3 was rejected because it does not solve the database plus Kafka problem.

## Related

[Case 02 — Event-Driven Architecture](../02-event-driven-architecture/README.md), [ADR-002](./ADR-002-at-least-once-with-idempotent-consumers.md)
