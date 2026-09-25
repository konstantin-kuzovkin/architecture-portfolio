# Evaluation of the Analysis Agent

> Only measured numbers are published here. If something was not measured, it says "not measured".

## Goal

Measure whether the agent's controls (source grounding, FACT / INFERENCE / UNKNOWN, validation) really reduce invented facts, compared with a plain LLM prompt.

## Test set

- 30 tasks: 5 each for requirements, OpenAPI, Kafka/AsyncAPI, BPMN, database schema, cross-artifact consistency.
- Source artifacts: the public artifacts of this repository (OpenAPI, AsyncAPI, BPMN, state machine). No confidential data.
- For each task: a list of expected facts (gold answer) and 1–2 **traps**: information that is intentionally absent, so the correct answer is UNKNOWN.
- Seeded defects to find: for example, a status that exists in one artifact but not in another.

## Metrics

| Metric | Definition |
|---|---|
| Fact precision | Statements marked FACT that are supported by a source / all statements marked FACT |
| Invented-fact rate | Statements marked FACT that are not in any source / all statements marked FACT |
| UNKNOWN recall | Traps answered with UNKNOWN / all traps |
| Completeness | Gold facts found / all gold facts |
| Defects found | Seeded defects found / seeded defects |
| Analyst time | Minutes to finish a task with and without the agent (same analyst, same tasks) |

## Results

| Metric | Plain LLM prompt | Agent with controls |
|---|---|---|
| Fact precision | not measured | not measured |
| Invented-fact rate | not measured | not measured |
| UNKNOWN recall | not measured | not measured |
| Completeness | not measured | not measured |
| Defects found | not measured | not measured |
| Analyst time (minutes per task) | not measured | not measured |

This evaluation is designed but not yet run. Running it against 10–30 tasks is a half-day task (see "How to run it" below); the plan itself already demonstrates the evaluation method.

Limitations: one analyst, a small test set, the same person wrote the test set and the agent. Numbers show a trend, not a proof.

## Security controls status

Fill in honestly. "Planned" is a valid answer.

| Control | Status | Evidence |
|---|---|---|
| Access control for source artifacts and tools | Planned | Tool access is currently limited by what the operator explicitly runs; no formal allowlist yet |
| Data classification before sending content to the LLM | Planned | Currently manual: only public/sanitised artifacts are used as input |
| Prompt injection protection (external text is untrusted) | Partially implemented | Source documents are treated as data, not instructions, by convention; not yet enforced by tooling |
| Allowlist of commands and URLs | Planned | |
| Audit log of tool calls | Planned | |
| Retention and deletion of analysis results | Planned | |

## How to run it (about half a day)

1. Write the 30 tasks and gold answers (start with 10 if time is short).
2. Run each task with a plain prompt and with the agent. Save the outputs.
3. Score the outputs against the gold answers.
4. Fill in the table above. Keep the raw outputs in an `evaluation-data/` folder if they contain no confidential data.
