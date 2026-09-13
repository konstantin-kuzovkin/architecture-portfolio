Integration Reliability

Timeout

Every synchronous integration should have an explicit timeout policy.

Without a timeout, a failed dependency can consume resources indefinitely.

────────

Retry

Retry should be used only when the operation is safely retryable.

Before retrying, evaluate:

• idempotency;
• error type;
• operation semantics;
• retry count;
• backoff;
• downstream capacity.

────────

Retry Amplification

Uncontrolled retries can make an outage worse.

Example:

```text
Dependency degraded
      ↓
Requests timeout
      ↓
Clients retry
      ↓
More requests
      ↓
Dependency becomes more overloaded
```

Therefore, retry must be bounded.

────────

Circuit Breaker

Where appropriate, a circuit breaker can prevent repeated calls to an unhealthy dependency.

Conceptually:

```text
CLOSED
  │
  │ failures
  ▼
OPEN
  │
  │ recovery check
  ▼
HALF-OPEN
  │
  ├── success → CLOSED
  └── failure → OPEN
```

────────

Idempotency

Retry without idempotency can create duplicate business operations.

Therefore:

> Retry policy and idempotency strategy must be designed together.

────────

Reconciliation

If a timeout creates uncertainty about the final business outcome, reconciliation should be preferred over blind retry.

This is particularly important for financial operations.
