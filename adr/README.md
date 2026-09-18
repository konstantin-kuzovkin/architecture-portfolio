# Architecture Decision Records

This directory contains selected Architecture Decision Records (ADRs) demonstrating how important architectural decisions are analysed, compared and documented.

The purpose of an ADR is not to present a universally correct solution.

The purpose is to preserve:

- the problem;
- the context;
- the constraints;
- the alternatives considered;
- the evaluation criteria;
- the selected decision;
- the consequences;
- the rejected alternatives.

## ADR Index

| ADR | Decision | Related Case |
|-----|----------|--------------|
| [ADR-001](ADR-001-operation-state-ownership.md) | Operation State Ownership | 01 — Payment & Transfer |
| [ADR-002](ADR-002-at-least-once-with-idempotent-consumers.md) | At-Least-Once Delivery with Idempotent Consumers | 02 — Event-Driven Architecture |
| [ADR-003](ADR-003-platform-governance-vs-team-autonomy.md) | Platform Governance vs Team Autonomy | 04 — Platform Architecture |
| [ADR-004](ADR-004-workflow-orchestration-vs-choreography.md) | Workflow Orchestration vs Choreography | 05 — Workflow & Process Orchestration |

## Decision Principles

The ADR collection follows several principles:

- decisions are context-dependent;
- alternatives should be considered before selecting a solution;
- trade-offs should be explicit;
- rejected alternatives should be documented;
- architectural decisions should remain traceable to the problem and constraints;
- no technology is treated as universally correct.
