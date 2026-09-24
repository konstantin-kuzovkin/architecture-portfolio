# Idempotency

## Problem

Clients, gateways and workers retry requests after timeouts. A retry can arrive after the first request was already accepted. Without idempotency the same transfer can be executed twice.

## Model

- The client sends an `Idempotency-Key` header. The key is unique per client and per logical operation.
- The service creates its own `operationId` and stores it together with the key.
- The service stores `request_hash` (SHA-256 of the canonical request body) to detect a reused key with different data.
- A unique constraint on `(client_id, idempotency_key)` protects against races. Two parallel first requests cannot both create an operation.
- Keys are kept for at least 7 days (NFR-06).

## Response rules

| Case | Response |
|---|---|
| New key | `202 Accepted`, operation is created in `NEW`. |
| Same key, same `request_hash`, operation exists | `200 OK` with the current client status. Header `Idempotency-Replayed: true`. |
| Same key, different `request_hash` | `422 Unprocessable Entity`, code `IDEMPOTENCY_KEY_REUSED`. |
| Same key, first request is still being stored | `409 Conflict`, code `REQUEST_IN_PROGRESS`. The client retries later. |
| Missing key | `400 Bad Request`. |

## Idempotency at every hop

| Hop | Idempotency key |
|---|---|
| Client to Transfer Service | `Idempotency-Key` header |
| Transfer Service to ABS (hold, capture, release) | `operationId` (hold key stored in `hold_id`) |
| Transfer Service to the payment network | `operationId` as the payment reference |
| Kafka consumers | `eventId` (see case 02) |

## Idempotency and the state machine

A retry never creates a second lifecycle and never bypasses the state machine. It only observes the current state.
