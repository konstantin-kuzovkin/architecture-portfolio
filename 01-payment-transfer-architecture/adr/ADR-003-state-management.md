# ADR-003: Explicit UNKNOWN State

## Context

A synchronous request to an external payment network may fail with a timeout even though the external system has already accepted the operation.

Therefore, a technical timeout does not necessarily indicate a business failure.

## Problem

Consider:

```text
Transfer Service
      │
      ▼
Payment Network
      │
      ▼
Operation accepted
      │
      X
Response lost
```

The Transfer Service does not know the final result.

## Options

Option 1

Set operation to FAILED.

Option 2

Keep operation in PROCESSING indefinitely.

Option 3

Set operation to UNKNOWN and initiate reconciliation.

## Decision

Use an explicit UNKNOWN state.

## Rationale

UNKNOWN accurately represents the information available to the system.

It avoids incorrectly declaring a financial operation failed when the external system may have successfully processed it.

It also creates an explicit trigger for reconciliation.

## Consequences

Positive

• Prevents incorrect failure classification.
• Provides explicit recovery path.
• Improves operational visibility.
• Supports manual investigation.

Negative

• Requires reconciliation.
• Introduces an additional lifecycle state.
• Operations must monitor unresolved transactions.

## Principle

The system must distinguish:

```text
FAILED
```

from:

```text
UNKNOWN
```

because they represent fundamentally different business meanings.
