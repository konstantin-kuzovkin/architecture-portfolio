ADR-003: Bounded Retry and Dead-Letter Processing

Status

Accepted

Context

Temporary processing failures should be retried, but indefinite retries can block processing and create operational instability.

────────

Decision

Use bounded retries followed by a dead-letter flow for messages that cannot be successfully processed.

────────

Rationale

This approach:

• isolates poison messages;
• prevents infinite retry loops;
• protects normal processing;
• creates an explicit operational recovery path.

────────

Consequences

A DLQ introduces operational responsibilities:

• monitoring;
• investigation;
• replay;
• retention;
• access control.

A DLQ without an operational process simply moves the problem rather than solving it.

────────

Principle

Failure handling must include both technical isolation and business recovery.
