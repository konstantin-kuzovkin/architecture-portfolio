# Failure Scenarios

## Consumer Processing Failure

```text id="2q4eas"
Kafka
  │
  ▼
Consumer
  │
  X
Processing failure
  │
  ▼
Retry
```

Temporary failures should be retried according to a bounded retry policy.

## Permanent Business Failure

A message may be syntactically valid but impossible to process because of a business condition.

Such failures should not necessarily be retried indefinitely.

## Poison Message

A poison message is a message that repeatedly causes processing failure.

Example:

```text id="f6qvuj"
Event
 │
 ▼
Consumer
 │
 X
 │
 ▼
Retry #1
 │
 X
 │
 ▼
Retry #2
 │
 X
 │
 ▼
DLQ
```

The DLQ provides isolation from the normal processing flow.

## DLQ Recovery

DLQ should not be treated as a final garbage bin.

An operational process should allow:

1. investigation;
2. root-cause identification;
3. correction where possible;
4. controlled replay;
5. monitoring.

## Consumer Crash

If a consumer crashes before committing the offset, the event may be delivered again.

This is expected behaviour under at-least-once processing.

The consumer must therefore tolerate duplicate delivery.

## Kafka Unavailability

Temporary Kafka unavailability may cause producers or consumers to fail.

The application should implement appropriate timeout and retry policies without creating uncontrolled request amplification.

## Consumer Lag

Increasing consumer lag may indicate:

• insufficient consumer capacity;
• slow downstream dependencies;
• processing failures;
• partition imbalance;
• infrastructure problems.

Lag should therefore be monitored as an operational signal.
