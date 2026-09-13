Workflow Technology Decision Matrix

Evaluation Criteria

A workflow technology should be evaluated against the following criteria.

|Criterion               |Importance |
|------------------------|----------:|
|Long-running workflows  |High       |
|Durable state           |High       |
|Retry / timeout handling|High       |
|BPMN support            |High       |
|Human tasks             |Medium/High|
|Compensation            |High       |
|Event integration       |High       |
|Operational visibility  |High       |
|Scalability             |High       |
|High availability       |High       |
|Developer experience    |Medium     |
|Migration complexity    |High       |
|Team expertise          |High       |
|Extensibility           |Medium     |
|Operational cost        |Medium     |

────────

Evaluation Approach

Each candidate technology should be evaluated using the same criteria.

Example scoring model:

```text
1 — Poor
2 — Limited
3 — Acceptable
4 — Good
5 — Excellent
```

The final decision should not be based on one feature.

Instead:

```text
Technology Capability
        +
Architecture Fit
        +
Migration Cost
        +
Operational Model
        +
Team Capability
        =
Technology Decision
```

────────

Important Consideration

A technology with more features is not automatically a better architectural choice.

The preferred solution is the one that satisfies the required business and operational characteristics with acceptable complexity and risk.
