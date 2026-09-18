# ADR-001: Operation State Ownership

## Status

Accepted

## Context

A distributed payment or transfer operation may involve multiple systems, including a client application, transfer service, core banking systems and external payment networks.

The operation may pass through states such as:

- NEW
- PROCESSING
- COMPLETED
- FAILED
- UNKNOWN

A distributed operation can also experience timeouts or partial failures where the final external outcome is temporarily unavailable.

If multiple components can independently modify the business state, the system can develop conflicting interpretations of the same operation.

## Problem

The architecture must define which component owns the current business state and which component is allowed to perform state transitions.

The solution must also support:

- idempotency;
- concurrency control;
- auditability;
- reconciliation;
- recovery;
- controlled manual operations.

## Constraints

- Multiple systems participate in the operation.
- External systems may return delayed or unavailable results.
- Duplicate requests are possible.
- Manual investigation may be required.
- The current business state must have a single authoritative owner.

## Alternatives

### Alternative 1 — Distributed state ownership

Allow participating systems to maintain and modify their own representation of the operation state.

### Alternative 2 — Client application owns the state

Treat the client-visible operation status as the authoritative state.

### Alternative 3 — Transfer Service owns the state

The Transfer Service maintains the authoritative operation state and validates all state transitions.

## Evaluation Criteria

- Consistency of business state
- Concurrency control
- Idempotency
- Recovery
- Auditability
- Operational support
- Ability to handle UNKNOWN outcomes

## Decision

The Transfer Service is the authoritative owner of the operation state.

All business state transitions must pass through the Transfer Service.

Operational users and supporting systems must not directly modify the operation state in the database.

The Transfer Service validates:

- current state;
- requested transition;
- idempotency;
- concurrency conditions;
- authorization;
- audit requirements.

The database stores the authoritative state but does not become the business decision-maker.

## Consequences

### Positive

- One authoritative state owner.
- Explicit and testable state transitions.
- Reduced risk of conflicting state updates.
- Centralised idempotency and concurrency control.
- Easier reconciliation and recovery.
- Controlled manual operations.

### Negative

- The Transfer Service becomes a critical component.
- Additional service calls may be required for operational actions.
- State-transition logic requires explicit testing and maintenance.

## Rejected Alternatives

Distributed state ownership was rejected because different components could produce conflicting interpretations of the same operation.

Client-owned state was rejected because the client cannot reliably determine the final state of a distributed operation.

Direct database modification by operational users was rejected because it bypasses business validation and state-transition rules.

## Related Case

[01 — Payment & Transfer Architecture](../01-payment-transfer-architecture/README.md)
