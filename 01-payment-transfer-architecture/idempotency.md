# Idempotency

## Problem

Distributed systems commonly retry requests because of network failures, timeouts or temporary unavailability.

A retry may reach the Transfer Service after the original request has already been accepted or processed.

Without idempotency, the same business operation could potentially be executed more than once.

## Idempotency Model

Each logical transfer operation has a unique operation identifier.

```text
Client
  │
  │ operation_id = X
  ▼
Transfer Service
  │
  ▼
Operations DB
```

The identifier is persisted before the operation proceeds into the processing lifecycle.

## Request Scenarios

Scenario 1 — New Operation

```text
operation_id = X
        │
        ▼
not found
        │
        ▼
create operation
        │
        ▼
PROCESSING
```

Scenario 2 — Retry

```text
operation_id = X
        │
        ▼
operation exists
        │
        ▼
return existing operation/result
```

Scenario 3 — Conflicting Request

If the same operation identifier is reused with incompatible business parameters, the request must be rejected rather than interpreted as a new operation.

## Idempotency Storage

The Operations Database stores the operation identity together with the information required to determine whether a request represents:

• a new operation;
• a retry;
• an already completed operation;
• an operation currently being processed;
• a conflicting request.

A uniqueness constraint on the operation identifier can provide an additional protection against duplicate creation.

## Important Principle

Idempotency is not the same as simply checking whether an operation exists.

The system must define what response should be returned for each existing operation state.

For example:

```text
NEW / PROCESSING
→ operation already exists

COMPLETED
→ return successful existing result

FAILED
→ return existing failed result

UNKNOWN
→ return current uncertain state
```

The exact client-facing representation may differ from the internal operation state.

## Idempotency and State Machine

Idempotency and state management work together.

An idempotent request must not bypass the state machine.

A retry of an existing operation should observe the current authoritative state rather than create a second lifecycle.
