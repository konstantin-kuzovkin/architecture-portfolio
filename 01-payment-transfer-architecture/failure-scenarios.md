# Failure Scenarios

## Purpose

This document describes the main technical and business failure scenarios for the payment/transfer process.

The objective is to distinguish between:

• confirmed failure;
• temporary technical failure;
• unknown outcome;
• duplicate processing;
• recoverable operational conditions.

## Failure Matrix

|# |Scenario                                          |Internal State                 |Expected Behaviour                   |
|--|--------------------------------------------------|-------------------------------|-------------------------------------|
|1 |Client request timeout before operation creation  |No operation / unknown         |Client may retry                     |
|2 |Duplicate client request                          |Existing state                 |Return existing operation state      |
|3 |Core Banking unavailable                          |PROCESSING / failed attempt    |Retry or fail according to policy    |
|4 |Payment Network timeout                           |UNKNOWN                        |Reconciliation required              |
|5 |Payment Network explicitly rejects operation      |FAILED                         |Persist confirmed failure            |
|6 |Payment Network accepts operation                 |COMPLETED or intermediate state|Persist confirmed result             |
|7 |Response lost after successful external processing|UNKNOWN                        |Do not mark as FAILED; reconcile     |
|8 |Database failure during transition                |Previous state                 |Roll back transaction                |
|9 |Concurrent transition attempt                     |Current state                  |Apply transaction/concurrency control|
|10|Duplicate external response/event                 |Existing state                 |Process idempotently                 |
|11|Reconciliation unavailable                        |UNKNOWN                        |Keep operation unresolved and retry  |
|12|Manual investigation required                     |MANUAL_INVESTIGATION           |Authorised operator investigates     |
|13|Invalid manual transition                         |Current state                  |Reject operation                     |
|14|Audit service temporarily unavailable             |Depends on criticality         |Apply defined audit failure policy   |

## Critical Scenario: Lost Response

One of the most important scenarios is when the external payment network successfully processes the transaction but the response does not reach the Transfer Service.

```text
Transfer Service
      │
      │ request
      ▼
Payment Network
      │
      │ transaction accepted
      ▼
   SUCCESS
      │
      X
 response lost
      │
      ▼
Transfer Service
      │
      ▼
    UNKNOWN
```

The absence of a response does not provide sufficient evidence that the operation failed.

Therefore:

> **Technical timeout must not automatically be interpreted as business failure.**

## Duplicate Request

A client may retry because the original response was lost.

```text
Request #1
operation_id = X
      │
      ▼
Transfer Service
      │
      ▼
PROCESSING

Request #2
operation_id = X
      │
      ▼
Transfer Service
      │
      ▼
Existing operation
```

The second request must not create another financial operation.

## Database Failure

If persistence fails during a state transition:

```text
BEGIN
   │
   ├─ validate transition
   │
   ├─ update operation
   │
   X database error
   │
ROLLBACK
```

The system must not expose a partially committed lifecycle transition.

## Invalid State Transition

Example:

```text
COMPLETED → FAILED
```

This is not a technical failure.

It is an invalid business operation and should return a deterministic business error.

## Design Principle

Failures must be classified before determining the next state.

A timeout, a confirmed rejection and an unknown external outcome are different conditions and must not be collapsed into one generic FAILED state.
