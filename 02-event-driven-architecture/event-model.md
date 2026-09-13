Event Model

Event as a Business Fact

An event represents something that has already happened.

Examples:

```text
TransferCreated
TransferCompleted
PaymentRejected
CustomerUpdated
DocumentSigned
```

An event should not be treated as a remote command disguised as an event.

────────

Event Envelope

A common event envelope may contain:

|Field        |Purpose                       |
|-------------|------------------------------|
|eventId      |Unique event identity         |
|eventType    |Business event type           |
|eventVersion |Contract version              |
|occurredAt   |Event creation timestamp      |
|operationId  |Business operation correlation|
|correlationId|End-to-end correlation        |
|traceId      |Distributed tracing           |
|payload      |Business data                 |

────────

Event ID

The event ID must uniquely identify a published event.

Consumers can use it as one of the inputs for duplicate detection.

────────

Partition Key

The partition key should be selected according to business ordering requirements.

For example:

```text id="w5f1zn"
operation_id
```

can ensure that events for the same operation are routed to the same partition.

────────

Consumer Responsibility

Consumers should:

• validate the event contract;
• validate event version;
• process the business event;
• handle duplicates;
• record processing outcome;
• commit offsets only according to the processing strategy.

────────

Producer Responsibility

Producers should:

• publish valid events;
• maintain contract compatibility;
• provide stable event identity;
• provide sufficient correlation metadata;
• avoid leaking internal implementation details into the event contract.
