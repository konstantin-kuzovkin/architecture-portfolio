# ADR-003: Platform Governance vs Team Autonomy

## Status

Accepted

## Context

A platform may support many development teams working on multiple business domains.

Without shared standards, teams may implement similar concerns differently, increasing integration complexity and operational inconsistency.

At the same time, excessive centralisation can slow teams down and reduce their ability to make local implementation decisions.

## Problem

The architecture must define which concerns should be governed centrally and which decisions should remain within individual product teams.

## Constraints

- Multiple teams use shared platform capabilities.
- Teams have different business domains and delivery priorities.
- Cross-team interfaces require consistency.
- Platform governance should not become a bottleneck.
- Exceptions will sometimes be necessary.

## Alternatives

### Alternative 1 — Full centralisation

The platform architecture team controls most technical decisions.

### Alternative 2 — Full team autonomy

Each product team independently defines its architecture and engineering standards.

### Alternative 3 — Federated governance

Common cross-team concerns are governed centrally while teams retain ownership of domain-specific implementation decisions.

## Evaluation Criteria

- Cross-team consistency
- Delivery speed
- Architectural control
- Team autonomy
- Reuse
- Operational risk
- Governance overhead

## Decision

Use federated architecture governance.

Central governance should define reusable standards and shared rules for areas such as:

- API contracts;
- event contracts;
- security;
- observability;
- documentation;
- platform capabilities;
- architecture review requirements.

Product teams retain ownership of:

- domain-specific business logic;
- internal implementation details;
- local optimisation;
- delivery decisions within the established boundaries.

Exceptions are explicitly documented and reviewed rather than handled through informal agreements.

## Consequences

### Positive

- Consistent cross-team interfaces.
- Reduced duplication.
- Reusable engineering practices.
- Clear ownership boundaries.
- Product teams retain implementation autonomy.
- Architecture governance becomes more predictable.

### Negative

- Governance processes require maintenance.
- Standards may need periodic evolution.
- Exceptions introduce additional review work.
- Central platform teams must avoid becoming delivery bottlenecks.

## Rejected Alternatives

Full centralisation was rejected because it creates excessive dependency on the platform architecture function and can reduce team autonomy.

Full team autonomy was rejected because shared interfaces, security and operational requirements require cross-team consistency.

## Related Case

[04 — Platform Architecture](../04-platform-architecture/README.md)
