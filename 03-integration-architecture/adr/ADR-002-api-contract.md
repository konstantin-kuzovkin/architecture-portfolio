ADR-002: API Contract as a First-Class Artefact

Status

Accepted

Context

Distributed teams need an explicit agreement about how services communicate.

Informal documentation is insufficient when APIs are independently developed and deployed.

────────

Decision

Maintain API contracts as machine-readable OpenAPI specifications.

────────

Rationale

OpenAPI allows teams to define:

• endpoints;
• request and response models;
• validation;
• errors;
• authentication;
• examples.

The contract can also support automated tooling and validation.

────────

Consequences

API changes must be reviewed as contract changes.

Breaking changes require explicit compatibility and migration decisions.

────────

Principle

An API is a contract between teams, not merely an implementation detail of one service.
