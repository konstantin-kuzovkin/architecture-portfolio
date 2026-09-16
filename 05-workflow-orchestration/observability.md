# Process Observability

## Purpose

A long-running distributed process may execute over minutes, hours or days.

Operational teams therefore need to understand not only whether the process failed, but also:

• where it is currently waiting;
• which step failed;
• which service was involved;
• whether a retry is running;
• whether compensation is required;
• whether human intervention is required.

## Observability Scope

Process observability should cover both:

1. workflow execution;
2. participating services.

The workflow engine alone should not become the only source of operational truth.

## Correlation Identifiers

The process should maintain stable identifiers that allow events and service calls to be correlated.

Typical identifiers include:

• process instance ID;
• business operation ID;
• correlation ID;
• request ID;
• external operation ID.

Example:

```text
Process Instance
       |
       +── Service A
       |
       +── Service B
       |
       +── External System
       |
       +── Human Task
```

All related operations should be traceable through correlation identifiers.

## Process-Level Metrics

Important metrics may include:

• active process instances;
• completed processes;
• failed processes;
• cancelled processes;
• processes waiting for external events;
• processes waiting for human tasks;
• retry count;
• timeout count;
• compensation count;
• compensation failures;
• manual investigation count;
• process execution duration;
• human-task duration.

## State-Based Monitoring

Monitoring should allow operators to identify processes by state.

Example:

```text
CREATED
RUNNING
WAITING
FAILED
COMPENSATING
MANUAL_REVIEW
COMPLETED
```

A large number of processes in WAITING may indicate:

• external dependency problems;
• missing events;
• delayed callbacks;
• business bottlenecks.

A growing number of MANUAL_REVIEW processes may indicate:

• insufficient automation;
• recurring integration failures;
• unclear business rules;
• reliability problems.

## Failure Observability

For failed processes, useful information includes:

• process instance ID;
• current state;
• failed step;
• service name;
• error category;
• error code;
• retry attempt;
• timestamp;
• correlation ID;
• external operation ID;
• compensation status.

## Retry Observability

Retries should be visible.

Example:

```text
Process: 12345
Step: Create Payment
Attempt: 3
Error: TIMEOUT
Next Retry: 30 seconds
```

This allows operators to distinguish between:

```text
One temporary failure
```

and:

```text
Continuous repeated failure
```

## Timeout Monitoring

Timeouts should be measured separately from other failures.

Important metrics may include:

• timeout count;
• timeout rate;
• timeout by service;
• timeout by operation;
• average timeout duration;
• unresolved timeout count.

This helps identify dependencies that are slow or unreliable.

## Compensation Monitoring

Compensation should have its own observability.

Useful metrics:

• compensation attempts;
• successful compensations;
• failed compensations;
• compensation duration;
• manual compensation cases.

## Human Task Monitoring

Human tasks should expose:

• active tasks;
• overdue tasks;
• average completion time;
• escalation count;
• rejected tasks;
• reassigned tasks;
• manual investigation duration.

## Process Tracing

Distributed tracing can connect:

```text
Client
  ↓
Workflow
  ↓
Service A
  ↓
Service B
  ↓
External System
```

The trace should preserve correlation across synchronous and asynchronous boundaries where technically possible.

## Audit vs Observability

Audit and observability serve different purposes.

Audit

Answers:

> What happened and who performed the action?

Observability

Answers:

> What is happening now and why?

Both may be required.

## Operational Investigation

A support engineer should be able to start with a business operation ID and reconstruct:

```text
Business Operation
       ↓
Process Instance
       ↓
Current State
       ↓
Executed Steps
       ↓
Service Calls
       ↓
External Operations
       ↓
Retries / Errors
       ↓
Compensation
       ↓
Final Result
```

## Observability Principles

• Every long-running process requires process-level observability.
• Correlation identifiers should connect workflow and services.
• Process states should be measurable.
• Failures, retries and timeouts should be distinguishable.
• Compensation should be observable.
• Human tasks should be observable.
• Operational investigation should not depend exclusively on workflow-engine internals.
• Audit and observability should be treated as complementary capabilities.
