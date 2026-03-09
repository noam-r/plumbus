# Plumbus Flow SDK Specification

## Purpose

This document defines the **Flow SDK**, which provides the programming
model for orchestrating multi-step workflows in Plumbus.

Capabilities represent atomic business operations. Flows coordinate
capabilities into structured processes that may:

-   span multiple steps
-   react to events
-   wait for external signals
-   retry on failure
-   run on schedules
-   execute asynchronously

The Flow SDK defines:

-   the `defineFlow()` primitive
-   flow structure
-   step types
-   state handling
-   retry behavior
-   event triggers
-   scheduling
-   flow context
-   interaction with capabilities
-   testing model

------------------------------------------------------------------------

# Conceptual Model

A **flow** represents a stateful workflow.

Examples:

-   customer onboarding
-   refund approval
-   invoice generation
-   support ticket triage
-   AI-assisted document processing

Flows coordinate capabilities and external signals to complete
long-running tasks.

Unlike capabilities, flows:

-   persist state
-   may span minutes, hours, or days
-   react to events
-   may pause and resume

------------------------------------------------------------------------

# Flow File Structure

Each flow lives in its own directory.

Recommended layout:

    app/
      flows/
        <domain>/
          <flow-name>/
            flow.ts
            steps/
            tests/

Example:

    app/flows/billing/refund-approval/
      flow.ts
      steps/
      tests/

This structure keeps orchestration logic isolated and understandable.

------------------------------------------------------------------------

# defineFlow()

Flows are defined using:

``` ts
defineFlow(...)
```

Example:

``` ts
export const refundApprovalFlow = defineFlow({
  name: "refundApprovalFlow",
  domain: "billing",

  input: z.object({
    refundId: z.string()
  }),

  steps: [
    "validateRefund",
    "approveRefund",
    "notifyCustomer"
  ]
})
```

The flow contract describes:

-   identity
-   input schema
-   step sequence
-   retry behavior
-   trigger conditions
-   scheduling rules

------------------------------------------------------------------------

# Flow Contract Fields

A flow definition may include:

## Identity

    name
    domain
    description (optional)
    tags (optional)

------------------------------------------------------------------------

## Input Schema

Defines the initial input provided when the flow starts.

Example:

``` ts
input: z.object({
  refundId: z.string()
})
```

The input schema supports:

-   validation
-   generated docs
-   generated clients
-   AI briefs

------------------------------------------------------------------------

## State Schema

Flows may define a state schema used to persist workflow state.

Example:

``` ts
state: z.object({
  refundId: z.string(),
  approvalStatus: z.enum(["pending","approved","rejected"])
})
```

State persists across retries and waits.

------------------------------------------------------------------------

## Steps

Steps define the workflow progression.

Example:

``` ts
steps: [
  "validateRefund",
  "approveRefund",
  "notifyCustomer"
]
```

Steps should reference capabilities or step handlers.

------------------------------------------------------------------------

# Step Types

Flows support several step types.

## Capability Step

Executes a capability.

Example:

``` ts
{
  type: "capability",
  name: "approveRefund"
}
```

------------------------------------------------------------------------

## Conditional Step

Branches flow execution.

Example:

``` ts
{
  type: "condition",
  if: "refundIsValid",
  then: "approveRefund",
  else: "rejectRefund"
}
```

------------------------------------------------------------------------

## Wait Step

Pauses execution until a signal or event arrives.

Example:

``` ts
{
  type: "wait",
  event: "customer.responded"
}
```

------------------------------------------------------------------------

## Delay Step

Introduces a scheduled delay.

Example:

``` ts
{
  type: "delay",
  duration: "24h"
}
```

------------------------------------------------------------------------

## Parallel Step

Runs multiple branches simultaneously.

Example:

``` ts
{
  type: "parallel",
  branches: ["notifyCustomer", "updateAnalytics"]
}
```

------------------------------------------------------------------------

# Retry Policies

Flows should support retry policies for resilient execution.

Example:

``` ts
retry: {
  attempts: 3,
  backoff: "exponential"
}
```

Retries may apply globally or per-step.

------------------------------------------------------------------------

# Event Triggers

Flows may start automatically in response to events.

Example:

``` ts
trigger: {
  event: "refund.requested"
}
```

The event payload becomes the flow input.

------------------------------------------------------------------------

# Scheduling

Flows may run on schedules.

Example:

``` ts
schedule: {
  cron: "0 0 * * *"
}
```

This allows recurring processes.

------------------------------------------------------------------------

# Flow Context

During execution, steps receive a **flow context (`ctx`)**.

Flow context provides:

-   access to persisted state
-   capability invocation
-   event emission
-   flow control

Example usage:

``` ts
ctx.state
ctx.capabilities
ctx.events
ctx.flows
ctx.audit
```

------------------------------------------------------------------------

# Flow State Management

Flow state should persist after each step.

Example state transition:

1.  step executed
2.  state updated
3.  state saved
4.  next step scheduled

State persistence ensures recovery after failures.

------------------------------------------------------------------------

# Interaction with Capabilities

Flows should use capabilities for domain operations.

Example:

``` ts
await ctx.capabilities.approveRefund({ refundId })
```

This ensures:

-   consistent authorization
-   audit visibility
-   governance compliance

Flows should not directly implement domain logic already represented as
capabilities.

------------------------------------------------------------------------

# Event Interaction

Flows may emit events.

Example:

``` ts
ctx.events.emit("refund.approved", payload)
```

Flows may also wait for events.

This allows asynchronous processes to coordinate across services.

------------------------------------------------------------------------

# Error Handling

When a step fails:

1.  retry policy evaluated
2.  step retried if allowed
3.  failure recorded
4.  flow marked failed if retries exhausted

Optional failure handlers may be defined.

------------------------------------------------------------------------

# Flow Manifest

The framework should generate a flow manifest containing:

-   name
-   steps
-   triggers
-   schedules
-   dependencies
-   state schema

Manifests enable:

-   documentation
-   architecture diagrams
-   policy analysis
-   AI briefs

------------------------------------------------------------------------

# Testing Flows

Flows should support simulation testing.

Example:

``` ts
simulateFlow("refundApprovalFlow", {
  refundId: "123"
})
```

Tests should verify:

-   correct step execution
-   branching logic
-   retry behavior
-   event handling
-   final state

Flow simulation avoids requiring full runtime infrastructure during
tests.

------------------------------------------------------------------------

# Governance Visibility

Flow definitions should expose metadata used by governance analysis.

Examples:

-   external integrations
-   AI operations
-   cross-tenant data access
-   long-running workflows

Governance rules may warn about:

-   overly complex flows
-   excessive retries
-   missing error handlers

------------------------------------------------------------------------

# AI-Friendly Flow Design

Flows must remain understandable for coding agents.

Guidelines:

-   small number of steps
-   descriptive step names
-   explicit branching
-   minimal hidden behavior

AI agents should be able to understand the entire workflow by reading a
single flow definition.

------------------------------------------------------------------------

# Best Practices

Recommended practices:

-   keep flows small
-   use capabilities for domain logic
-   declare retry policies explicitly
-   prefer event triggers over polling
-   test flows with simulation tools

Flows should represent **business processes, not arbitrary code
execution pipelines**.

------------------------------------------------------------------------

# Summary

The Flow SDK defines how Plumbus orchestrates multi-step processes.

By combining declarative flow definitions, durable state management, and
capability integration, flows allow applications to implement reliable
business workflows while maintaining:

-   observability
-   auditability
-   governance visibility
-   AI-friendly structure
