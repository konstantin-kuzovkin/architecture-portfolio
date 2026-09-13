Agent Tools

File Tools

list_files

Used to discover available source artefacts.

read_file

Used to inspect the contents of source artefacts.

write_file

Used to generate structured documentation.

────────

Execution Tool

run_command

Used for controlled technical operations such as:

• validation;
• transformation;
• generation;
• automated checks.

The agent must not claim that a command succeeded unless execution actually returned a successful result.

────────

Web Tools

search_web

Used when external information is explicitly required.

fetch_url

Used to retrieve a known external source.

────────

Tool Security

Tools should follow the principle of least privilege.

An agent should only receive the permissions required for the current task.

────────

Tool Failure

If a tool fails:

```text
Tool failure
    ↓
Report failure
    ↓
Do not fabricate result
```

────────

Principle

Tools extend the agent’s capabilities but do not automatically make their results authoritative.
