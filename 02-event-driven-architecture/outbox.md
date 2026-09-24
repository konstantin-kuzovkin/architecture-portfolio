# Transactional Outbox

## Problem: dual write

A service must change its database and publish an event. These are two systems. If the service commits the database and then crashes before publishing, the event is lost. If it publishes first and the commit fails, the event describes something that never happened. No retry logic fixes this without a design.

## Solution

The service writes the event to an `outbox_event` table **in the same database transaction** as the state change. A separate relay reads unpublished rows and publishes them to Kafka. Publication is at-least-once, so consumers must be idempotent.

```text
BEGIN
  UPDATE transfer_operation SET state = 'COMPLETED' ...
  INSERT INTO outbox_event (...)
COMMIT
        |
        v
Outbox relay --> Kafka topic transfer.events.v1 (key = operationId)
        |
        v
Consumer: INSERT processed_event ... + business effect, in one transaction
```

## Tables (PostgreSQL, simplified)

```sql
CREATE TABLE outbox_event (
  event_id      uuid PRIMARY KEY,
  aggregate_id  uuid        NOT NULL,        -- operationId, used as the Kafka key
  event_type    text        NOT NULL,
  event_version integer     NOT NULL,
  payload       jsonb       NOT NULL,
  created_at    timestamptz NOT NULL DEFAULT now(),
  published_at  timestamptz
);
CREATE INDEX outbox_unpublished ON outbox_event (created_at) WHERE published_at IS NULL;

CREATE TABLE processed_event (
  consumer      text        NOT NULL,
  event_id      uuid        NOT NULL,
  processed_at  timestamptz NOT NULL DEFAULT now(),
  PRIMARY KEY (consumer, event_id)
);
```

## Relay options

| Option | How it works | Advantage | Disadvantage |
|---|---|---|---|
| Polling relay (chosen first) | A job selects unpublished rows in `created_at` order, publishes, marks `published_at` | Simple, no extra infrastructure | Extra load on the DB; latency of the polling interval |
| Log-based CDC (for example Debezium) | Reads the database log and publishes changes | Low latency, low DB load | More infrastructure and operations |

Start with polling. Move to CDC when the outbox lag or the DB load becomes a problem.

## Ordering

The relay publishes rows of one `aggregate_id` in `created_at` order and uses `aggregate_id` as the Kafka key. Events of one operation stay in order inside one partition.

## Idempotent consumer

```text
BEGIN
  INSERT INTO processed_event (consumer, event_id) VALUES (:c, :id) ON CONFLICT DO NOTHING
  if 0 rows inserted: this is a duplicate -> COMMIT and skip the business effect
  else: apply the business effect
COMMIT
```

The deduplication record and the business effect are in the same transaction, so a crash cannot separate them.

## Operations

| Item | Reference value |
|---|---|
| Outbox lag alert | p95 > 5 s |
| Cleanup | Delete published rows older than 7 days |
| Deduplication table cleanup | Keep at least as long as the topic retention (7 days) |
| Dead-letter topic | Any message triggers an alert |

## What this does not solve

- It does not give exactly-once delivery. It gives at-least-once delivery and idempotent processing.
- It does not remove the need for schema compatibility rules (see schema-evolution.md).
