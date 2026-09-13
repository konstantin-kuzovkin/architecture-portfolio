Security

Authentication

All client and service interactions must be authenticated.

Depending on the integration boundary, the architecture may use:

• OAuth/OIDC-based authentication;
• JWT;
• mutual TLS;
• certificate-based authentication.

────────

Authorisation

Authentication alone is not sufficient.

The Transfer Service must verify whether the caller is authorised to:

• initiate an operation;
• query operation state;
• perform operational actions;
• initiate manual reconciliation.

────────

Operational Access

Manual investigation is a privileged operation.

The Operations Console must enforce:

• authenticated operator identity;
• role-based access control;
• least privilege;
• audit logging;
• controlled state-changing operations.

────────

State Modification

Operators must not receive unrestricted access to the underlying operation database.

State changes should be performed through a controlled application interface that validates the state transition.

This prevents accidental or unauthorised modification of financial operation states.

────────

Transport Security

Communication between trusted components should use encrypted transport.

For external integrations requiring stronger mutual authentication, mutual TLS may be used.

────────

Sensitive Data

The portfolio architecture intentionally avoids:

• customer data;
• account numbers;
• credentials;
• production certificates;
• tokens;
• internal network addresses;
• proprietary endpoint names.

────────

Security Principle

Security controls must protect not only access to the system but also the integrity of the operation lifecycle.

An authenticated user must still be prevented from performing an invalid state transition.
