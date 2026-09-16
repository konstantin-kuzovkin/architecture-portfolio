# ADR-001: Integration Pattern Selection

## Context

Different business interactions require different communication semantics.

A single integration technology should not be used for every interaction.

## Decision

Use:

• REST for synchronous request/response;
• SOAP where legacy contract compatibility is required;
• Kafka for asynchronous event-driven interactions.

## Rationale

The selection is based on:

• response requirements;
• temporal coupling;
• downstream availability;
• contract characteristics;
• scalability;
• replay requirements;
• failure semantics.

## Consequences

The platform must support multiple integration patterns and provide consistent standards for each.

This increases technical diversity but better matches business requirements.

## Principle

Technology selection follows interaction semantics rather than technology preference.
