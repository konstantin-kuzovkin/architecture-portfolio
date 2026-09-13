Observability

Objective

The operation must be traceable across the distributed components involved in processing.

Observability should allow engineers and operations teams to answer:

> What happened to this operation?

> Where did processing stop?

> Which system provided the final result?

> How long has the operation remained unresolved?

────────

Correlation

The operation should be associated with:

• operation ID;
• correlation ID;
• request ID;
• trace ID where distributed tracing is available.

────────

Recommended Log Fields

```text
operation_id
correlation_id
event
current_state
previous_state
target_state
component
downstream_system
timestamp
error_code
error_category
retry_number
```

────────

Metrics

Processing

• total operations;
• successful operations;
• failed operations;
• processing latency;
• throughput.

Reliability

• timeout rate;
• downstream error rate;
• retry count;
• duplicate request rate.

Reconciliation

• UNKNOWN operations;
• reconciliation backlog;
• reconciliation success rate;
• reconciliation latency;
• manual investigation count.

────────

Alerts

Potential alerts include:

```text
UNKNOWN operations > threshold

Reconciliation backlog increasing

External timeout rate > threshold

Processing latency > threshold

Duplicate request rate anomaly

Manual investigation backlog > threshold
```

────────

Distributed Tracing

A distributed trace should allow the operation to be followed through:

```text
Client
  ↓
Transfer Service
  ↓
Core Banking
  ↓
Payment Network
  ↓
Transfer Service
  ↓
Audit
```

The trace must not expose sensitive financial information.

────────

Operational Dashboard

A useful operational dashboard could contain:

|Metric                      |Purpose                    |
|----------------------------|---------------------------|
|Processing volume           |Overall traffic            |
|Success rate                |Business outcome           |
|Failure rate                |Business/technical problems|
|Unknown count               |Unresolved operations      |
|Reconciliation backlog      |Recovery health            |
|Processing latency          |Performance                |
|External timeout rate       |Dependency health          |
|Manual investigation backlog|Operational risk           |

────────

Principle

Observability is part of the architecture rather than an afterthought.

For distributed financial processing, the ability to reconstruct the lifecycle of an individual operation is a functional operational requirement.
