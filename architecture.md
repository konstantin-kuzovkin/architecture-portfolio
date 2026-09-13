Architecture Approach

Purpose

This document describes the architecture and system analysis approach used throughout this portfolio.

The portfolio focuses on architecture problems rather than implementation-specific details.

────────

1. Problem First

Architecture starts with the problem.

Before selecting technologies or patterns, I identify:

• business objective;
• system boundaries;
• actors;
• dependencies;
• constraints;
• expected load;
• consistency requirements;
• failure scenarios;
• security requirements.

────────

2. Context

The first architectural question is:

> What systems and components participate in the solution?

I typically model:

• users;
• business systems;
• internal services;
• external systems;
• databases;
• message brokers;
• workflow engines;
• security boundaries.

────────

3. Requirements

Requirements are separated into:

Functional

What the system must do.

Non-functional

How the system must behave.

Examples:

• availability;
• performance;
• scalability;
• security;
• reliability;
• auditability;
• observability;
• maintainability.

────────

4. Architecture

The architecture defines:

• components;
• responsibilities;
• interfaces;
• data flows;
• synchronous interactions;
• asynchronous interactions;
• ownership boundaries;
• state ownership.

Architecture decisions should be traceable to requirements and constraints.

────────

5. Integration

Integration is selected according to the interaction semantics.

Typical patterns:

|Pattern         |Typical Use                            |
|----------------|---------------------------------------|
|REST / OpenAPI  |Synchronous request/response           |
|Kafka / AsyncAPI|Asynchronous event-driven communication|
|SOAP            |Legacy or contract-heavy integrations  |

Technology selection is not the goal.

The goal is to select an interaction model appropriate for the business and technical requirements.

────────

6. Failure Scenarios

Every distributed architecture should consider what happens when dependencies fail.

Typical scenarios:

• timeout;
• unavailable dependency;
• duplicate request;
• duplicate event;
• partial success;
• unknown external outcome;
• message delivery failure;
• concurrent update;
• inconsistent state.

Failure handling is part of the architecture, not an afterthought.

────────

7. State Ownership

For business-critical processes, the architecture should explicitly define:

> Who owns the current state?

The owner should provide:

• valid state transitions;
• deterministic behaviour;
• concurrency control;
• auditability;
• recovery mechanisms.

────────

8. Reliability

Reliability mechanisms may include:

• idempotency;
• retries;
• timeouts;
• circuit breakers;
• reconciliation;
• dead-letter queues;
• recovery workflows;
• controlled manual operations.

Each mechanism should have a defined purpose and operational consequence.

────────

9. Security

Security is considered at architecture boundaries.

Typical concerns:

• authentication;
• authorisation;
• service identity;
• OAuth2;
• JWT;
• mTLS;
• certificates;
• trust boundaries;
• sensitive data handling.

────────

10. Observability

A production-oriented architecture should provide sufficient information to answer:

• What happened?
• When did it happen?
• Which business operation was affected?
• Which component failed?
• Which dependency caused the failure?
• Can the operation be recovered?

Typical mechanisms:

• structured logs;
• metrics;
• traces;
• correlation IDs;
• audit events.

────────

11. Architecture Decisions

Important decisions are documented as ADRs.

Each ADR should contain:

• context;
• problem;
• alternatives;
• decision;
• consequences;
• rejected alternatives.

The purpose of an ADR is not to prove that one solution is universally correct.

The purpose is to preserve the reasoning behind a decision.

────────

12. Trade-offs

Architecture is a sequence of trade-offs.

Typical trade-offs include:

• consistency vs availability;
• synchronous vs asynchronous communication;
• simplicity vs flexibility;
• reliability vs latency;
• centralisation vs autonomy;
• operational complexity vs business flexibility.

The selected solution should make these trade-offs explicit.

────────

13. Documentation

Architecture documentation is treated as an engineering artifact.

Typical artefacts:

• Context diagrams;
• Sequence diagrams;
• State machines;
• BPMN;
• ERD;
• OpenAPI;
• AsyncAPI;
• ADR;
• operational documentation.

────────

14. AI-assisted Analysis

The AI-assisted system analysis case follows the same architecture principles.

AI output is not automatically treated as a system fact.

The analytical workflow distinguishes:

```text
FACT
  ↓
directly supported by source

INFERENCE
  ↓
derived from available information

UNKNOWN
  ↓
required information is unavailable
```

The system should report uncertainty instead of inventing technical details.

────────

Final Principle

A good architecture is not the architecture with the most technologies.

It is the architecture where:

• responsibilities are clear;
• boundaries are explicit;
• contracts are defined;
• failure behaviour is understood;
• security is considered;
• operational behaviour is observable;
• important decisions are documented.
