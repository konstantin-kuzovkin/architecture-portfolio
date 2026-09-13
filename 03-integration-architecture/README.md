Integration Architecture

Overview

This case study demonstrates an integration architecture combining synchronous REST APIs, legacy SOAP integrations and asynchronous event-driven communication.

The objective is to demonstrate how integration patterns are selected according to business requirements, consistency needs, latency expectations and characteristics of downstream systems.

> **Portfolio note:** This is a sanitized and reconstructed architecture case. It does not contain confidential information, production endpoints, internal system names, credentials or proprietary implementation details.

────────

Business Context

A modern microservice platform often needs to integrate with systems based on different technologies and communication models.

A single business process may therefore involve:

• modern REST services;
• legacy SOAP systems;
• asynchronous Kafka events;
• authentication and authorisation services;
• external systems requiring mutual TLS.

The architecture must provide a consistent integration approach despite these technical differences.

────────

Architectural Problem

The main question is not:

> Which technology should be used?

The main question is:

> Which interaction pattern best matches the business and technical requirements of a particular integration?

────────

Integration Patterns

|Pattern         |Typical Use                                        |
|----------------|---------------------------------------------------|
|REST            |Synchronous request/response                       |
|SOAP            |Integration with legacy or contract-heavy systems  |
|Kafka           |Asynchronous event-driven communication            |
|mTLS            |Strong service-to-service / external authentication|
|JWT / OAuth/OIDC|Identity and access control                        |

────────

High-Level Architecture

```text
                         ┌─────────────────────┐
                         │     Client / API    │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │   Integration      │
                         │      Service       │
                         └──────┬────┬────┬───┘
                                │    │    │
                   ┌────────────┘    │    └─────────────┐
                   ▼                 ▼                  ▼
              REST API             SOAP              Kafka
                   │                 │                  │
                   ▼                 ▼                  ▼
             Microservice       Legacy System     Event Platform

                         ┌─────────────────────┐
                         │ Security Boundary   │
                         │ JWT / mTLS / Auth   │
                         └─────────────────────┘
```

────────

REST Integration

REST is appropriate when the caller requires a synchronous response.

Typical characteristics:

• request/response;
• immediate validation;
• explicit API contract;
• HTTP status codes;
• synchronous error handling.

────────

SOAP Integration

SOAP may be appropriate when integrating with legacy or contract-oriented systems.

Typical characteristics:

• XML-based messages;
• WSDL-driven contract;
• strict schemas;
• enterprise legacy compatibility.

The architecture should isolate legacy-specific concerns from modern domain services where practical.

────────

Kafka Integration

Kafka is appropriate when:

• asynchronous processing is acceptable;
• consumers should be temporally decoupled;
• multiple consumers may react independently;
• event replay is useful;
• producer availability should not depend on immediate consumer availability.

────────

Security

The architecture may use different security mechanisms depending on the integration boundary.

Examples:

• JWT for authenticated API access;
• OAuth/OIDC for identity;
• mTLS for strong service-to-service authentication;
• certificate-based authentication for selected external integrations.

────────

Error Handling

Errors are classified into:

• validation errors;
• authentication/authorisation errors;
• business errors;
• technical errors;
• timeout;
• downstream unavailable;
• unknown result.

The error model should be consistent at the API boundary even when downstream systems use different technical error representations.

────────

Reliability

Integration reliability is addressed through:

• timeouts;
• controlled retries;
• idempotency;
• circuit breaking where appropriate;
• asynchronous processing;
• reconciliation where the final outcome is uncertain.

────────

API Contract

The API contract should define:

• operations;
• request structure;
• response structure;
• validation rules;
• error model;
• authentication requirements;
• idempotency requirements;
• versioning strategy.

OpenAPI can be used as the formal API contract.

────────

Observability

Each integration should support correlation across participating systems.

Recommended identifiers:

• request ID;
• correlation ID;
• operation ID;
• trace ID.

────────

Key Architectural Principles

1. Select integration patterns based on business requirements.
2. Keep synchronous and asynchronous responsibilities explicit.
3. Isolate legacy integration complexity.
4. Treat API contracts as first-class architecture artefacts.
5. Define errors explicitly.
6. Apply security appropriate to each trust boundary.
7. Design for downstream failures.
8. Make integrations observable.
9. Avoid technology-driven architecture decisions.
10. Document significant integration decisions through ADRs.

────────

What I Personally Contributed

The case reflects my approach to integration architecture and system analysis.

Key areas include:

• decomposition of integration scenarios;
• selection of synchronous versus asynchronous patterns;
• REST API contract design;
• SOAP integration analysis;
• Kafka integration modelling;
• security requirements;
• error handling;
• timeout and retry scenarios;
• idempotency requirements;
• sequence diagrams;
• OpenAPI-oriented technical documentation;
• integration standards.
