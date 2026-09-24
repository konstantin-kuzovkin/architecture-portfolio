# Workflow Technology Decision Matrix

> Example evaluation for the transfer process. Scores are illustrative and reflect the requirements of this case. In a real project they come from a proof of concept and the team's experience.

## Candidates

1. **BPMN engine (for example Camunda).** Process is a BPMN model; workers implement the tasks.
2. **Custom state machine in the service.** No engine; timers and retries are coded in the service.
3. **Code-first workflow engine (for example Temporal).** Process is code; the engine stores history.

## Criteria and weights

Weight 3 = high, 2 = medium. Scores: 1 poor, 5 excellent.

| Criterion | Weight | BPMN engine | Custom state machine | Code-first engine |
|---|---:|---:|---:|---:|
| Long-running process, durable state | 3 | 5 | 4 | 5 |
| Timers, retries, timeouts | 3 | 5 | 2 | 5 |
| Human tasks | 2 | 5 | 1 | 2 |
| Compensation | 3 | 4 | 3 | 5 |
| Operational visibility for analysts and support | 3 | 5 | 2 | 4 |
| Operating cost and complexity | 2 | 3 | 4 | 2 |
| Team skills and migration cost | 3 | 4 | 4 | 2 |
| **Weighted total (max 95)** | | **85** | **55** | **71** |

## Result

The BPMN engine scores highest for this case. The main reasons are human tasks, timers and process visibility for analysts and support. The result changes if the process has no human tasks, or if the team has no engine experience and the process is small. In that case a custom state machine (as in case 01) is a valid choice.

## Notes

- The engine does not own the operation state ([ADR-001](../adr/ADR-001-operation-state-ownership.md)).
- The choice of orchestration is governed by the rule in [ADR-004](../adr/ADR-004-workflow-orchestration-vs-choreography.md).
- Licensing, support and the vendor roadmap must be checked in a real project.

The preferred solution is the one that satisfies the required business and operational characteristics with acceptable complexity and risk.
