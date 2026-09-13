AI-Assisted Analysis Workflow

Step 1 — Understand the Task

The agent identifies:

• requested outcome;
• domain;
• required artefacts;
• expected output.

────────

Step 2 — Identify Sources

The agent determines which source artefacts may contain relevant information.

Example:

```text
API question
   ↓
OpenAPI
API documentation
Sequence diagrams
Architecture documentation
```

────────

Step 3 — Inspect Sources

The agent reads the relevant artefacts using available tools.

────────

Step 4 — Extract Facts

Information is extracted without interpretation where possible.

────────

Step 5 — Specialist Analysis

The task is delegated to an appropriate specialist.

────────

Step 6 — Cross-Check

Results from multiple sources or specialists are compared.

────────

Step 7 — Classify Information

Each significant conclusion is classified as:

• fact;
• inference;
• unknown.

────────

Step 8 — Validate

Generated output is checked for:

• unsupported technical facts;
• structural errors;
• contradictions;
• missing information.

────────

Step 9 — Generate Documentation

Only validated information is used for final documentation.

────────

Step 10 — Human Review

The analyst remains responsible for final acceptance of the result.
