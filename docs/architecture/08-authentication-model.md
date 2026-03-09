# Plumbus Authentication Model

## Purpose

This document defines the **Authentication Model** of the Plumbus
framework.

Authentication is responsible for identifying the actor initiating a
request. It establishes the identity context used later by the
**Security Model** to evaluate authorization rules.

Authentication answers the question:

**Who is making this request?**

Authorization (covered in the Security Model) answers:

**What is this actor allowed to do?**

The authentication subsystem provides:

-   identity verification
-   principal creation
-   session management
-   token validation
-   identity propagation to capabilities, flows, and events
-   integration with external identity providers

Authentication must remain **modular and adapter-driven** so
applications can integrate with common identity systems.

------------------------------------------------------------------------

## Core Authentication Principles

### 1. Authentication and Authorization Are Separate

Authentication determines identity.

Authorization determines permissions.

The authentication layer should **never contain business authorization
logic**. It only produces the identity context used by the security
subsystem.

------------------------------------------------------------------------

### 2. Identity Context Must Be Explicit

Authenticated requests must populate a structured identity object
available throughout execution.

Example conceptual structure:

    ctx.auth

Typical fields:

-   userId
-   roles
-   scopes
-   tenantId
-   provider
-   authentication timestamp

This context is immutable during request execution.

------------------------------------------------------------------------

### 3. Provider-Agnostic Design

Plumbus should not hard-code support for a single authentication
provider.

Instead it should support **authentication adapters**.

Adapters allow integration with:

-   OIDC providers
-   hosted identity services
-   custom enterprise identity systems
-   service-to-service authentication

------------------------------------------------------------------------

## Identity Model

The authentication system recognizes different types of actors.

### Users

Human users authenticated through identity providers.

Examples:

-   application users
-   administrators
-   support staff

------------------------------------------------------------------------

### Service Accounts

Non-human actors used by services or automation.

Examples:

-   background workers
-   integration services
-   scheduled jobs

Service accounts may authenticate using API tokens or internal
credentials.

------------------------------------------------------------------------

### System Actors

Internal framework processes acting without external authentication.

Examples:

-   scheduled flows
-   internal maintenance tasks

System actors should still produce identity metadata for audit trails.

------------------------------------------------------------------------

## Authentication Flow

A typical authentication lifecycle:

1.  client sends request with credentials
2.  API layer invokes authentication adapter
3.  adapter validates credentials
4.  identity context is created
5.  identity is attached to execution context (`ctx.auth`)
6.  authorization evaluation occurs before capability execution

------------------------------------------------------------------------

## Authentication Providers

Plumbus should support common authentication providers via adapters.

Examples include:

-   OIDC
-   Auth0
-   Clerk
-   Cognito
-   custom OAuth2 providers

Adapters should normalize identity information into the framework's
identity context.

------------------------------------------------------------------------

## Token-Based Authentication

Most API clients authenticate using tokens.

Common token types include:

-   JWT tokens
-   opaque access tokens
-   API keys
-   service tokens

Token validation should occur inside the authentication adapter.

Capabilities should never parse or validate tokens directly.

------------------------------------------------------------------------

## Session-Based Authentication

Browser-based applications may use session cookies.

Sessions may include:

-   encrypted cookies
-   session identifiers stored in a database
-   token-backed sessions

Session management should be implemented at the API layer and adapter
level.

------------------------------------------------------------------------

## Multi-Tenant Authentication

Multi-tenant applications require tenant awareness.

Authentication providers may supply:

-   tenant identifiers
-   organization identifiers
-   account scopes

The authentication system should propagate tenant context through
`ctx.auth`.

Capabilities and repositories can then enforce tenant isolation.

------------------------------------------------------------------------

## Identity Propagation

Identity must propagate across system boundaries.

This includes:

-   capability execution
-   flow execution
-   event emission
-   background tasks

When flows trigger additional work, the originating identity should
remain traceable.

Example metadata propagated:

-   actorId
-   tenantId
-   correlationId

------------------------------------------------------------------------

## Service-to-Service Authentication

Internal services may authenticate using service credentials.

Examples:

-   API tokens
-   signed service tokens
-   mTLS identity certificates

Service identities should be distinguishable from user identities.

------------------------------------------------------------------------

## Audit Integration

Authentication events should produce audit records.

Examples:

-   login success
-   login failure
-   token validation
-   session expiration

Audit records should include:

-   actor identifier
-   authentication provider
-   timestamp
-   request metadata

This supports security monitoring and compliance reporting.

------------------------------------------------------------------------

## Security Integration

The authentication subsystem provides the identity context required by
the **Security Model**.

Authorization checks rely on:

-   roles
-   scopes
-   tenant context

These values originate from the authentication process.

------------------------------------------------------------------------

## Governance and Compliance

Authentication configuration may affect compliance posture.

Examples:

-   weak token policies
-   missing session expiration
-   missing MFA support

Governance analysis may detect these issues and include them in policy
compatibility reports.

------------------------------------------------------------------------

## AI-Friendly Authentication Design

The authentication subsystem must remain understandable for coding
agents.

Guidelines:

-   keep adapters explicit
-   avoid hidden authentication flows
-   clearly document identity context fields
-   centralize authentication logic

This reduces the risk of accidental security vulnerabilities introduced
by automated code modifications.

------------------------------------------------------------------------

## Best Practices

Recommended practices:

-   rely on established identity providers
-   avoid custom authentication mechanisms when possible
-   keep authentication adapters isolated
-   audit authentication activity
-   propagate identity context through all system operations

Authentication should remain **simple, predictable, and externally
integrated**.

------------------------------------------------------------------------

## Summary

The Plumbus authentication model establishes identity context for all
system operations.

By separating authentication from authorization and supporting
adapter-based identity providers, the framework enables:

-   flexible identity integration
-   consistent identity propagation
-   strong security foundations
-   reliable audit trails

Authentication provides the **identity layer that powers the security
model and governance system**.
