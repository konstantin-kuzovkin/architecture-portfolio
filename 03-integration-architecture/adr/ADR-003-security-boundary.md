ADR-003: Explicit Security at Integration Boundaries

Status

Accepted

Context

Distributed systems contain multiple trust boundaries.

Authentication and authorisation requirements may differ between client-facing, internal and external integrations.

────────

Decision

Define security requirements explicitly for every integration boundary.

Depending on the boundary, use appropriate mechanisms such as:

• JWT;
• OAuth/OIDC;
• mTLS;
• certificate-based authentication.

────────

Rationale

Security should be part of the integration contract rather than an implementation detail added after the API has been designed.

────────

Consequences

Every integration specification must identify:

• authentication mechanism;
• authorisation requirements;
• credential/certificate requirements;
• trust boundary;
• security failure behaviour.

────────

Principle

Every integration boundary must have an explicit security model.
