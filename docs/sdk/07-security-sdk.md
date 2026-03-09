# Plumbus Security SDK Specification

## Purpose

This document defines the **Security SDK**, the programming interface
used to declare and enforce authorization policies within Plumbus
applications.

Security in Plumbus is **capability-centric**. Instead of spreading
authorization logic across controllers, services, and middleware, access
rules are declared directly in capability contracts.

The Security SDK defines:

-   capability access declarations
-   role and scope models
-   tenant scoping
-   contextual authorization
-   field-level restrictions
-   authorization lifecycle
-   security helpers
-   governance integration

The goal is to make security **explicit, analyzable, and auditable**.

------------------------------------------------------------------------

## Conceptual Model

Security in Plumbus follows a **deny-by-default model**.

Every capability must declare who is allowed to execute it.

Example:

``` ts
access: {
  roles: ["admin", "support"]
}
```

If no access rule is defined, the capability should be considered
**restricted by default** unless explicitly marked public.

------------------------------------------------------------------------

## Access Declaration

Capabilities define access rules using the `access` property.

Example:

``` ts
export const approveRefund = defineCapability({
  name: "approveRefund",

  access: {
    roles: ["admin", "support"],
    scopes: ["refund.approve"],
    tenantScoped: true
  },

  handler: async (ctx, input) => {
    ...
  }
})
```

Access rules are evaluated automatically by the framework before
executing the capability handler.

------------------------------------------------------------------------

## Access Contract Fields

An access declaration may include:

## roles

Defines which user roles are allowed.

Example:

``` ts
roles: ["admin", "support"]
```

Roles typically come from the authentication provider.

------------------------------------------------------------------------

## scopes

Defines fine-grained permissions.

Example:

``` ts
scopes: ["invoice.read", "refund.approve"]
```

Scopes allow more precise authorization control than roles.

------------------------------------------------------------------------

## public

Marks a capability as publicly accessible.

Example:

``` ts
access: {
  public: true
}
```

Public capabilities should still validate input carefully.

------------------------------------------------------------------------

## tenantScoped

Indicates that operations must respect tenant boundaries.

Example:

``` ts
tenantScoped: true
```

Tenant scoping prevents cross-tenant data access.

------------------------------------------------------------------------

## serviceAccounts

Allows invocation by specific service accounts.

Example:

``` ts
serviceAccounts: ["billing-worker"]
```

This enables internal automation while restricting external access.

------------------------------------------------------------------------

## Authorization Lifecycle

Authorization evaluation occurs before capability execution.

Execution sequence:

1.  request authenticated
2.  capability identified
3.  access rules evaluated
4.  authorization granted or denied
5.  handler executed if authorized

If authorization fails, the handler must not run.

------------------------------------------------------------------------

## Contextual Authorization

Capabilities may enforce additional contextual checks inside the
handler.

Example:

``` ts
if (refund.customerId !== ctx.auth.userId) {
  throw ctx.errors.forbidden("Not allowed to approve this refund")
}
```

Contextual checks should complement, not replace, access declarations.

------------------------------------------------------------------------

## Tenant Isolation

Multi-tenant systems require strict tenant isolation.

Tenant context is typically available through:

``` ts
ctx.auth.tenantId
```

Repositories should enforce tenant filtering automatically.

Example:

``` ts
await ctx.data.invoice.findMany({
  tenantId: ctx.auth.tenantId
})
```

Capabilities should not bypass tenant restrictions unless explicitly
intended.

------------------------------------------------------------------------

## Field-Level Restrictions

Sensitive entity fields may require additional protection.

Examples:

-   personal data
-   payment tokens
-   authentication secrets

Field metadata defined in the Entity SDK may include:

    classification
    encrypted
    maskedInLogs

The Security SDK should respect these flags when:

-   logging data
-   returning responses
-   generating audit records

------------------------------------------------------------------------

## Security Helpers

The runtime context may expose helper utilities.

Examples:

``` ts
ctx.security.hasRole("admin")
ctx.security.hasScope("refund.approve")
ctx.security.requireRole("admin")
```

These helpers simplify common authorization patterns.

------------------------------------------------------------------------

## Authorization Errors

Unauthorized access should produce standardized errors.

Example:

``` ts
throw ctx.errors.forbidden("Access denied")
```

Standardized errors ensure consistent API responses.

------------------------------------------------------------------------

## Security Metadata

Capability access declarations contribute to system metadata.

Security metadata may include:

-   roles required by capabilities
-   scopes used across the system
-   public endpoints
-   service account usage

This metadata supports:

-   governance analysis
-   security reviews
-   documentation generation

------------------------------------------------------------------------

## Governance Integration

The governance system may detect security risks such as:

-   capabilities without access declarations
-   overly broad role permissions
-   sensitive data exposure

These warnings appear during verification:

    plumbus verify

Teams may acknowledge exceptions through advisory governance.

------------------------------------------------------------------------

## Audit Integration

Security decisions should generate audit records.

Examples:

-   successful capability invocation
-   authorization failures
-   role-based access checks

Audit records allow organizations to trace system access patterns.

------------------------------------------------------------------------

## AI-Friendly Security Design

Security rules should remain easy for AI agents to interpret.

Guidelines:

-   define access rules explicitly
-   avoid hidden authorization logic
-   document role and scope usage clearly

Clear security declarations help prevent accidental vulnerabilities.

------------------------------------------------------------------------

## Best Practices

Recommended practices:

-   always define access rules for capabilities
-   use roles for broad permissions
-   use scopes for granular permissions
-   enforce tenant isolation consistently
-   audit sensitive operations

Security should remain **visible at the capability boundary**.

------------------------------------------------------------------------

## Summary

The Security SDK defines how Plumbus applications enforce authorization
policies.

By centralizing access declarations in capability contracts and
integrating them with authentication, repositories, audit logging, and
governance analysis, the framework ensures that application security
remains:

-   explicit
-   enforceable
-   auditable
-   analyzable
