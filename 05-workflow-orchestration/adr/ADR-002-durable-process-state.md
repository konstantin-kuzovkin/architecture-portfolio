# ADR-002 — Durable Process State

## Context

Long-running processes cannot rely on in-memory state.

The process may survive:

• service restart;
• deployment;
• infrastructure failure;
• network interruption;
• dependency outage.

Decision

Process state must be durable and recoverable.

The workflow runtime must be able to reconstruct the process after infrastructure failure.

State Principles

1. State transitions must be explicit.
2. Invalid transitions must be rejected.
3. Process state must be observable.
4. Recovery must not create duplicate business actions.
5. External unknown outcomes must be represented explicitly.

Consequence

Durable state becomes part of the architecture rather than an implementation detail.
