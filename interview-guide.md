Interview Guide

This document contains typical architecture and system analysis questions related to the portfolio cases.

────────

01 — Payment & Transfer Architecture

Typical Questions

Why is an UNKNOWN state required?

Because a timeout does not necessarily mean that the external operation failed.

The external system may have completed the operation while the response was lost.

Therefore the system should distinguish:

• confirmed success;
• confirmed failure;
• unknown external outcome.

────────

Where should idempotency be implemented?

Idempotency should be implemented at the business operation boundary.

The system should persist the operation identifier and prevent duplicate processing of the same logical request.

────────

Who owns the operation state?

The service responsible for the business operation should own the authoritative state.

Client applications may display their own labels, but the backend service should remain the source of truth.

────────

How is concurrency controlled?

State changes are performed within transactional boundaries with appropriate database locking or optimistic concurrency mechanisms.

────────

Why is reconciliation required?

Because distributed systems can produce situations where the local state and external state temporarily disagree.

Reconciliation restores consistency without blindly repeating the original operation.

────────

02 — Event-Driven Architecture

Typical Questions

Does Kafka guarantee exactly-once business processing?

Transport-level semantics should not be confused with business-level exactly-once processing.

A practical design often uses at-least-once delivery together with idempotent consumers.

────────

Where is ordering guaranteed?

Kafka ordering is guaranteed within a partition.

Therefore the partition key should be selected according to the business entity for which ordering matters.

────────

Why use a DLQ?

A dead-letter queue provides controlled isolation of messages that cannot be successfully processed after the allowed retry policy.

────────

How are duplicate events handled?

Consumers should be designed to be idempotent.

The business event identifier or another deterministic deduplication key can be used to prevent repeated business effects.

────────

03 — Integration Architecture

Typical Questions

REST or Kafka?

REST is appropriate when the caller requires an immediate response.

Kafka is appropriate when asynchronous communication, decoupling or event-driven processing is required.

────────

Why keep SOAP?

SOAP can remain appropriate for legacy or contract-heavy integrations where replacing the existing interface would introduce unnecessary risk.

────────

What should an API contract contain?

At minimum:

• endpoint;
• HTTP method;
• request;
• response;
• validation;
• error model;
• security;
• idempotency;
• versioning.

────────

04 — Platform Architecture

Typical Questions

Why create platform standards?

To reduce repeated architectural decisions and improve consistency across multiple teams.

────────

What should be standardised?

Typical areas:

• API design;
• event contracts;
• security;
• observability;
• documentation;
• service ownership;
• operational readiness.

────────

05 — Workflow & Process Orchestration

Typical Questions

Why use workflow orchestration?

When a business process spans multiple systems, events, long-running operations and potentially human tasks, explicit orchestration can provide durable state and controlled recovery.

────────

Is compensation the same as database rollback?

No.

A database rollback reverses a local transaction.

Compensation is a business operation that attempts to semantically reverse or correct a previously completed business action.

────────

Who owns workflow state?

The workflow engine or orchestration component should own process execution state, while individual services remain responsible for their own business data.

────────

06 — AI-Assisted System Analysis

Typical Questions

How do you control hallucinations?

Hallucination control should not rely only on prompt engineering.

The workflow separates:

```text
FACT
INFERENCE
UNKNOWN
```

The agent uses controlled source access, specialised tools, structured outputs and validation.

If information is unavailable, the system should return UNKNOWN rather than inventing an endpoint, Kafka topic, database table or business rule.

────────

Why use specialised agents?

Different technical domains require different analysis rules.

Examples:

• API;
• Kafka;
• BPMN;
• database;
• Java;
• metrics.

Specialisation allows each agent to focus on a defined analytical responsibility.

────────

Leadership Questions

How do you lead without formal people management?

I focus on technical influence:

• reusable standards;
• templates;
• knowledge sharing;
• technical reviews;
• interviews;
• cross-team coordination;
• mentoring.

The objective is to improve engineering consistency rather than manage people administratively.

────────

Architecture Interview Principle

When answering architecture questions:

1. Clarify the business problem.
2. Identify constraints.
3. Define system boundaries.
4. Identify ownership.
5. Define contracts.
6. Analyse failure scenarios.
7. Consider security.
8. Consider observability.
9. Explain trade-offs.
10. Document the decision.

Avoid jumping directly to technology selection.
