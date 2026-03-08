# Plumbus Core SDK Overview

## Purpose

This document defines the **Plumbus Core SDK**, the programming
interface developers and AI coding agents use to build applications on
the Plumbus framework.

While previous documents describe the architecture of the framework, the
SDK defines **how developers actually write Plumbus code**.

The SDK provides the primitives used to define:

-   capabilities
-   flows
-   entities
-   events
-   prompts
-   policies

These primitives create a structured environment where:

-   application behavior is explicit
-   contracts are machine-readable
-   architecture is enforceable
-   AI coding agents can reason about the system safely

------------------------------------------------------------------------

# Core Philosophy

The Plumbus SDK is designed around three ideas:

### 1. Contracts First

Every system component declares a contract describing:

-   inputs
-   outputs
-   security requirements
-   side effects

Contracts make system behavior explicit and analyzable.

------------------------------------------------------------------------

### 2. Declarative Architecture

Developers describe **what the system should do**, and the framework
handles:

-   routing
-   validation
-   authorization
-   auditing
-   observability

This reduces accidental complexity.

------------------------------------------------------------------------

### 3. AI-Operable Code

The SDK is intentionally structured so that **LLMs can understand and
modify code safely**.

This is achieved through:

-   explicit primitives
-   predictable file roles
-   strongly typed contracts
-   small isolated components

------------------------------------------------------------------------

# Core SDK Primitives

The Plumbus SDK exposes a small set of primitives used to define
application behavior.

## Capability

Defines an atomic business operation.

Example:

``` ts
export const approveRefund = defineCapability({
  name: "approveRefund",
  input: z.object({
    refundId: z.string()
  }),
  output: z.object({
    status: z.string()
  }),
  handler: async (ctx, input) => {
    const refund = await ctx.data.refund.findById(input.refundId)

    await ctx.data.refund.update(refund.id, {
      status: "approved"
    })

    await ctx.events.emit("refund.approved", {
      refundId: refund.id
    })

    return { status: "approved" }
  }
})
```

Capabilities represent the **core building blocks of application
logic**.

------------------------------------------------------------------------

## Flow

Defines a multi-step workflow.

Example:

``` ts
export const refundApprovalFlow = defineFlow({
  name: "refundApprovalFlow",

  steps: [
    "validateRefund",
    "approveRefund",
    "notifyCustomer"
  ]
})
```

Flows orchestrate capabilities into business processes.

------------------------------------------------------------------------

## Entity

Defines persistent domain data.

Example:

``` ts
export const Customer = defineEntity({
  name: "Customer",

  fields: {
    id: field.id(),
    email: field.string({ classification: "personal" }),
    createdAt: field.timestamp()
  }
})
```

Entities define database structure and privacy metadata.

------------------------------------------------------------------------

## Event

Defines domain events emitted by capabilities or flows.

Example:

``` ts
defineEvent({
  name: "refund.approved",
  payload: z.object({
    refundId: z.string()
  })
})
```

Events enable asynchronous communication and workflow triggers.

------------------------------------------------------------------------

## Prompt

Defines an AI prompt template.

Example:

``` ts
definePrompt({
  name: "summarize-ticket",
  input: z.object({
    message: z.string()
  }),
  output: z.object({
    summary: z.string()
  })
})
```

Prompts are used by the AI runtime.

------------------------------------------------------------------------

# Runtime Context

Capability handlers receive a **runtime context (`ctx`)**.

The context provides controlled access to system services.

## Authentication

``` ts
ctx.auth
```

Contains:

-   user identity
-   roles
-   tenant context

------------------------------------------------------------------------

## Data Access

``` ts
ctx.data
```

Provides repository access for entities.

Example:

``` ts
await ctx.data.customer.findById(id)
```

------------------------------------------------------------------------

## Events

``` ts
ctx.events
```

Used to emit domain events.

Example:

``` ts
ctx.events.emit("refund.approved", payload)
```

------------------------------------------------------------------------

## Flows

``` ts
ctx.flows
```

Used to trigger workflows.

Example:

``` ts
ctx.flows.start("refundApprovalFlow", input)
```

------------------------------------------------------------------------

## AI Runtime

``` ts
ctx.ai
```

Provides safe access to AI capabilities.

Example:

``` ts
await ctx.ai.generate({
  prompt: "summarize-ticket",
  input: { message }
})
```

------------------------------------------------------------------------

## Audit

``` ts
ctx.audit
```

Allows capabilities to record audit events.

Example:

``` ts
ctx.audit.record("refundApproved", {
  refundId
})
```

------------------------------------------------------------------------

# Capability Lifecycle

When a capability executes:

1.  request authenticated
2.  authorization policy evaluated
3.  input validated
4.  capability handler executed
5.  side effects performed
6.  events emitted
7.  audit logs written
8.  output validated
9.  response returned

The framework manages this lifecycle automatically.

------------------------------------------------------------------------

# SDK Design Goals

The SDK is designed to provide:

-   **predictable code structure**
-   **clear system boundaries**
-   **safe AI-assisted development**
-   **strong type safety**
-   **governance visibility**

Developers focus on **business logic**, while the framework manages
operational concerns.

------------------------------------------------------------------------

# Example Capability with Full Context

``` ts
export const approveRefund = defineCapability({
  name: "approveRefund",

  access: {
    roles: ["admin", "support"]
  },

  input: z.object({
    refundId: z.string()
  }),

  output: z.object({
    status: z.string()
  }),

  handler: async (ctx, input) => {

    const refund = await ctx.data.refund.findById(input.refundId)

    await ctx.data.refund.update(refund.id, {
      status: "approved"
    })

    await ctx.events.emit("refund.approved", {
      refundId: refund.id
    })

    await ctx.audit.record("refundApproved", {
      refundId: refund.id
    })

    return { status: "approved" }
  }
})
```

This example demonstrates how a capability integrates with:

-   authentication
-   authorization
-   data layer
-   event system
-   audit logging

------------------------------------------------------------------------

# Summary

The Plumbus Core SDK defines the programming model used to build
applications within the framework.

By exposing a small set of structured primitives and a controlled
runtime context, the SDK ensures that applications remain:

-   secure
-   auditable
-   testable
-   AI-friendly

The SDK is the foundation upon which all Plumbus applications are built.
