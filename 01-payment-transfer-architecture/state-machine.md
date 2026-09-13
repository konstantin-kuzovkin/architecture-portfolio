State Machine

Purpose

The operation state machine defines the only valid lifecycle transitions for a transfer operation.

The state machine prevents arbitrary status changes and provides deterministic behaviour for both automated and manual processing.

⸻

States

|State               |Type        |Description                                                   |
|--------------------|------------|--------------------------------------------------------------|
|NEW                 |Initial     |Operation has been created but processing has not started     |
|PROCESSING          |Intermediate|Transfer processing is in progress                            |
|COMPLETED           |Terminal    |Successful processing confirmed                               |
|FAILED              |Terminal    |Processing failed and the operation is considered unsuccessful|
|UNKNOWN             |Recovery    |Final outcome cannot currently be determined                  |
|MANUAL_INVESTIGATION|Operational |Operation requires controlled investigation                   |

⸻

Transition Rules

|Current State       |Event                     |Next State          |Allowed|
|--------------------|--------------------------|--------------------|-------|
|NEW                 |Start processing          |PROCESSING          |Yes    |
|PROCESSING          |Successful result         |COMPLETED           |Yes    |
|PROCESSING          |Business failure          |FAILED              |Yes    |
|PROCESSING          |Timeout / unknown result  |UNKNOWN             |Yes    |
|UNKNOWN             |External success confirmed|COMPLETED           |Yes    |
|UNKNOWN             |External failure confirmed|FAILED              |Yes    |
|UNKNOWN             |Investigation required    |MANUAL_INVESTIGATION|Yes    |
|MANUAL_INVESTIGATION|Success confirmed         |COMPLETED           |Yes    |
|MANUAL_INVESTIGATION|Failure confirmed         |FAILED              |Yes    |
⸻

Invalid Transitions

Examples:

COMPLETED → PROCESSING
COMPLETED → FAILED
FAILED → PROCESSING
FAILED → COMPLETED
NEW → COMPLETED
NEW → FAILED

These transitions must be rejected.

The service should return a deterministic business error indicating that the requested state transition is not allowed.

⸻

Transition Atomicity

A state transition must be performed atomically with the corresponding persistence operation.

Conceptually:

BEGIN TRANSACTION
    Load operation
    Validate current state
    Validate requested transition
    Update operation state
    Persist transition metadata
COMMIT

If any required operation fails, the transaction must not leave a partially applied state transition.

⸻

Concurrency

Two concurrent transition attempts for the same operation must not both succeed when they conflict with the state machine.

Example:

                 Operation = UNKNOWN
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
       Transaction A       Transaction B
       → COMPLETED         → FAILED
             │                   │
             └─────────┬─────────┘
                       ▼
                DB concurrency
                   control

Only one valid transition may commit according to the transaction and locking strategy.

⸻

Principle

The state machine is the authoritative definition of allowed operation lifecycle transitions.

The client application may use different user-facing labels, but it must not redefine the underlying lifecycle.
