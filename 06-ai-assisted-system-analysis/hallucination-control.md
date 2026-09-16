# Hallucination Control

## Problem

LLMs are capable of producing technically plausible information that is not supported by source artefacts.

For system analysis this is particularly dangerous.

An invented endpoint, Kafka topic or database field can be mistaken for an actual architecture decision.

## Control Model

The solution applies several layers of control.

```text
Source Artefacts
       ↓
Controlled Tool Access
       ↓
Evidence Extraction
       ↓
Specialist Analysis
       ↓
Fact / Inference / Unknown
       ↓
Validation
       ↓
Final Output
```

## Rule 1 — Source Grounding

Technical facts should originate from available source artefacts.

## Rule 2 — Explicit Unknown

If required information is unavailable:

```text
UNKNOWN
```

must be returned instead of a fabricated value.

## Rule 3 — Assumptions

If an assumption is necessary for analysis, it must be explicitly labelled.

Example:

```text
ASSUMPTION:
The operation is expected to be idempotent.

EVIDENCE:
No explicit idempotency requirement was found.
```

The assumption must not become a fact.

## Rule 4 — Conflict Detection

If two artefacts contain different information, the agent should report the conflict.

## Rule 5 — Structured Output

Separating facts from recommendations reduces the probability that generated design proposals will be interpreted as existing architecture.

## Rule 6 — Validation

Generated artefacts should be validated where possible.

Examples:

• OpenAPI syntax;
• JSON schema;
• required fields;
• allowed enum values;
• document structure.

## Principle

The objective is not to eliminate every possible hallucination.

The objective is to make unsupported information visible and prevent it from silently becoming architectural truth.
