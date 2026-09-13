Integration Error Handling

Error Classification

Integration errors should be classified before defining the recovery strategy.

Validation Error

The request is invalid.

Action:

```text
Reject
```

Authentication Error

The caller cannot be authenticated.

Action:

```text
Reject
```

Authorisation Error

The caller is authenticated but lacks permission.

Action:

```text
Reject
```

Business Error

The request is valid technically but cannot be processed according to business rules.

Action:

```text
Return deterministic business error
```

Technical Error

A downstream or infrastructure component fails.

Action:

```text
Retry / fallback / fail
```

depending on the failure and operation semantics.

Timeout

No response was received within the defined timeout.

Action depends on whether the operation is safely retryable.

Unknown Result

The request may have been accepted by the downstream system, but the result is unknown.

Action:

```text
Reconciliation
```

────────

Error Mapping

Different downstream systems may use different technical error formats.

The integration layer should map these into a consistent external error model.

```text
SOAP Fault ──────┐
                 │
REST 5xx ────────┼──► Integration Error Model
                 │
Kafka failure ───┘
```

────────

Error Code

Business errors should use stable machine-readable codes.

Example:

```text
INVALID_REQUEST
NOT_AUTHORIZED
RESOURCE_NOT_FOUND
INVALID_STATE_TRANSITION
DOWNSTREAM_UNAVAILABLE
DOWNSTREAM_TIMEOUT
UNKNOWN_RESULT
```

────────

Principle

Error handling is part of the API contract.

It must be designed together with the happy path rather than added after implementation.
