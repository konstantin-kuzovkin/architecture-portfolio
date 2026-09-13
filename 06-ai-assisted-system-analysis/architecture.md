Agent Architecture

Architectural Components

The solution consists of several logical components:

1. User interface
2. Agent orchestrator
3. LLM
4. Tool layer
5. Specialist agents
6. Validation layer
7. Output formatter

────────

Agent Orchestrator

The orchestrator is responsible for:

• understanding the analyst’s task;
• determining required source artefacts;
• selecting appropriate tools;
• delegating specialised analysis;
• combining results;
• invoking validation;
• producing the final response.

────────

Tool Layer

Tools provide controlled access to external information.

Examples:

```text
list_files
read_file
write_file
run_command
search_web
fetch_url
```

The LLM should not assume that information exists simply because it would be useful.

────────

Specialist Agents

The orchestrator delegates domain-specific tasks.

```text
                     Orchestrator
                          │
          ┌───────────────┼────────────────┐
          ▼               ▼                ▼
       API Agent       Kafka Agent      BPMN Agent
          │               │                │
          ▼               ▼                ▼
       API facts       Kafka facts       BPMN facts
          │               │                │
          └───────────────┼────────────────┘
                          ▼
                      Validator
```

────────

Separation of Responsibilities

The orchestrator should not contain every domain-specific rule.

Instead:

```text
Orchestration
     ≠
Domain analysis
     ≠
Validation
     ≠
Presentation
```

This separation makes the system easier to evolve.

────────

Source of Truth

The system should define an explicit hierarchy:

```text
Authoritative Source
        ↓
Extracted Fact
        ↓
Analytical Interpretation
        ↓
Recommendation
```

A recommendation must not be represented as an authoritative technical fact.

────────

Failure Handling

The agent should explicitly handle:

• missing files;
• unreadable files;
• incomplete specifications;
• conflicting artefacts;
• unavailable tools;
• invalid generated output;
• unsupported tasks.

────────

Principle

The agent should fail transparently rather than silently fabricate missing information.
