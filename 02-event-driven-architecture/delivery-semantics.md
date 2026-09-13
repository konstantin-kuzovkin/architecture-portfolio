Message Delivery Semantics

At-Most-Once

The message may be delivered zero or one time.

Potential advantage:

• lower processing complexity.

Potential risk:

• message loss may be possible.

This model is appropriate only when occasional loss is acceptable.

────────

At-Least-Once

The message is delivered one or more times.

Potential advantage:

• stronger protection against message loss.

Potential consequence:

• duplicate processing is possible.

Therefore:

> **At-least-once delivery requires idempotent consumers.**

────────

Exactly-Once

Exactly-once semantics can refer to different guarantees at different boundaries.

It should not automatically be interpreted as:

> “the entire distributed business operation happens exactly once.”

Application-level business effects may still require explicit idempotency and transactional design.

────────

Recommended Approach

For a general event-driven business integration:

```text id="2ahb8e"
At-least-once delivery
        +
Idempotent consumer
        +
Durable processing state
```

provides a practical reliability model.

────────

Duplicate Processing

Example:

```text id="8x9s7k"
Event #123
    │
    ▼
Consumer
    │
    ▼
Business operation
    │
    X
offset commit failed

Event #123
    │
    ▼
Consumer again
```

The consumer must detect that the business event has already been processed.

────────

Idempotency Record

Conceptually:

```text id="w5c1u4"
event_id
    │
    ▼
Processed Events
    │
 ┌──┴──┐
 │     │
new   exists
 │     │
 ▼     ▼
process skip
```

The exact implementation may use a database, transactional storage or another durable mechanism appropriate to the architecture.

────────

Important Distinction

Kafka offset management and business idempotency are related but not identical.

A committed Kafka offset tells Kafka that a consumer has progressed.

It does not by itself guarantee that a business side effect cannot be duplicated.

Therefore, business-level idempotency must be explicitly designed.
