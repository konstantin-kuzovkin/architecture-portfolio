Schema Evolution

Problem

Events are long-lived contracts between independently deployed producers and consumers.

A producer may evolve faster than its consumers.

A breaking schema change can therefore disrupt otherwise healthy services.

────────

Compatibility Principles

Prefer additive changes.

Example:

```text id="plmby5"
Version 1

{
  eventId,
  operationId,
  status
}
```

Later:

```text id="3c7oar"
Version 2

{
  eventId,
  operationId,
  status,
  metadata
}
```

Adding optional information is generally less disruptive than removing or changing the meaning of existing fields.

────────

Breaking Changes

Potential breaking changes include:

• removing a required field;
• changing field semantics;
• changing field type incompatibly;
• renaming required fields;
• changing enum meaning.

Such changes require explicit migration planning.

────────

Versioning

Event versioning may be represented by:

```text
eventVersion
```

or by another agreed contract-versioning mechanism.

The chosen strategy should be consistent across the platform.

────────

Consumer Compatibility

Before publishing a breaking change:

1. identify consumers;
2. assess compatibility;
3. introduce migration path;
4. deploy compatible consumers;
5. migrate producers;
6. remove obsolete contract only after consumers have migrated.

────────

Principle

Event schemas are integration contracts, not internal DTOs.
