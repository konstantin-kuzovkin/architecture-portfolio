# AI-Assisted System Analysis Agent

## Overview

This case study demonstrates the architecture of an AI-assisted system analysis agent designed to support software analysts during requirements analysis, architecture analysis and technical documentation.

The solution combines an LLM with controlled tools, specialised analytical agents, structured prompts and validation rules.

The primary design principle is:

> **The agent must distinguish between facts obtained from source artefacts and its own analytical conclusions.**

## Problem

System analysts regularly perform repetitive activities:

• reading large technical documents;
• analysing API specifications;
• reviewing Kafka contracts;
• extracting business rules;
• identifying missing requirements;
• analysing dependencies;
• preparing technical documentation;
• checking consistency between artefacts.

These activities consume significant analyst time while often following repeatable patterns.

## Objective

Create an AI-assisted workflow that can:

1. inspect source artefacts;
2. identify relevant information;
3. delegate specialised analysis;
4. produce structured results;
5. explicitly identify missing information;
6. avoid inventing technical facts;
7. generate reusable documentation.

## Core Principle

> **The agent is a detective, not an architect.**

The agent must analyse available evidence and identify what is explicitly present in the source material.

It must not invent:

• APIs;
• endpoints;
• Kafka topics;
• database tables;
• BPMN processes;
• business rules;
• metrics;
• configuration values;
• architecture components.

When information cannot be established from the available sources, the agent must explicitly report:

```text
НЕ НАЙДЕНО
```

or

```text
ОТСУТСТВУЕТ
```

The system distinguishes between:

• FACT — explicitly supported by source material;
• INFERENCE — reasoned interpretation;
• UNKNOWN — cannot be established from available evidence.

This separation is a core hallucination-control mechanism.

## High-Level Architecture

```text
                    ┌────────────────────┐
                    │      Analyst       │
                    └─────────┬──────────┘
                              │
                              ▼
                    ┌────────────────────┐
                    │    AI Agent        │
                    │  Orchestrator      │
                    └─────────┬──────────┘
                              │
             ┌────────────────┼────────────────┐
             ▼                ▼                ▼
        ┌─────────┐      ┌─────────┐      ┌─────────┐
        │ Files   │      │  Web    │      │Command  │
        │ Tool    │      │ Search  │      │ Tool    │
        └─────────┘      └─────────┘      └─────────┘
                              │
                              ▼
                    ┌────────────────────┐
                    │ Specialised        │
                    │ Subagents          │
                    └─────────┬──────────┘
                              │
       ┌──────────────┬───────┼────────┬──────────────┐
       ▼              ▼       ▼        ▼              ▼
      API           BPMN     Kafka    Data          Metrics
    Specialist    Specialist Specialist Specialist Specialist
       │              │       │        │              │
       └──────────────┴───────┼────────┴──────────────┘
                              ▼
                    ┌────────────────────┐
                    │ Validation Layer   │
                    └─────────┬──────────┘
                              ▼
                    ┌────────────────────┐
                    │ Structured Output  │
                    └────────────────────┘
```

## Tools

The agent can operate through controlled tools such as:

• file listing;
• file reading;
• file writing;
• command execution;
• web search;
• URL retrieval.

Tools are invoked explicitly rather than allowing the LLM to assume access to unavailable information.

## Specialised Subagents

Different analytical domains are delegated to specialised components:

• API specialist;
• BPMN specialist;
• Kafka specialist;
• data specialist;
• metrics specialist;
• Java specialist.

This allows domain-specific instructions and validation rules to be isolated.

## Typical Workflow

```text
Source Artefacts
      ↓
Source Inspection
      ↓
Relevant Information Extraction
      ↓
Task Classification
      ↓
Specialist Analysis
      ↓
Cross-check
      ↓
Validation
      ↓
Structured Result
      ↓
Documentation
```

## Example Tasks

The agent can assist with:

Requirements

• extract requirements;
• identify ambiguity;
• detect missing acceptance criteria;
• classify functional/non-functional requirements.

API

• analyse OpenAPI;
• identify endpoints;
• inspect request/response models;
• identify error contracts;
• generate API documentation.

Kafka

• analyse topics;
• identify producers and consumers;
• inspect event schemas;
• identify partitioning requirements;
• document delivery semantics.

BPMN

• analyse process flows;
• identify states and transitions;
• detect missing branches;
• produce structured process documentation.

Database

• analyse schemas;
• identify entities and relationships;
• document ownership;
• identify retention considerations.

## Hallucination Control

The architecture explicitly separates:

```text
FACT
  ↓
Source-backed information

INFERENCE
  ↓
Analytical conclusion

UNKNOWN
  ↓
Information not available
```

The agent must not silently convert UNKNOWN into FACT.

## Output

The preferred output is structured.

Example:

```json
{
  "facts": [],
  "assumptions": [],
  "openQuestions": [],
  "risks": [],
  "recommendations": []
}
```

This makes the result easier to validate and consume programmatically.

## Security

The agent must not expose confidential information unnecessarily.

Production implementations should consider:

• access control;
• data classification;
• audit;
• prompt injection protection;
• tool permissions;
• secret handling;
• logging policy.

## Limitations

The agent does not replace architectural ownership or business decision-making.

LLM output must be treated as an analytical aid and validated against source artefacts.

## Personal Contribution

The case reflects my experience designing AI-assisted analytical workflows and adapting LLM-based tooling to system analysis tasks.

Key areas include:

• agent architecture;
• system prompts;
• tool design;
• specialised subagents;
• structured output;
• hallucination prevention;
• analytical workflow design;
• technical documentation automation.

## My Role

Role: System Analyst / AI-assisted Engineering Designer

Responsibilities

• AI-assisted analysis workflow design;
• agent architecture;
• tool orchestration;
• specialist agent decomposition;
• system prompt design;
• structured output design;
• source-grounded analysis;
• hallucination control;
• validation workflow;
• analysis result verification;
• documentation generation;
• definition of AI limitations and boundaries.

## Portfolio Note

This is a reconstructed and sanitised portfolio case based on an AI-assisted system analysis approach.

The case demonstrates architecture and engineering principles rather than exposing proprietary implementation details, credentials, internal data or confidential documentation.
