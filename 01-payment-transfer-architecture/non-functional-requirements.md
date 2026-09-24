# Non-Functional Requirements

> These are **reference values** for this case study. They are assumptions, not measurements of a real system. In a real project each value comes from business owners, regulation and load tests.

| ID | Requirement | Reference value | Reason |
|---|---|---|---|
| NFR-01 | Accept latency (`POST /transfers`, store and return 202) | p95 <= 300 ms | Client should not wait for external systems. |
| NFR-02 | Time to a terminal state, normal path | p95 <= 5 s | Hold + network call + capture. |
| NFR-03 | Timeout of one network call | 10 s | Longer waits block workers. |
| NFR-04 | Automatic re-send after a network timeout | Never | Prevents duplicate payments. Use status inquiry. |
| NFR-05 | Status inquiry policy | Every 5 min, max 24 attempts (2 hours) | Then `MANUAL_INVESTIGATION`. |
| NFR-06 | Idempotency key retention | >= 7 days | Covers client retries and weekends. |
| NFR-07 | Availability of the accept API | 99.95% per month | About 22 minutes of downtime per month. |
| NFR-08 | State durability | RPO = 0 (synchronous replica), RTO <= 15 min | Operation state must not be lost. |
| NFR-09 | Capacity (assumption) | 50 TPS average, 200 TPS peak, design for 2x headroom | Sizing input for a real project. |
| NFR-10 | Hold expiry in ABS | 72 hours | Safety net. Must be longer than the manual investigation SLA. |
| NFR-11 | Manual investigation SLA | 1 business day, four-eyes approval | Operational control. |
| NFR-12 | Retry of capture and release | Exponential backoff, alert after 10 min | A failed capture after network success is critical. |
| NFR-13 | Audit retention | 5 years (or as required by regulation) | Investigation and compliance. |

## Alert thresholds (reference)

| Signal | Threshold |
|---|---|
| Operations in `UNKNOWN` older than 15 minutes | > 0.5% of last hour operations |
| Operations in `MANUAL_INVESTIGATION` older than 8 hours | any |
| Capture retries older than 10 minutes | any |
| Outbox lag (see case 02) | p95 > 5 s |
| `INVALID_STATE_TRANSITION` errors | sudden growth |
