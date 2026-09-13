ADR-002: Idempotent Consumer

Status

Accepted

Context

Kafka-based consumers may receive the same event more than once.

This may happen because a consumer processes a message but fails before the corresponding offset is committed.

────────

Decision

Consumers must be designed to tolerate duplicate events for business operations where duplicate side effects are unacceptable.

────────

Rationale

Offset management alone does not provide business-level exactly-once semantics.

A durable idempotency mechanism is therefore required when duplicate business effects must be prevented.

────────

Consequences

Positive

• safer retry behaviour;
• resilience to consumer restarts;
• protection against duplicate side effects.

Negative

• additional state;
• additional storage;
• more complex processing logic.

────────

Principle

Message delivery semantics and business-effect semantics must be considered separately.
