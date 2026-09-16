# Microservice Standards

## Service Identity

Every service should have:

• unique service name;
• defined owner;
• business purpose;
• technical purpose;
• lifecycle status.

## API

Where a service exposes synchronous functionality, its API should define:

• endpoint;
• HTTP method;
• request;
• response;
• validation;
• errors;
• security;
• idempotency;
• versioning.

## Events

Where a service publishes or consumes events, documentation should define:

• topic purpose;
• event type;
• event schema;
• version;
• key;
• partitioning requirements;
• producer;
• consumers;
• delivery semantics;
• retry/DLQ behaviour.

## Database

Database documentation should define:

• ownership;
• logical purpose;
• major entities;
• relationships;
• retention;
• archival/cleanup;
• access patterns.

## Security

The service should document:

• authentication;
• authorisation;
• service-to-service security;
• external integration security;
• sensitive data.

## Observability

The service should provide:

• logs;
• metrics;
• correlation identifiers;
• health checks;
• relevant alerts.

## Documentation

Minimum documentation should include:

```text
README
Architecture
API
Events
Database
Security
Observability
```

The exact set may be adapted according to service complexity.

## Operational Readiness

Before production release, the team should be able to answer:

• Who owns the service?
• How is it monitored?
• What happens if a dependency fails?
• How is the service recovered?
• What are the critical integrations?
• Where is the technical documentation?
• What are the known limitations?
