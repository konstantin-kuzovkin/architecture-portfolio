# Operation State Model

## Purpose

This artefact defines the authoritative lifecycle of a payment or transfer operation.

The model separates the internal operation state from customer-facing status labels.

## State Model

```mermaid
stateDiagram-v2
    [*] --> NEW

    NEW --> PROCESSING: start processing

    PROCESSING --> COMPLETED: confirmed success
    PROCESSING --> FAILED: confirmed business failure
    PROCESSING --> UNKNOWN: timeout / uncertain outcome

    UNKNOWN --> COMPLETED: reconciliation confirms success
    UNKNOWN --> FAILED: reconciliation confirms failure

    COMPLETED --> [*]
    FAILED --> [*]
```

## State Definition

| State | Meaning | Terminal |
|---|---|---|
| NEW | Operation created but processing has not started | No |
| PROCESSING | Operation is being processed | No |
| UNKNOWN | Final external outcome cannot currently be established | No |
| COMPLETED | Successful business outcome confirmed | Yes |
| FAILED | Unsuccessful business outcome confirmed | Yes |

## Transition Rules

| Current State | Event | Next State | Condition |
|---|---|---|---|
| NEW | Start processing | PROCESSING | Operation is valid |
| PROCESSING | Success | COMPLETED | Success is confirmed |
| PROCESSING | Business failure | FAILED | Failure is confirmed |
| PROCESSING | Timeout / unknown result | UNKNOWN | Final outcome unavailable |
| UNKNOWN | Reconciliation success | COMPLETED | External status confirms success |
| UNKNOWN | Reconciliation failure | FAILED | External status confirms failure |

## Ownership
The Transfer Service owns the authoritative internal operation state.

Operational users and supporting components must not directly assign arbitrary state values.

All state changes must pass through the same transition validation rules.

## Unknown Outcome

UNKNOWN does not mean that the business operation failed.

It means that the system cannot currently establish the final external outcome.

This distinction prevents an uncertain operation from being incorrectly treated as a confirmed failure.

## Reconciliation

Operations in UNKNOWN are resolved through reconciliation.

The reconciliation process obtains the authoritative external status and requests a validated state transition.

## Client-facing Status

Client applications may map internal states to user-oriented statuses.

For example:

| Internal State | Possible Client Status |
|---|---|
| NEW | Created |
| PROCESSING | In progress |
| UNKNOWN | Processing |
| COMPLETED | Completed |
| FAILED | Failed |

The client-facing status is not the authoritative operation state.

## Architectural Principle

One operation must have one authoritative current internal state and one controlled transition mechanism.
