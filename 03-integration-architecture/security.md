# Integration Security

## Trust Boundaries

Every integration should explicitly identify its trust boundary.

Example:

```text
Client
  │
  │ authenticated request
  ▼
API Boundary
  │
  ▼
Internal Service
  │
  │ service authentication
  ▼
Downstream System
```

## JWT / OAuth / OIDC

Token-based authentication is appropriate when the system needs to establish the identity and permissions of the calling party.

The architecture should distinguish:

• authentication;
• authorisation;
• token validation;
• token lifetime;
• scopes / roles.

## mTLS

Mutual TLS provides authentication at the transport layer.

Both parties present certificates and verify the identity of the other side.

Typical use cases include integrations where strong service identity is required.

## Certificate Management

A production architecture must consider:

• certificate issuance;
• expiration;
• rotation;
• trust chain;
• revocation;
• secure storage.

Certificate expiration should be observable before it becomes an outage.

## Authorisation

An authenticated caller is not automatically authorised to perform every operation.

Authorisation should consider:

• operation;
• resource;
• role;
• scope;
• service identity.

## Security and Integration Errors

The architecture should distinguish:

```text
401 → authentication problem

403 → authorisation problem

4xx → client/business validation

5xx → server/integration problem
```

The exact API mapping should follow the platform’s error standard.
