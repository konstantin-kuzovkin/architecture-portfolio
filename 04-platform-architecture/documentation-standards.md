# Documentation Standards

## Documentation Structure

A service documentation set may contain:

```text
README
│
├── Architecture
├── API
├── BPMN
├── Kafka
├── Database
├── Security
├── Metrics
└── Operations
```

## README

The README should answer:

• What does the service do?
• Who owns it?
• How does it integrate?
• Where is the detailed documentation?
• What are the main operational characteristics?

## Architecture

Should describe:

• components;
• responsibilities;
• dependencies;
• integration patterns;
• important decisions.

## API

Should contain the OpenAPI contract and relevant usage information.

## Kafka

Should describe:

• topics;
• producers;
• consumers;
• events;
• schemas;
• partitioning;
• delivery semantics.

## Database

Should describe:

• entities;
• ownership;
• relationships;
• important constraints;
• retention/archival.

## Security

Should describe:

• authentication;
• authorisation;
• certificates;
• trust boundaries;
• sensitive information.

## Metrics

Should describe:

• key metrics;
• dashboards;
• alerts;
• SLO-related indicators where applicable.

## Documentation Quality

Documentation should be:

• discoverable;
• current;
• consistent;
• versioned;
• linked to the relevant service;
• understandable without tribal knowledge.
