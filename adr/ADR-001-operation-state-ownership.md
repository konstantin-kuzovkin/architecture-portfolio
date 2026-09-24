# ADR-001: Operation State Ownership

## Status

Accepted

## Context

A transfer involves a client channel, a Transfer Service, core banking (ABS) and an external payment network. The network result can arrive late or be lost. Several components could keep a status for the same operation. Reference targets are in [non-functional-requirements.md](../01-payment-transfer-architecture/non-functional-requirements.md): p95 time to a terminal state <= 5 s, RPO = 0, 99.95% availability.

## Problem

Which component owns the business state of the operation and is the only one allowed to change it? The answer must support idempotency, concurrency control, audit, reconciliation and controlled manual work.

## Alternatives

### A. Distributed ownership
Each system keeps and updates its own status of the operation.

### B. Core banking (ABS) owns the operation state
The operation lifecycle is stored in the ledger system.

### C. Transfer Service owns the operation state; ABS owns balances and holds (chosen)
A dedicated service stores the lifecycle and validates every transition. ABS stays the owner of money.

### D. Workflow engine owns the operation state
The BPMN engine variables are the source of truth.

## Evaluation

| Criterion | A | B | C | D |
|---|---|---|---|---|
| One source of truth | No | Yes | Yes | Yes |
| Handling of the unknown network result | Poor | Medium | Good | Good |
| Change speed (ABS is a shared legacy system) | Good | Poor | Good | Good |
| Audit of every transition | Poor | Good | Good | Medium |
| Manual actions under the same rules | Poor | Medium | Good | Poor (engine tools bypass business rules) |
| Independence from a tool | Good | Poor | Good | Poor |

## Decision

Choose **C**. The Transfer Service is the only writer of the operation state. ABS owns balances and holds.

The workflow engine (case 05) may run the process, but it does not own the state. Its job workers call the Transfer Service to request transitions. Process variables are a copy for the engine, not the truth.

Operational users and support tools never write the state directly in the database.

## Consequences

Positive:
- One authoritative state; explicit and testable transitions.
- Idempotency and concurrency control in one place (unique key, version column).
- Reconciliation and manual work use the same state machine.

Negative and how they are handled:
- **The Transfer Service is a critical component.** Handled by: stateless instances behind a load balancer, a highly available database with a synchronous replica (RPO = 0), a recovery job for stuck operations, timeouts and circuit breakers on every external call.
- **Extra calls for operational actions.** Accepted; operators use an operations console that calls the service.
- **Transition logic needs maintenance and tests.** Accepted; the state machine is documented and tested with a transition table.

## Rejected alternatives

- **A** was rejected because components produce conflicting views of the same operation, and nobody can decide the truth after a timeout.
- **B** was rejected because ABS is a shared system with a slow release cycle and no concept of the network's uncertain result. Putting network states into it couples every team to ABS.
- **D** was rejected because engine tools can change variables without business validation, and the state would depend on one technology.

## Related

[Case 01 — Payment & Transfer](../01-payment-transfer-architecture/README.md), [state-machine.md](../01-payment-transfer-architecture/state-machine.md)
