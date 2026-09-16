# ADR-002: Persistent Idempotency

## Context

Clients and infrastructure may retry requests when responses are delayed or lost.

A financial operation must not be executed multiple times because of a technical retry.

## Options

1. In-memory idempotency.
2. Distributed cache only.
3. Persistent database-backed idempotency.

## Decision

Use persistent operation identity stored in the Operations Database.

## Rationale

The operation lifecycle is business-critical and must survive:

• service restarts;
• process failures;
• retries;
• deployment;
• temporary infrastructure failures.

The database provides durable state and can enforce uniqueness at the persistence layer.

## Consequences

Positive

• Durable operation identity.
• Reliable duplicate detection.
• Integration with state management.
• Survives application restarts.

Negative

• Additional database dependency.
• Requires transaction and concurrency management.
• Database availability becomes part of the processing design.

## Principle

Idempotency is treated as a business reliability requirement rather than merely an API convenience.
