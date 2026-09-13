ADR-001: Use Event-Driven Communication

Status

Accepted

Context

Multiple services need to react to business events without requiring synchronous availability of every consumer.

Direct synchronous integration would increase coupling between services.

────────

Options

Option 1 — Synchronous REST

Producer directly calls consumers.

Option 2 — Event-Driven Communication

Producer publishes a business event and consumers independently process it.

────────

Decision

Use event-driven communication for scenarios where asynchronous processing is acceptable and consumers do not need to synchronously participate in the producer’s transaction.

────────

Rationale

Benefits include:

• reduced temporal coupling;
• independent consumer scaling;
• independent deployment;
• ability to add new consumers;
• asynchronous processing;
• replay capability where supported by the event-retention strategy.

────────

Consequences

Positive

Services become less dependent on immediate consumer availability.

Negative

The architecture becomes more complex.

Additional concerns include:

• eventual consistency;
• duplicate processing;
• ordering;
• retries;
• observability;
• operational recovery.

────────

Principle

Asynchronous communication should be selected because of the required business interaction model, not simply because Kafka is available.
