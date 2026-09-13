Structured Output Contract

Analysis Result

```json
{
  "facts": [],
  "inferences": [],
  "unknowns": [],
  "openQuestions": [],
  "risks": [],
  "recommendations": []
}
```

────────

Facts

Information directly supported by source artefacts.

────────

Inferences

Conclusions derived from available information.

────────

Unknowns

Required information that was not found.

────────

Open Questions

Questions requiring clarification from a system owner, product owner, architect or another stakeholder.

────────

Risks

Potential problems identified during analysis.

────────

Recommendations

Potential improvements proposed by the agent.

Recommendations must not be presented as existing system behaviour.

────────

Example

```json
{
  "facts": [
    "The service exposes a REST API."
  ],
  "inferences": [
    "The API is likely used for synchronous interaction."
  ],
  "unknowns": [
    "Authentication mechanism is not defined."
  ],
  "openQuestions": [
    "What authentication mechanism is required?"
  ],
  "risks": [
    "Authentication requirements may be discovered too late."
  ],
  "recommendations": [
    "Define authentication requirements before implementation."
  ]
}
```

This structure makes generated analysis easier to review.
