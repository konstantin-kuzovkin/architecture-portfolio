# Operation State Model

The authoritative lifecycle is defined in [state-machine.md](../state-machine.md). This file does not repeat it, to avoid two versions of the truth.

## Client-facing status

Clients never see internal states. The API returns only three statuses.

| Internal state | Client status | Note |
|---|---|---|
| NEW | PENDING | |
| FUNDS_RESERVED | PENDING | |
| SUBMITTED | PENDING | |
| UNKNOWN | PENDING | The client is not told about internal uncertainty. |
| MANUAL_INVESTIGATION | PENDING | Support can see the internal state in the operations console. |
| COMPLETED | COMPLETED | |
| FAILED | FAILED | `statusReason` explains why. |

The mapping is done by the Transfer Service. The client status is never used as a source of truth.
