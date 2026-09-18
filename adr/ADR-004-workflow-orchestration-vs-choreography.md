# ADR-004: Workflow Orchestration vs Choreography

## Status

Accepted

## Context

Long-running business processes may involve several independent services.

A process can be implemented through service choreography, where services react to events independently, or through orchestration, where a workflow component coordinates the process.

The choice affects visibility, state ownership, recovery and operational support.

## Problem

The architecture must determine when explicit workflow orchestration should be preferred over distributed event choreography.

## Constraints

- Processes may contain multiple business steps.
- Some steps may require retries or compensation.
- Some processes may contain human tasks.
- Process state must remain observable.
- Operational teams may need to investigate and recover failed processes.

## Alternatives

### Alternative 1 — Event choreography

Each service reacts to events and decides independently what action to perform next.

### Alternative 2 — Central workflow orchestration

A workflow component explicitly coordinates the process and tracks process state.

### Alternative 3 — Hybrid approach

Use orchestration for long-running business processes while allowing event-driven communication inside individual process steps.

## Evaluation Criteria

- Process visibility
- State ownership
- Recovery
- Compensation
- Operational support
- Coupling
- Scalability
- Complexity

## Decision

Use workflow orchestration for long-running business processes that require explicit state, recovery, compensation, timeouts or human tasks.

Use event-driven choreography where independent services can react to events without requiring a central process coordinator.

A hybrid architecture is therefore acceptable.

The decision should be based on process semantics rather than on a general preference for one communication style.

## Consequences

### Positive

- Explicit process state.
- Predictable recovery.
- Central visibility of long-running workflows.
- Easier implementation of compensation and human tasks.
- Clear operational control.

### Negative

- Workflow infrastructure introduces an additional architectural component.
- The workflow definition requires lifecycle management.
- Excessive orchestration can create unnecessary coupling.

## Rejected Alternatives

Pure choreography was rejected for processes where state, recovery and compensation must be centrally observable.

Pure orchestration was rejected as a universal approach because simple independent event reactions do not necessarily require a workflow coordinator.

## Related Case

[05 — Workflow & Process Orchestration](../05-workflow-orchestration/README.md)
