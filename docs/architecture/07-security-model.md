# Plumbus Security Model

## Purpose

This document defines the **Security Model** of the Plumbus framework.

Security in Plumbus is designed to be **explicit, auditable, and
capability-centered**. Instead of scattering authorization logic across
controllers, services, and middleware, Plumbus centralizes security at
the **capability boundary**.

The security model is responsible for:

-   authorization enforcement
-   tenant isolation
-   access policy evaluation
-   field-level data restrictions
-   security audit generation
-   policy compatibility analysis

Security is treated as a **first-class system concern**, not an
application afterthought.

------------------------------------------------------------------------

# Core Security Principles

## 1. Deny by Default

Capabilities must explicitly declare access requirements.

If a capability does not declare who may execute it, the system should
assume **no access**.

This prevents accidental exposure of functionality.

------------------------------------------------------------------------

## 2. Capability-Level Authorization

Authorization is evaluated at the **capability boundary**.

This ensures:

-   a single enforcement point
-   consistent behavior
-   easy auditing
-   predictable security guarantees

Authorization should occur **before capability implementation logic
runs**.

------------------------------------------------------------------------

## 3. Explicit Access Contracts

Capabilities must declare who may invoke them.

Example conceptual contract:

``` ts
access: {
  roles: ["admin", "support"]
}
```

Access contracts may include:

-   roles
-   scopes
-   tenant constraints
-   contextual conditions

------------------------------------------------------------------------

## 4. Separation of Authentication and Authorization

Authentication determines **who the actor is**.

Authorization determines **what that actor may do**.

These concerns must remain separate.

Authentication populates the identity context, while authorization
evaluates capability policies.

------------------------------------------------------------------------

# Identity Context

Authenticated requests provide identity information through the
execution context.

Example:

    ctx.auth

The authentication context may include:

-   userId
-   roles
-   scopes
-   tenantId
-   authentication provider metadata

Capabilities should not directly parse tokens or session data.

------------------------------------------------------------------------

# Authorization Evaluation

Authorization evaluation occurs before capability execution.

Execution lifecycle:

1.  request authenticated
2.  capability identified
3.  capability access policy evaluated
4.  authorization granted or denied
5.  capability implementation executed

If authorization fails, the capability implementation must not run.

------------------------------------------------------------------------

# Role-Based Access

The simplest authorization model is role-based access control (RBAC).

Example:

    roles: ["admin"]

RBAC is easy to reason about and should be the default model.

However, it should remain flexible enough to support:

-   service roles
-   internal system actors
-   automation roles

------------------------------------------------------------------------

# Scope-Based Access

Capabilities may optionally require scopes.

Scopes represent fine-grained permissions.

Example:

    scopes: ["invoice.read"]

Scopes allow capability contracts to express more precise permissions
than roles alone.

------------------------------------------------------------------------

# Conditional Authorization

Some capabilities may require conditional access rules.

Examples:

-   the actor owns the resource
-   the actor belongs to the resource's tenant
-   the actor has delegated access

Conditional policies should remain simple and explicit.

------------------------------------------------------------------------

# Tenant Isolation

Multi-tenant systems require strict tenant isolation.

Tenant isolation should ensure that:

-   actors cannot access data belonging to another tenant
-   queries automatically include tenant scope
-   flows and events propagate tenant context

Capabilities should not manually implement tenant filtering logic where
the framework can enforce it.

------------------------------------------------------------------------

# Field-Level Restrictions

Some fields may require restricted access.

Examples:

-   personal data
-   financial data
-   authentication secrets

The framework may support:

-   read restrictions
-   write restrictions
-   logging restrictions

Field-level security rules should integrate with entity classification
metadata.

------------------------------------------------------------------------

# Data Access Enforcement

The repository/data service layer should enforce security policies when
accessing entities.

This ensures:

-   consistent access control
-   reduced risk of developer mistakes
-   easier auditability

Capabilities should rely on repository services rather than raw database
access.

------------------------------------------------------------------------

# Flow Security

Flows must propagate security context.

This includes:

-   actor identity
-   tenant context
-   security scopes

Flows triggered by events should inherit the security context from the
originating event when possible.

------------------------------------------------------------------------

# Event Security

Events should preserve security context metadata.

Event metadata may include:

-   actor identity
-   tenant identifier
-   correlation identifiers

This ensures downstream consumers can evaluate security policies
correctly.

------------------------------------------------------------------------

# Audit Integration

Security decisions must be auditable.

Audit records should capture:

-   actor identity
-   capability invoked
-   authorization result
-   timestamp
-   relevant resource identifiers

This allows operators to answer questions such as:

-   who accessed a specific resource
-   when access occurred
-   whether access was authorized

------------------------------------------------------------------------

# Policy Compatibility

The security model integrates with governance analysis.

Policy evaluation may detect issues such as:

-   capabilities missing access rules
-   sensitive data accessed without appropriate roles
-   weak tenant isolation

These results appear in policy compatibility reports.

Example:

    plumbus certify policy soc2

------------------------------------------------------------------------

# Advisory Governance Interaction

Security warnings may be produced when risky patterns are detected.

Examples:

-   capability with overly broad access
-   sensitive fields exposed in responses
-   cross-tenant access paths

Developers may acknowledge these risks when necessary.

------------------------------------------------------------------------

# AI Safety Considerations

Security logic must remain predictable for coding agents.

Guidelines:

-   keep access contracts simple
-   avoid hidden security rules
-   define authorization declaratively
-   rely on framework enforcement rather than ad-hoc checks

This reduces the chance that AI-generated code introduces
vulnerabilities.

------------------------------------------------------------------------

# Best Practices

Recommended practices:

-   always define access rules in capability contracts
-   prefer role-based access for clarity
-   avoid manual security checks when the framework provides enforcement
-   audit sensitive operations
-   restrict access to sensitive fields

Security should be **explicit and visible in capability contracts**.

------------------------------------------------------------------------

# Summary

The Plumbus security model ensures that authorization is enforced
consistently and transparently.

By centralizing security at the capability boundary, the framework
provides:

-   predictable access control
-   strong tenant isolation
-   clear audit trails
-   governance visibility

This model supports building systems that are secure, explainable, and
compatible with compliance frameworks.
