# Plumbus Runtime Context SDK Specification

## Purpose

This document defines the **Runtime Context SDK**, the API surface
exposed to capability and flow handlers through the `ctx` object.

The runtime context provides **controlled access to all framework
services**, ensuring that application logic interacts with the system
through predictable, auditable interfaces.

The runtime context enables access to:

-   authentication and identity
-   entity repositories
-   event emission
-   flow orchestration
-   AI runtime services
-   audit logging
-   error generation

By routing all system interactions through `ctx`, the framework ensures:

-   consistent security enforcement
-   audit visibility
-   policy compatibility analysis
-   AI-safe code modification

------------------------------------------------------------------------

# Conceptual Model

When a capability or flow executes, it receives a **runtime context
object**.

Example:

``` ts
handler: async (ctx, input) => {
  const customer = await ctx.data.customer.findById(input.customerId)
}
```

The runtime context contains framework-managed services.

Capabilities should **never bypass the runtime context** for system
operations when a context API is available.

------------------------------------------------------------------------

# Context Structure

The runtime context may expose the following properties:

    ctx.auth
    ctx.data
    ctx.events
    ctx.flows
    ctx.ai
    ctx.audit
    ctx.errors
    ctx.logger
    ctx.time
    ctx.config

Each section provides controlled access to specific services.

------------------------------------------------------------------------

# ctx.auth

Provides authentication and identity information.

Example:

``` ts
ctx.auth.userId
ctx.auth.roles
ctx.auth.tenantId
```

Typical fields:

-   `userId`
-   `roles`
-   `scopes`
-   `tenantId`
-   `provider`
-   `sessionId`

Capabilities use this information for contextual behavior but should
rely on the framework for authorization enforcement.

------------------------------------------------------------------------

# ctx.data

Provides access to entity repositories.

Example:

``` ts
await ctx.data.customer.findById(id)
await ctx.data.invoice.create(data)
await ctx.data.refund.update(id, updates)
```

Repositories generated from entity definitions enforce:

-   schema validation
-   tenant isolation
-   audit logging
-   policy checks

Direct database queries should be avoided unless explicitly allowed.

------------------------------------------------------------------------

# ctx.events

Used to emit domain events.

Example:

``` ts
await ctx.events.emit("refund.approved", {
  refundId: refund.id
})
```

Event emission triggers:

-   outbox recording
-   asynchronous delivery
-   flow triggers
-   audit records

Capabilities should not publish directly to message brokers.

------------------------------------------------------------------------

# ctx.flows

Used to start or interact with workflows.

Example:

``` ts
await ctx.flows.start("refundApprovalFlow", {
  refundId: refund.id
})
```

Possible operations:

-   `start(flowName, input)`
-   `resume(flowId)`
-   `cancel(flowId)`

Flows allow long-running orchestration without embedding complex logic
in capabilities.

------------------------------------------------------------------------

# ctx.ai

Provides access to the managed AI runtime.

Example:

``` ts
const result = await ctx.ai.generate({
  prompt: "summarize-ticket",
  input: { message }
})
```

Supported operations may include:

-   `generate()`
-   `extract()`
-   `classify()`
-   `retrieve()`

AI runtime features include:

-   prompt templates
-   structured output validation
-   cost tracking
-   security filtering
-   audit integration

Capabilities should avoid calling provider SDKs directly for standard
operations.

------------------------------------------------------------------------

# ctx.audit

Records audit events.

Example:

``` ts
await ctx.audit.record("refundApproved", {
  refundId: refund.id
})
```

Audit records may include:

-   actor identity
-   capability name
-   event type
-   relevant metadata
-   timestamps

Audit logs support:

-   explainability
-   compliance reporting
-   operational debugging

------------------------------------------------------------------------

# ctx.errors

Provides standardized error constructors.

Example:

``` ts
throw ctx.errors.notFound("Refund not found")
throw ctx.errors.forbidden("Unauthorized action")
```

Common error types:

-   `validation()`
-   `notFound()`
-   `forbidden()`
-   `conflict()`
-   `internal()`

Using standardized errors ensures consistent API responses.

------------------------------------------------------------------------

# ctx.logger

Provides structured logging.

Example:

``` ts
ctx.logger.info("Refund approved", {
  refundId: refund.id
})
```

Logging should avoid including sensitive classified data.

Structured logging improves observability and debugging.

------------------------------------------------------------------------

# ctx.time

Provides time utilities.

Example:

``` ts
const now = ctx.time.now()
```

Using a framework time provider improves:

-   deterministic testing
-   simulation environments
-   time-based workflows

------------------------------------------------------------------------

# ctx.config

Provides access to application configuration.

Example:

``` ts
const apiKey = ctx.config.paymentProvider.apiKey
```

Configuration access should remain read-only from runtime context.

Secrets should be managed by the deployment environment.

------------------------------------------------------------------------

# Context Lifecycle

The runtime context is created at the start of execution and destroyed
after completion.

Example lifecycle:

1.  request received
2.  authentication performed
3.  runtime context created
4.  capability handler executed
5.  audit events recorded
6.  response returned
7.  context disposed

The context should not persist outside the execution boundary.

------------------------------------------------------------------------

# Flow Context

Flow step handlers receive a similar context.

Additional fields may include:

    ctx.state
    ctx.step
    ctx.flowId

These fields allow flows to track execution state.

------------------------------------------------------------------------

# Testing Context

Testing helpers should create a mock runtime context.

Example:

``` ts
const ctx = createTestContext()
```

Mock contexts allow:

-   deterministic testing
-   simulated repositories
-   simulated event emission
-   AI runtime mocking

------------------------------------------------------------------------

# Governance Visibility

Operations executed through the runtime context are visible to
governance systems.

Examples:

-   AI usage
-   external integrations
-   data access patterns

This visibility allows governance checks to analyze application
behavior.

------------------------------------------------------------------------

# AI-Friendly Context Design

The runtime context should remain predictable and well-documented.

Guidelines:

-   stable property names
-   minimal hidden behavior
-   explicit service boundaries

This ensures AI agents can safely understand how to interact with the
system.

------------------------------------------------------------------------

# Best Practices

Recommended practices:

-   always use `ctx` for system interactions
-   avoid global service access
-   rely on repository APIs instead of direct database access
-   emit events through `ctx.events`
-   record meaningful audit actions

The runtime context should act as the **single gateway to framework
services**.

------------------------------------------------------------------------

# Summary

The Runtime Context SDK defines the structured interface through which
application code interacts with Plumbus services.

By providing controlled access to authentication, data repositories,
event emission, flows, AI runtime, and audit logging, the runtime
context ensures that application behavior remains:

-   secure
-   observable
-   auditable
-   compatible with governance analysis
