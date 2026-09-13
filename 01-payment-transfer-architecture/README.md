Payment & Transfer Architecture

Overview

This case study presents a reference architecture for a payment/transfer operation involving a client application, an orchestration service, core banking systems, an audit service and an external payment processing network.

The architecture addresses a distributed business transaction where the final result may not be immediately available to the initiating channel.

The primary focus is reliability, consistency, idempotency, controlled state transitions and operational recovery.

Portfolio note: This is a sanitized and reconstructed architecture case. It does not contain confidential information, production endpoints, internal system names, customer data or proprietary implementation details.

⸻

Business Context

A customer initiates a transfer through a client-facing channel.

The request is processed by a dedicated Transfer Service that coordinates interactions with internal banking systems and an external payment network.

The external network may accept the transaction while the final processing status is not immediately available to the client channel.

This creates an important distributed-systems problem:

How should the bank maintain a reliable operation state when different systems may temporarily have different views of the same transaction?

⸻

Architectural Goals

The solution must provide:

* a single authoritative operation state;
* deterministic and controlled state transitions;
* idempotent request processing;
* protection against concurrent updates;
* reliable interaction with external systems;
* explicit timeout and unknown-result handling;
* reconciliation of uncertain operations;
* auditability;
* operational recovery;
* clear separation between internal operation state and client-facing status.

⸻

Main Components

Client Application

Initiates the transfer and displays a customer-facing representation of the operation state.

Transfer Service

Acts as the orchestration layer and the single source of truth for the internal operation state.

Responsible for:

* validating requests;
* creating operations;
* controlling state transitions;
* coordinating downstream interactions;
* enforcing idempotency;
* handling technical and business failures;
* initiating reconciliation.

Operations Database

Persistent storage of operation state and technical processing information.

Used for:

* operation identification;
* idempotency;
* state management;
* concurrency control;
* audit correlation;
* reconciliation support.

Core Banking System

Responsible for banking-account operations and the corresponding financial processing within the bank.

Payment Processing Network

External processing component responsible for processing the transfer outside the immediate transaction boundary of the Transfer Service.

Audit Service

Stores auditable information about important business and technical events.

Operations Console

Provides controlled access for authorised operational staff to investigate operations requiring manual reconciliation.

⸻

High-Level Architecture

                         ┌──────────────────┐
                         │ Client Application│
                         └────────┬─────────┘
                                  │
                                  ▼
                       ┌────────────────────┐
                       │   Transfer Service │
                       │    Orchestrator    │
                       └───────┬───┬────────┘
                               │   │
                  ┌────────────┘   └─────────────┐
                  ▼                              ▼
          ┌───────────────┐              ┌───────────────┐
          │ Operations DB │              │  Audit Service │
          └───────────────┘              └───────────────┘
                  │
                  │
        ┌─────────┴──────────┐
        ▼                    ▼
┌───────────────┐    ┌────────────────────┐
│ Core Banking  │    │ Payment Processing │
│    System     │    │      Network       │
└───────────────┘    └────────────────────┘
                              │
                              ▼
                       External Result
                              │
                              ▼
                    Reconciliation Process
                              │
                              ▼
                     Operations Console

⸻

Core Architectural Principle

The Transfer Service owns the internal lifecycle of the operation.

There must be exactly one authoritative current state for an operation.

Client-facing applications may translate this state into their own user-oriented statuses, but they must not become an independent source of truth for the operation lifecycle.

⸻

State Management

The operation lifecycle is represented as an explicit state machine.

A transition is valid only when:

1. the current state allows the requested transition;
2. the transition is triggered by an allowed event;
3. the required business and technical conditions are satisfied.

Invalid transitions must return a deterministic business error.

⸻

Example State Model

NEW
 │
 │ start
 ▼
PROCESSING
 │
 ├──────────── success ───────────► COMPLETED
 │
 ├──────────── business error ────► FAILED
 │
 └──────────── timeout/unknown ───► UNKNOWN
                                      │
                                      │ reconciliation
                              ┌───────┴───────┐
                              ▼               ▼
                         COMPLETED          FAILED

⸻

Idempotency

The operation is associated with an idempotency key / operation identifier.

The identifier is persisted in the Operations Database and is used to prevent duplicate processing caused by client retries, network retries or repeated delivery of the same command.

The service must distinguish between:

* a new operation;
* a repeated request for an existing operation;
* a request that conflicts with an existing operation;
* an operation already in a terminal state.

⸻

Concurrency

Concurrent updates to the same operation must not result in lost updates or invalid state transitions.

State modification is performed within a database transaction.

The persistence layer provides the required concurrency control so that competing transactions cannot independently overwrite the operation state.

The business transition is therefore treated as an atomic operation:

Read current state
      ↓
Validate transition
      ↓
Update state
      ↓
Persist
      ↓
Commit

⸻

Unknown Result

A timeout does not necessarily mean that the transfer failed.

For example:

Transfer Service
      │
      │ request
      ▼
Payment Network
      │
      │ accepted
      ▼
      X  response lost / timeout
      │
      ▼
Transfer Service
      │
      ▼
UNKNOWN

The operation must not automatically be marked as failed solely because the response was not received.

This prevents an important class of financial consistency problems.

⸻

Reconciliation

Operations in an uncertain state are reconciled using the authoritative status provided by the external payment network.

The reconciliation process may be:

* automatic;
* scheduled;
* initiated by an authorised operator.

If the external status confirms successful processing, the operation transitions to COMPLETED.

If the external status confirms unsuccessful processing, the operation transitions to FAILED.

Manual intervention is subject to the same state-transition rules as automated processing.

⸻

Manual Investigation

An operational investigation is required for cases where the final status cannot be determined automatically.

The operations user must:

1. identify the operation;
2. inspect the internal operation state;
3. request or retrieve the external processing status;
4. compare the external result with the internal state;
5. perform only an allowed state transition;
6. record the action in the audit trail.

Manual intervention must not bypass the state machine.

⸻

Audit

Important lifecycle events are auditable, including:

* operation creation;
* processing start;
* downstream request;
* received response;
* timeout;
* transition to unknown state;
* reconciliation attempt;
* manual investigation;
* manual state transition.

Audit records should contain sufficient correlation information to reconstruct the operation lifecycle.

⸻

Security

The architecture assumes:

* authenticated client requests;
* service-to-service authentication;
* authorisation for operational actions;
* encrypted communication;
* controlled access to operational tooling;
* auditability of privileged actions.

Operational users must not be able to arbitrarily assign an operation state.

⸻

Observability

The operation should be traceable across participating components using a correlation identifier.

Recommended observability dimensions include:

* operation ID;
* correlation ID;
* current state;
* transition;
* downstream system;
* processing duration;
* timeout;
* retry count;
* reconciliation result;
* error category.

Metrics should allow operators to identify abnormal growth of:

* failed operations;
* unknown operations;
* reconciliation backlog;
* processing latency;
* downstream timeouts.

⸻

Key Design Decisions

1. Transfer Service is the orchestration component.
2. Transfer Service owns the authoritative operation state.
3. Operation state is persisted.
4. State transitions are explicitly modelled.
5. Invalid transitions are rejected.
6. Idempotency is implemented using persistent operation identity.
7. Concurrent state modifications are protected transactionally.
8. Unknown results are represented explicitly rather than interpreted as failures.
9. Reconciliation resolves uncertain operations.
10. Manual intervention is controlled by the same state machine.

⸻

Trade-offs

Advantages

* Clear ownership of operation state.
* Predictable lifecycle.
* Strong protection against duplicate processing.
* Explicit handling of uncertain outcomes.
* Better operational support.
* Improved auditability.

Trade-offs

* Additional persistence and transaction management.
* More complex state management.
* Need for reconciliation mechanisms.
* Operational tooling is required.
* The orchestration service becomes an important component of the overall solution.

⸻

What I Personally Contributed

The case reflects my approach to system analysis and architecture of integration-heavy banking processes.

The key areas of contribution include:

* decomposition of the distributed business process;
* definition of service responsibilities;
* modelling of operation states and transitions;
* analysis of idempotency requirements;
* analysis of concurrent updates and transactional consistency;
* design of failure and timeout scenarios;
* reconciliation and manual investigation scenarios;
* definition of integration and audit requirements;
* preparation of technical documentation and architecture models.

My Role

Role: System Analyst / Architecture-oriented System Analyst

Responsibilities

• requirements analysis;
• solution design;
• state model definition;
• integration analysis;
• idempotency design;
• failure scenario analysis;
• reconciliation design;
• technical documentation;
• architecture decision analysis.

────────

What This Case Demonstrates

• Distributed transaction design
• State ownership
• Idempotency
• ACID transaction boundaries
• Failure handling
• Unknown external outcomes
• Reconciliation
• Reliability
• Architecture trade-offs

────────

Portfolio Note

This is a reconstructed and sanitised portfolio case created to demonstrate architectural reasoning and system analysis practices.

It is not a copy of a production system.
