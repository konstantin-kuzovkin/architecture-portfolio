# ADR-001 — Workflow Orchestration

## Context

A distributed business process spans multiple independent services and external systems.

The process requires:

• durable state;
• retries;
• timeouts;
• asynchronous waiting;
• failure recovery;
• operational visibility.

Implementing the complete process lifecycle independently inside each service would make the global process difficult to observe and maintain.

## Decision

Use an explicit orchestration model for long-running business processes.

The workflow component coordinates the process while individual services remain responsible for their own business capabilities.

## Consequences

Positive

• explicit process model;
• centralized process visibility;
• easier recovery;
• clear responsibility boundaries;
• support for long-running processes.

Negative

• additional platform component;
• orchestration logic requires governance;
• workflow engine becomes an important infrastructure dependency.

Alternatives

Pure Choreography

Rejected for processes requiring strong global visibility and explicit coordination.

Custom State Machine

Can be appropriate for simple processes but becomes increasingly complex as timers, retries, human tasks and compensation are introduced.

Principle

Use orchestration where the business process itself is a first-class architectural concern.
