# Reconciliation

## Purpose

Reconciliation resolves operations for which the final processing result cannot be reliably determined from the initial synchronous interaction.

The primary example is an external timeout after the request may already have been accepted by the payment network.

## Reconciliation Flow

```text
UNKNOWN
   │
   ▼
Reconciliation Request
   │
   ▼
Payment Network
   │
   ├──── SUCCESS ────► COMPLETED
   │
   ├──── FAILED ─────► FAILED
   │
   └──── UNKNOWN ────► remain unresolved
```

## Reconciliation Sources

An operation may enter reconciliation because of:

• network timeout;
• lost external response;
• temporary external unavailability;
• inconsistent technical responses;
• operational investigation.

## Automatic Reconciliation

Automatic reconciliation may periodically identify unresolved operations and request their external status.

Example:

```text
Scheduler
    │
    ▼
Find UNKNOWN operations
    │
    ▼
Request external status
    │
    ▼
Apply valid transition
```

## Manual Reconciliation

If automatic reconciliation cannot determine the final outcome, an authorised operator may investigate the operation.

The operator should:

1. identify the operation;
2. verify internal state;
3. obtain the external status;
4. validate the result;
5. perform an allowed transition;
6. record the action in the audit trail.

## Manual Actions

The operator must not be able to arbitrarily set a status.

Instead, the operation should expose only actions allowed by the state machine.

For example:

```text
UNKNOWN
   │
   ├── Confirm successful processing
   │          ↓
   │      COMPLETED
   │
   └── Confirm unsuccessful processing
              ↓
           FAILED
```

## Reconciliation Idempotency

Repeated reconciliation requests must be safe.

If an operation has already transitioned to a terminal state, subsequent reconciliation attempts must not modify the result unless an explicitly defined correction process exists.

## Reconciliation Metrics

Recommended metrics:

• number of UNKNOWN operations;
• age of oldest UNKNOWN operation;
• reconciliation success rate;
• reconciliation failure rate;
• average reconciliation time;
• manual investigation backlog;
• number of manually resolved operations.

## Key Principle

Reconciliation is not an exceptional workaround.

It is a normal part of reliable distributed financial processing where the final result may be temporarily unavailable.
