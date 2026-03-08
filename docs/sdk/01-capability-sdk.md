# Plumbus Capability SDK Specification

## Purpose

This document defines the **Capability SDK**, the primary programming
interface for implementing business logic in Plumbus.

Capabilities are the atomic building blocks of application behavior.
Every meaningful action or query in a Plumbus application should be
represented as a capability.

This document specifies:

-   the `defineCapability()` primitive
-   capability kinds
-   contract shape
-   handler behavior
-   access declarations
-   effect declarations
-   validation rules
-   audit and explanation integration
-   testing expectations
-   governance visibility

This document is intended to be the canonical reference for both
framework implementers and application developers.

------------------------------------------------------------------------

# Design Goals

The Capability SDK is designed to achieve the following:

-   make business logic explicit
-   keep side effects visible
-   centralize authorization
-   enable schema-driven validation
-   support auditability and explainability
-   generate machine-readable manifests
-   make code safe for AI-assisted modification

------------------------------------------------------------------------

# Conceptual Model

A capability is a **single unit of business intent**.

Examples:

-   createCustomer
-   approveRefund
-   listInvoices
-   classifyTicket
-   sendReminder

A capability must do one of the following:

-   return information
-   perform an action
-   initiate or assist a workflow
-   invoke governed AI behavior

A capability should not represent:

-   broad application modules
-   arbitrary utility bundles
-   mixed domain concerns
-   multi-step orchestration logic

Multi-step orchestration belongs in flows.

------------------------------------------------------------------------

# Capability Kinds

Plumbus should support at least the following capability kinds.

## Query

A query returns data and should not perform domain mutations.

Examples:

-   getCustomerProfile
-   listInvoices
-   searchKnowledgeBase

Queries may still:

-   read data
-   perform retrieval
-   invoke read-only AI classification or summarization
-   emit technical telemetry if needed

But they should not perform business state changes.

------------------------------------------------------------------------

## Action

An action performs a domain state change or external side effect.

Examples:

-   approveRefund
-   createInvoice
-   sendNotification
-   revokeAccess

Actions may:

-   write data
-   emit events
-   trigger flows
-   invoke external integrations
-   invoke governed AI operations when declared

------------------------------------------------------------------------

## Job

A job represents a background-executable operation that is still atomic
at the capability level.

Examples:

-   recalculateInvoiceTotals
-   reindexKnowledgeDocument
-   sendDigestEmail

Jobs are still capabilities, but may be invoked by flows, schedules, or
workers rather than direct end-user requests.

------------------------------------------------------------------------

## Event Handler

An event handler capability responds to a domain event.

Examples:

-   onInvoiceGenerated
-   onRefundApproved

This should still be modeled explicitly as a capability kind rather than
hidden in framework magic.

------------------------------------------------------------------------

# File Structure

Each capability should live in its own directory.

Recommended structure:

``` text
app/
  capabilities/
    <domain>/
      <capability-name>/
        capability.ts
        impl.ts
        tests/
          <capability-name>.spec.ts
        fixtures/
          <capability-name>.fixture.ts
```

Rules:

-   `capability.ts` contains the declarative contract
-   `impl.ts` contains executable logic
-   tests and fixtures are colocated
-   generated artifacts are never mixed with authored files

------------------------------------------------------------------------

# defineCapability()

The Capability SDK centers around a single primitive:

``` ts
defineCapability(...)
```

This primitive declares a capability contract. It should be
framework-owned and highly stable.

The primitive should accept a single object describing the capability.

Example:

``` ts
export const approveRefund = defineCapability({
  name: "approveRefund",
  kind: "action",
  domain: "billing",

  input: z.object({
    refundId: z.string(),
    reason: z.string().optional(),
  }),

  output: z.object({
    status: z.literal("approved"),
  }),

  access: {
    roles: ["admin", "support"],
  },

  effects: {
    data: ["refund"],
    events: ["refund.approved"],
    external: [],
    ai: false,
  },

  handler: async (ctx, input) => {
    const refund = await ctx.data.refund.findById(input.refundId)

    await ctx.data.refund.update(refund.id, {
      status: "approved",
    })

    await ctx.events.emit("refund.approved", {
      refundId: refund.id,
    })

    return { status: "approved" }
  },
})
```

In practice, Plumbus may split the contract and handler across
`capability.ts` and `impl.ts`, but this combined shape is the conceptual
model.

------------------------------------------------------------------------

# Capability Contract Shape

A capability contract should define the following sections.

## Identity

Required metadata:

-   `name`
-   `kind`
-   `domain`

Optional metadata:

-   description
-   tags
-   version
-   owner

The identity section allows the framework to generate manifests, docs,
and dependency graphs.

------------------------------------------------------------------------

## Input Schema

`input` defines the request shape.

Recommended standard:

-   Zod schemas in TypeScript

The input schema is used for:

-   runtime validation
-   type inference
-   generated docs
-   generated clients
-   AI briefs
-   testing helpers

A capability with no input should still explicitly declare an empty
object schema or use a framework standard for empty input.

------------------------------------------------------------------------

## Output Schema

`output` defines the response shape.

The output schema is used for:

-   runtime validation
-   type inference
-   generated docs
-   generated clients
-   contract tests
-   safe AI integration

Structured outputs are strongly preferred, even for simple capabilities.

------------------------------------------------------------------------

## Access Declaration

`access` defines who may invoke the capability.

This section may include:

-   roles
-   scopes
-   tenant constraints
-   service account requirements
-   contextual conditions
-   public/internal visibility flags

Example:

``` ts
access: {
  roles: ["admin", "support"],
  scopes: ["refund.approve"],
  tenantScoped: true,
}
```

The access declaration is required for any non-public capability.

Capabilities without an access declaration should be treated as denied
by default unless explicitly marked public.

------------------------------------------------------------------------

## Effect Declaration

`effects` defines meaningful side effects.

This should include at least:

-   data access surfaces
-   event emission
-   external integrations
-   flow triggers
-   AI usage

Example:

``` ts
effects: {
  data: ["refund"],
  events: ["refund.approved"],
  external: ["stripe"],
  flows: [],
  ai: false,
}
```

Effect declarations are used for:

-   auditing
-   policy analysis
-   governance warnings
-   AI-safe repository understanding

They should describe **what kinds of effects occur**, not every internal
implementation detail.

------------------------------------------------------------------------

## Audit Configuration

Capabilities may define audit behavior explicitly.

Example:

``` ts
audit: {
  enabled: true,
  event: "refundApproved",
  includeInput: ["refundId"],
  includeOutput: ["status"],
}
```

Audit configuration allows the framework to standardize what gets
recorded.

------------------------------------------------------------------------

## Explanation Configuration

Capabilities may define explanation behavior.

Example:

``` ts
explanation: {
  enabled: true,
  summary: "Approves an eligible refund and emits refund.approved",
}
```

This helps produce explanation trails and system introspection.

------------------------------------------------------------------------

## AI Configuration

If a capability uses AI, it should declare an AI section.

Example:

``` ts
ai: {
  prompts: ["summarize-ticket"],
  operations: ["generate"],
  structuredOutput: true,
}
```

This must integrate with the managed AI runtime and should not permit ad
hoc provider calls for standard use.

------------------------------------------------------------------------

## Verification Configuration

Capabilities may include verification expectations.

Example:

``` ts
verification: {
  requireTests: true,
  minimumCoverage: 0.8,
}
```

This metadata helps CLI and CI workflows enforce capability quality.

------------------------------------------------------------------------

# Handler Contract

The handler contains the implementation logic.

Recommended conceptual signature:

``` ts
handler: async (ctx, input) => { ... }
```

Where:

-   `ctx` is the execution context
-   `input` is already validated against the input schema

The handler must return output matching the output schema.

The framework is responsible for:

-   auth evaluation before handler execution
-   input validation before handler execution
-   output validation after handler execution
-   audit/explanation integration
-   error normalization

------------------------------------------------------------------------

# Runtime Context in Handlers

Capabilities should access system services through `ctx`.

Key services include:

-   `ctx.auth`
-   `ctx.data`
-   `ctx.events`
-   `ctx.flows`
-   `ctx.ai`
-   `ctx.audit`
-   `ctx.errors`

This ensures:

-   no hidden side effects
-   controlled infrastructure access
-   consistent auditing
-   safe AI usage
-   easier reasoning for humans and LLMs

Capabilities should not use random global services for core behavior.

------------------------------------------------------------------------

# Data Access Rules

Capabilities should use:

-   `ctx.data.<entity>.<operation>()`

rather than direct database access where the framework already provides
repository operations.

This ensures:

-   tenant isolation
-   audit hooks
-   consistent validation
-   policy visibility

If direct SQL or direct ORM access is used in advanced cases, the
framework should surface governance warnings and require explicit
declarations where appropriate.

------------------------------------------------------------------------

# Event Emission

Capabilities emit events through:

-   `ctx.events.emit(eventName, payload)`

This ensures emitted events are:

-   schema-validated
-   auditable
-   routed through the outbox pattern
-   visible to policy analysis

Capabilities should not publish directly to brokers.

------------------------------------------------------------------------

# Flow Interaction

Capabilities may trigger flows through:

-   `ctx.flows.start(flowName, input)`

This should be declared in effects.

Capabilities should not embed complex orchestration logic themselves. If
orchestration becomes substantial, the logic should move into a flow.

------------------------------------------------------------------------

# AI Usage in Capabilities

Capabilities may invoke the AI runtime through `ctx.ai`.

Examples:

-   `ctx.ai.generate(...)`
-   `ctx.ai.extract(...)`
-   `ctx.ai.classify(...)`
-   `ctx.ai.retrieve(...)`

AI usage should be:

-   explicit
-   policy-visible
-   auditable
-   budget-trackable

Direct provider SDK usage should be discouraged for standard application
behavior.

------------------------------------------------------------------------

# Error Handling

Capabilities should throw structured framework errors via `ctx.errors`.

Examples:

-   `ctx.errors.notFound(...)`
-   `ctx.errors.validation(...)`
-   `ctx.errors.forbidden(...)`
-   `ctx.errors.conflict(...)`

This gives the framework consistent error normalization and client
behavior.

Capabilities should avoid throwing arbitrary opaque objects.

------------------------------------------------------------------------

# Capability Lifecycle

The conceptual execution lifecycle is:

1.  request or invocation received
2.  capability identified
3.  input validated
4.  auth evaluated
5.  handler executed
6.  side effects performed
7.  audit and explanation data recorded
8.  output validated
9.  result returned or queued downstream work continues

This lifecycle should be stable across capability kinds.

------------------------------------------------------------------------

# Manifest Generation

From capability contracts, the framework should generate
machine-readable manifests.

A manifest may include:

-   name
-   kind
-   domain
-   input and output schema metadata
-   access metadata
-   effect metadata
-   AI metadata
-   linked flows and events
-   documentation summary

These manifests power:

-   docs
-   CLI output
-   policy analysis
-   AI briefs
-   architectural visualization

------------------------------------------------------------------------

# Testing Expectations

Every capability should have associated tests.

The Capability SDK should make testing straightforward by allowing
capabilities to be invoked in isolation through helpers.

Capability tests should cover:

-   success cases
-   authorization behavior
-   validation failures
-   declared side effects
-   audit behavior
-   AI behavior if present

The framework should generate test scaffolds from the capability
contract.

------------------------------------------------------------------------

# AI-Friendly Design Constraints

The Capability SDK must remain highly predictable.

Guidelines:

-   one capability per directory
-   one contract file
-   one implementation file
-   stable naming
-   explicit dependencies
-   minimal hidden conventions

These constraints reduce the risk of unsafe AI-generated changes.

------------------------------------------------------------------------

# Best Practices

Recommended practices:

-   make capabilities small and specific
-   use actions for state changes and queries for reads
-   declare effects honestly
-   keep handlers short and focused
-   move orchestration into flows
-   prefer structured output schemas
-   test capabilities in isolation
-   let the framework own validation, auth wiring, and auditing

A capability should be understandable by reading:

-   `capability.ts`
-   `impl.ts`
-   its tests
-   its generated brief

------------------------------------------------------------------------

# Summary

The Capability SDK defines the most important programming primitive in
Plumbus.

By standardizing capability contracts, handler behavior, access
declarations, effect declarations, and runtime integration, the SDK
ensures that business logic remains:

-   explicit
-   secure
-   auditable
-   testable
-   AI-friendly

Capabilities are the unit from which the rest of the framework is built.
