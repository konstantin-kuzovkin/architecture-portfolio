# Failure Scenarios

## Purpose

This document lists the main failures, the resulting state, what happens to the money and what the system does. States are defined in [state-machine.md](./state-machine.md). Money rules are in [money-flow.md](./money-flow.md).

## Failure matrix

| # | Scenario | State after | Money | Action |
|---|---|---|---|---|
| 1 | Client times out before the response | Unchanged | None or hold | Client retries with the same `Idempotency-Key` and gets the current state. |
| 2 | Duplicate client request | Unchanged | Unchanged | `200` replay. No second operation. |
| 3 | Same key, different body | Unchanged | Unchanged | `422 IDEMPOTENCY_KEY_REUSED`. |
| 4 | ABS unavailable at hold | `NEW` | None | Retry with backoff; fail after the retry budget. |
| 5 | ABS declines the hold | `FAILED` | None | Return `statusReason`. |
| 6 | Crash after the hold, before sending | `FUNDS_RESERVED` | Hold | Recovery job continues to `SUBMITTED` and sends. |
| 7 | Network rejects the payment | `FAILED` | Hold released | Release with retry. |
| 8 | Network timeout | `UNKNOWN` | Hold | Status inquiry every 5 min. **No re-send.** |
| 9 | Network accepted, response lost | `UNKNOWN` -> `COMPLETED` | Hold, then captured | Status inquiry finds success. |
| 10 | Crash after `SUBMITTED`, before the call | `UNKNOWN` | Hold | Treated as unknown. Status inquiry decides. |
| 11 | Network success but ABS unavailable at capture | `SUBMITTED` (`network_result = SUCCESS`) | Hold, money left the bank | Retry capture; alert after 10 min. Critical. |
| 12 | Release fails after rejection | `SUBMITTED` | Hold | Retry release; the hold expires after 72 h. |
| 13 | Duplicate network response or event | Unchanged | Unchanged | Processed idempotently; transition already applied. |
| 14 | Concurrent transitions | One wins | Unchanged | Optimistic lock; the loser reloads and gets a business error. |
| 15 | Database failure during a transition | Previous state | Unchanged | Transaction rolls back; the worker retries. |
| 16 | Status inquiry keeps returning unknown | `MANUAL_INVESTIGATION` after 2 h | Hold | Operator decides with evidence; four-eyes approval. |
| 17 | Audit service unavailable | Unchanged | Unchanged | Audit events go through the outbox; the transition is not blocked. |
| 18 | Invalid manual transition | Unchanged | Unchanged | Rejected with `INVALID_STATE_TRANSITION`. |

## Critical scenario: lost response

```text
Transfer Service --submit--> Payment Network --accepted--> (response lost)
Transfer Service: SUBMITTED -> UNKNOWN (hold stays)
Reconciliation: asks the network for the status -> SUCCESS -> capture -> COMPLETED
```

A missing response is not evidence of failure. Sending the payment again could pay twice.

## Design principle

Classify a failure before choosing the next state. A confirmed rejection, a technical timeout and an unknown outcome are different situations and must not be collapsed into one `FAILED` state.
