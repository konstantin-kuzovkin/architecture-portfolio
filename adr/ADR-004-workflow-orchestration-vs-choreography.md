# ADR-004: Workflow Orchestration vs Choreography

## Status

Accepted

## Context

Some business processes span several services, wait for external results, need timers, compensation or human decisions. Others are simple reactions to an event. Using one style for everything makes simple things heavy or complex things invisible.

## Problem

When should a process be run by a workflow engine (orchestration), and when should services react to events on their own (choreography)?

## Alternatives

1. **Choreography only.** Every service reacts to events.
2. **Orchestration only.** Every multi-service flow runs in a workflow engine.
3. **Rule-based hybrid (chosen).** Orchestration when a process meets the rule below; choreography otherwise.

## Decision rule

Use orchestration when a process meets **at least two** of these conditions:

1. It waits longer than 1 minute (timers, external results).
2. It contains a human task.
3. It must compensate two or more completed steps.
4. It crosses three or more services that share one business outcome and need one place to see its status.

Otherwise use choreography with Kafka events.

## Application

| Process | Conditions met | Style |
|---|---|---|
| Transfer processing (hold, submit, wait, reconcile, capture or release) | 1, 2, 3 | Orchestration. See [BPMN model](../05-workflow-orchestration/artifacts/workflow-example.bpmn). |
| Notification after `COMPLETED` or `FAILED` | none | Choreography. See [AsyncAPI contract](../02-event-driven-architecture/artifacts/asyncapi-example.yaml). |
| Statement or analytics update after a transfer | none | Choreography. |

State ownership stays with the Transfer Service in both styles ([ADR-001](./ADR-001-operation-state-ownership.md)). The engine runs the process; it does not own the operation state.

## Consequences

Positive:
- Long-running processes have visible state, timers and recovery.
- Simple reactions stay simple and loosely coupled.
- The rule is testable: a new process is classified before design starts.

Negative:
- Two styles to operate and to teach.
- The engine is an extra critical component. Its choice is compared in [decision-matrix.md](../05-workflow-orchestration/decision-matrix.md).

## Rejected alternatives

- **Choreography only** was rejected: the transfer process would have no single place to see its status, and compensation and timers would be spread across services.
- **Orchestration only** was rejected: simple event reactions would become heavy processes with no benefit.
