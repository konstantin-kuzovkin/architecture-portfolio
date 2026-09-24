# Money Flow

## Purpose

This document explains what happens to the customer's money in every outcome. The state machine describes the operation. This document describes the funds.

## Principles

1. Core banking (ABS) owns balances and holds. The Transfer Service never calculates balances.
2. Amounts are exact: decimal strings in APIs and events, integer minor units in the database. Never floating point.
3. Every call to ABS and to the payment network carries `operationId` as an idempotency key. Repeating a call must not move money twice.
4. Funds are held first. They are captured (debited) only after the network confirms success. They are released if the payment fails.

## Money positions

```text
NEW --> hold --> [ FUNDS_RESERVED / SUBMITTED / UNKNOWN / MANUAL_INVESTIGATION ] --> capture  --> COMPLETED
                                                                                 \-> release  --> FAILED
```

## Outcomes

| Situation | Money position | Action |
|---|---|---|
| ABS declines the hold | Nothing moved | `NEW -> FAILED` |
| Network rejects the payment | Hold exists | Release the hold, then `SUBMITTED -> FAILED` |
| Network confirms success | Hold exists | Capture the hold, then `SUBMITTED -> COMPLETED` |
| Timeout or lost response | Hold exists | `SUBMITTED -> UNKNOWN`. Keep the hold. Ask the network for the status. |
| Status confirms success | Hold exists | Capture, then `UNKNOWN -> COMPLETED` |
| Status confirms failure | Hold exists | Release, then `UNKNOWN -> FAILED` |
| Capture fails after network success | Hold exists, money left the bank | Do not change the state. Retry capture with backoff. Alert after 10 minutes. This is a critical incident. |
| Release fails after rejection | Hold exists | Retry release with backoff. The hold expires automatically after 72 hours as a safety net. |
| Customer asks for a refund of a completed transfer | Debited | Create a **new** operation of type `RETURN` with `original_operation_id`. The original stays `COMPLETED`. |

## Why hold and capture instead of debit and reverse

| Option | Advantage | Disadvantage |
|---|---|---|
| Hold, then capture or release (chosen) | Failure does not need a reversal in the ledger. The customer's money is never "gone" during uncertainty. | ABS must support holds. Holds must expire safely. |
| Debit first, reverse on failure | Works with a simple ABS. | Every failure creates a reversal. During `UNKNOWN` the money is already debited, which creates more customer complaints and more accounting entries. |

If a real ABS does not support holds, the fallback is "debit first, reverse on failure". Then a `REVERSED` step is added to the failure path and to the accounting rules.

## Open questions for a real project

- Does the network guarantee that a payment that was not received will never be processed later? This decides whether `NOT_FOUND` from a status inquiry is a confirmed failure.
- What is the maximum lifetime of a hold in ABS?
- Which currency and rounding rules apply to cross-currency transfers?
