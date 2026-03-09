# Plumbus Flow Engine Design

## Purpose

This document describes the flow engine, the orchestration subsystem of Plumbus.

Flows coordinate multiple capabilities into structured workflows that represent business processes.

While capabilities represent atomic actions, flows represent process logic.

Examples include:

- refund approval pipelines
- customer onboarding
- invoice generation cycles
- AI-assisted support ticket handling
- scheduled maintenance tasks

The flow engine enables systems to implement complex workflows while maintaining:

- auditability
- reliability
- fault tolerance
- explainability
- AI-safe modification boundaries

## Core Concept

A flow represents a sequence of steps executed over time.

Each step typically invokes a capability or performs a control operation such as:

- branching
- waiting
- scheduling
- retrying
- emitting events

Flows may run:

- immediately
- asynchronously
- in response to events
- on schedules
- across long periods of time

Flows are durable and resumable.

## Flow Structure

Each flow is defined in its own directory.

Example:

`app/flows/billing/refund-approval-flow/`

Containing:

- `flow.ts`
- `tests/refund-approval-flow.spec.ts`
- `fixtures/refund-approval-flow.fixture.ts`

Flows should remain self-contained and focused on a single business process.

## Flow Definition

Flows are defined using declarative orchestration logic.

A flow definition should describe:

- name
- domain
- trigger type
- input schema
- shared state shape
- ordered steps
- conditions
- retry behavior
- scheduling rules if applicable
- audit and explanation settings

Flow definitions describe process structure, not domain logic. Capabilities contain the actual business logic.

## Flow Execution Model

Flows execute as stateful workflows.

Each execution produces a persistent record describing:

- flow identifier
- input
- current step
- state snapshot
- execution history
- timestamps
- actor context
- audit references

This state allows flows to resume safely after interruptions.

## Architecture Overview

The flow engine uses a hybrid architecture combining:

- durable state storage
- queue-driven execution

Flow execution state is stored in PostgreSQL.
Runnable steps are executed through a durable queue and stateless worker processes.

This model provides:

- crash recovery
- retries
- delayed execution
- dead-letter handling
- distributed workers

## Flow State Storage

Flow state is stored in PostgreSQL.

Stored information includes:

- flow ID
- execution ID
- input payload
- current step
- step history
- execution status
- retry counters
- wait conditions
- audit references
- explanation references

This persistent state ensures workflows remain recoverable even if workers fail.

## Queue-Driven Step Execution

When a flow step is ready to run, a job is placed onto the execution queue.

Workers:

1. fetch a queued step
2. load flow state
3. execute the next step
4. update flow state
5. enqueue the next step, complete the flow, or mark it waiting or failed

Queue-driven execution enables:

- retry policies
- delayed execution
- horizontal worker scaling
- dead-letter handling

## Worker Model

Workers are stateless processes responsible for executing flow steps.

Worker responsibilities:

- fetch job from queue
- load execution state
- run the step
- persist results
- enqueue next step

Workers should not maintain long-lived in-memory state.

## Step Types

Flows support several step types.

### Capability execution
Invokes a capability.

### Conditional branching
Evaluates declared conditions and chooses the next path.

### Wait and resume
Pauses execution until a condition is met, such as an event, timeout, or external approval.

### Scheduled delay
Pauses execution until a future time.

### Event emission
Emits a framework event.

In v0.1, true first-class parallel branch execution is deferred to keep semantics simpler.

## Retry Behavior

Steps may specify retry policies.

Retry settings may include:

- attempts
- backoff strategy
- retryable error categories

Retry policies help handle transient failures such as:

- network errors
- temporary service outages
- rate limits

Non-retryable failures terminate the flow.

## Dead-Letter Handling

If a step fails repeatedly, the flow enters a failed state and the step job may be moved to a dead-letter queue.

Failure information includes:

- failed step
- error details
- retry history
- execution context

Failed flows may be:

- retried manually
- inspected by operators
- resumed after correction

Dead-letter handling must be visible at both the queue level and the semantic flow level.

## Event-Triggered Flows

Flows may start in response to events.

When the triggering event occurs, the flow engine creates a new flow execution.

## Scheduled Flows

Flows may run on schedules.

Supported schedule types may include:

- fixed intervals
- daily or weekly schedules
- cron-style expressions

The scheduler creates new flow executions according to the schedule definition.

## Flow Audit Integration

Flow execution produces audit records capturing:

- flow start
- step execution
- decision branches
- retries
- failures
- completion

This provides visibility into long-running processes.

## Flow Explainability

Flows also produce explanation metadata.

Explanation records may include:

- why a branch was taken
- why a retry occurred
- which event resumed execution
- why execution entered a waiting or failed state

This makes workflow behavior understandable during debugging and review.

## AI Interaction in Flows

Flows may invoke AI-enabled capabilities or, in controlled cases, AI runtime operations.

AI-related execution data may include:

- prompt template version
- retrieval sources
- token usage
- validation results

This information becomes part of the audit trail.

## Testing Flows

Flows should include automated tests.

Tests may simulate flow execution using fixtures.

Flow tests should verify:

- branching logic
- retry behavior
- state transitions
- event triggers
- wait and resume behavior
- failure handling

## AI-Friendly Flow Design

Flow definitions should follow predictable patterns to support safe AI modification.

Guidelines include:

- one flow per directory
- small flow definitions
- explicit capability references
- predictable naming
- isolated tests

AI agents should be able to modify flow definitions without requiring full repository context.

## Best Practices

Flows should follow these rules:

- keep flows focused on one process
- avoid excessive branching
- prefer capabilities for business logic
- keep flow definitions readable
- avoid embedding complex logic inside flows

If flows become too complex, they should be broken into smaller flows connected by events.

## Summary

The flow engine orchestrates complex workflows by coordinating capabilities through durable, stateful execution.

Key properties include:

- durable workflow state
- queue-driven execution
- fault tolerance
- auditability
- explainability
- scalable workers

Flows enable Plumbus applications to implement complex business processes in a structured and reliable way.
