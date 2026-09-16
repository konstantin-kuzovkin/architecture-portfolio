# ADR-001: Use Orchestration for Transfer Processing

## Context

The transfer process involves multiple components with different responsibilities and processing semantics.

The operation requires controlled sequencing, explicit state management, error handling and reconciliation.

## Options

Option 1 — Orchestration

A dedicated Transfer Service coordinates the process.

```text
Transfer Service
   ├── Core Banking
   ├── Payment Network
   └── Audit
```

Option 2 — Choreography

Each component reacts to events and independently determines the next action.

```text
Service A → Event → Service B
              ↓
            Event
              ↓
          Service C
```

## Decision

Use orchestration for the core transfer lifecycle.

## Rationale

Orchestration provides:

• explicit process ownership;
• centralised state management;
• deterministic sequencing;
• easier failure handling;
• clear responsibility for reconciliation;
• simpler operational investigation.

The process has a business lifecycle that benefits from an explicit coordinator.

## Consequences

Positive

• Easier to understand process flow.
• Centralised lifecycle control.
• Clear operational ownership.
• Easier state-machine implementation.

Negative

• Transfer Service becomes an important architectural component.
• Additional coordination logic is required.
• Care must be taken to avoid turning the service into an oversized business monolith.

## Rejected Alternative

Pure choreography was not selected for the core transaction lifecycle because it would distribute process ownership across multiple components and make global lifecycle management and operational investigation more complex.
