Integration Patterns

Pattern Selection

Integration technology should be selected according to the required interaction semantics.

The main decision factors are:

• response latency;
• synchronous versus asynchronous behaviour;
• consistency requirements;
• coupling;
• consumer availability;
• expected load;
• failure handling;
• replay requirements;
• contract complexity.

────────

REST

Use when

The caller needs a synchronous response.

Example:

```text
Client → Service → Response
```

Advantages

• simple request/response model;
• widely supported;
• clear HTTP semantics;
• suitable for interactive operations.

Risks

• temporal coupling;
• downstream availability affects the caller;
• retry may create duplicate operations.

────────

SOAP

Use when

Integration with an existing contract-heavy or legacy system requires SOAP.

Example:

```text
Modern Service
      │
      │ XML / SOAP
      ▼
Legacy System
```

Advantages

• explicit contract;
• mature enterprise integration model;
• compatibility with legacy platforms.

Risks

• XML complexity;
• tighter coupling to legacy contracts;
• more complex error handling.

────────

Kafka

Use when

The interaction can be asynchronous.

Example:

```text
Producer
   │
   ▼
Kafka
   │
   ├── Consumer A
   ├── Consumer B
   └── Consumer C
```

Advantages

• temporal decoupling;
• scalable consumers;
• multiple independent consumers;
• replay possibilities.

Risks

• eventual consistency;
• duplicate processing;
• ordering considerations;
• more complex operational model.

────────

Decision Matrix

|Requirement                   |REST   |SOAP    |Kafka|
|------------------------------|------:|-------:|----:|
|Immediate response            |✓      |✓       |—    |
|Legacy compatibility          |—      |✓       |—    |
|Async processing              |—      |possible|✓    |
|Multiple independent consumers|limited|limited |✓    |
|Strong synchronous coupling   |✓      |✓       |—    |
|Replayable event stream       |—      |—       |✓    |

────────

Principle

No integration technology is universally superior.

The architecture should optimise for the required business semantics rather than technology preference.
