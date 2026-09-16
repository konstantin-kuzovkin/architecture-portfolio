# API Design

## API as a Contract

An API should be treated as an explicit contract between independently developed components.

The contract defines:

• endpoint;
• HTTP method;
• request;
• response;
• validation;
• errors;
• authentication;
• authorisation;
• idempotency;
• versioning.

## Example

A simplified transfer API:

```http
POST /transfers
```

Request:

```json
{
  "operationId": "operation-id",
  "source": "source-account",
  "destination": "destination-account",
  "amount": 100.00
}
```

Response:

```json
{
  "operationId": "operation-id",
  "status": "PROCESSING"
}
```

The example is intentionally generic and contains no real banking data.

## HTTP Semantics

HTTP status codes should represent the result of the API interaction rather than expose arbitrary downstream codes.

For example:

|HTTP Status|Meaning                       |
|-----------|------------------------------|
|200        |Successful request            |
|201        |Resource created              |
|400        |Invalid request               |
|401        |Authentication required       |
|403        |Access denied                 |
|404        |Resource not found            |
|409        |Business conflict             |
|422        |Business validation failure   |
|500        |Internal technical error      |
|502        |Downstream integration failure|
|504        |Downstream timeout            |

The exact mapping should be defined consistently across the platform.

## Business Errors

Business errors should contain stable machine-readable codes.

Example:

```json
{
  "code": "INVALID_STATE_TRANSITION",
  "message": "Operation cannot be moved to the requested state",
  "correlationId": "correlation-id"
}
```

The human-readable message should help the operator or client understand the problem.

## Idempotency

For operations that may be retried, an idempotency mechanism should be explicitly defined.

Possible approaches include:

• idempotency key;
• operation ID;
• request ID combined with durable operation state.

## Versioning

API evolution should avoid unexpected breaking changes.

Possible strategies:

• URI versioning;
• header versioning;
• media-type versioning.

The platform should select and consistently apply one strategy where possible.

## OpenAPI

The API contract should be maintained in OpenAPI.

The specification can describe:

• paths;
• parameters;
• schemas;
• responses;
• security schemes;
• examples;
• error models.

This allows the API contract to become a machine-readable artefact rather than remaining only in documentation.
