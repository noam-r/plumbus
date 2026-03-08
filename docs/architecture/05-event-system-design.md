# Plumbus Event System Design

## Purpose

This document defines the Plumbus event system.

The event system provides asynchronous communication between capabilities, flows, and integrations. It enables decoupled processing while preserving the framework’s guarantees around auditability, explainability, policy visibility, and operational reliability.

Events in Plumbus represent domain facts that have already happened.

Examples:

- `customer.created`
- `invoice.generated`
- `refund.approved`
- `subscription.cancelled`

The event system is used to:

- trigger flows
- drive asynchronous processing
- notify integrations
- decouple capabilities from downstream work
- provide durable records of domain facts

## Design Goals

The event system is designed to:

- preserve transactional consistency
- provide durable delivery
- support at-least-once semantics
- remain auditable and explainable
- be safe for multi-tenant systems
- integrate naturally with capabilities and flows
- remain simple enough for v0.1 adoption

## Core Principles

### Domain facts, not remote procedure calls
Events should describe facts that occurred, not instructions to perform arbitrary work.

Good:
- `invoice.generated`
- `refund.approved`

Less good:
- `sendInvoiceNow`
- `doRefundPipeline`

Capabilities and flows should use events to communicate facts, while orchestration remains explicit in the flow system.

### Transactional consistency first
An event must not be emitted in a way that can be lost if the business transaction succeeds.

Plumbus uses the outbox pattern so that state changes and event creation are committed together.

### At-least-once delivery
The event system should guarantee durable, retryable delivery rather than exactly-once semantics.

Consumers must be idempotent.

### Explicit schemas and versions
Events are structured artifacts with declared schemas and versioning metadata.

### Advisory-first visibility
Undeclared or risky event behavior should be surfaced and reported rather than silently allowed.

## Event Model

An event consists of:

- event name
- event version
- payload
- metadata
- actor context
- correlation identifiers
- tenant and scope information where relevant
- timestamps

Conceptual structure:

- `eventType`
- `version`
- `payload`
- `occurredAt`
- `actor`
- `tenantId`
- `correlationId`
- `causationId`

### Event Type Naming

Recommended naming pattern:

`<domain>.<fact>`

Examples:

- `customer.created`
- `invoice.generated`
- `refund.approved`
- `billing.cycle.closed`

A version suffix may be represented in metadata rather than in the event name itself. The framework should treat versioning as first-class even if the wire representation varies.

## Event Definition

Plumbus should support explicit event definitions.

An event definition should include:

- event type
- version
- payload schema
- documentation summary
- classification hints if the payload contains sensitive information

This allows the framework to:

- validate emitted payloads
- generate docs
- assess policy compatibility
- produce agent-facing briefs

## Event Emission

Capabilities and flows emit events through the framework context.

Example usage:

- `ctx.emit("refund.approved", payload)`

The framework is responsible for:

- validating payload shape
- attaching metadata
- writing the event into the outbox
- linking the event to the current request, capability, or flow execution
- recording audit metadata

Application code should not publish directly to message brokers.

## Outbox Pattern

Plumbus uses the outbox pattern as the default event durability mechanism.

Execution model:

1. capability or flow step completes business logic
2. event is written into an outbox table in the same database transaction as the state change
3. a dispatcher process reads pending outbox rows
4. the dispatcher publishes delivery jobs to the event delivery queue
5. consumers process the event asynchronously

Benefits:

- no lost events when transactions succeed
- simpler recovery behavior
- stronger auditability
- clear operational model

## Event Storage

The database should persist at least:

- outbox rows awaiting dispatch
- event metadata for delivery tracking
- optionally retained event history, depending on configuration

A Plumbus implementation may distinguish between:

- transient delivery state
- durable audit/event history

These should be related but not necessarily the same storage table.

## Delivery Model

The event system uses durable queue-backed delivery.

Delivery guarantees:

- at-least-once delivery
- retry with backoff
- dead-letter handling after retry exhaustion

This means consumers must be idempotent.

### Why not exactly-once?
Exactly-once delivery is complex and often misleading in distributed systems. Plumbus should prefer durable at-least-once delivery with explicit idempotency handling.

## Event Consumers

Consumers may include:

- flow triggers
- event capabilities
- integration adapters
- background processors

A consumer should declare:

- event types handled
- supported versions
- retry behavior
- idempotency expectations
- audit requirements

## Flow Triggers

Flows may start in response to events.

A flow trigger should define:

- triggering event type
- optional version constraints
- input mapping from event payload to flow input
- tenant and actor propagation behavior

This keeps event-driven orchestration explicit and inspectable.

## Idempotency

Because events are delivered at least once, consumers must tolerate duplicate delivery.

Plumbus should support idempotency through:

- consumer-level idempotency keys
- delivery tracking
- flow trigger deduplication where needed
- clear best-practice guidance for side-effecting consumers

The framework should surface duplicate-risk warnings where consumers appear non-idempotent.

## Retry and Dead-Letter Handling

Delivery failures should use queue-backed retry behavior.

Retry policy may include:

- max attempts
- backoff strategy
- retryable error classes

After retry exhaustion:

- the delivery job is moved to a dead-letter queue or equivalent failure state
- the failed delivery is surfaced in operational tooling
- governance and audit layers can reference the failure

The system should distinguish between:

- event delivery failure
- downstream flow or capability failure after successful delivery

## Multi-Tenant Behavior

In multi-tenant systems, events must preserve tenant scope.

Events should carry enough metadata to ensure:

- tenant-aware delivery
- correct downstream authorization context
- safe flow triggering
- audit traceability

Cross-tenant event consumption should be explicit, visible, and unusual.

## Security and Data Classification

Event payloads may contain classified data.

The framework should support:

- event payload classification
- warnings for sensitive data in events
- masking or restricted storage for some payload elements
- policy reporting against event definitions and emitted behavior

Plumbus should not silently normalize risky event payloads. It should surface the risk and require acknowledgement if the design chooses to proceed.

## Audit Integration

Event emission and delivery should both be auditable.

Audit records should capture at least:

- event emitted
- emitter capability or flow
- actor
- tenant
- payload classification summary
- outbox dispatch result
- delivery attempts
- terminal delivery status

This allows operators to answer:

- who emitted the event
- when it was emitted
- whether it was delivered
- what it triggered

## Explainability

Events contribute to explanation trails.

For example, a flow explanation may include:

- triggered by `refund.approved`
- caused by capability `approveRefund`
- correlated with request `req_123`

This makes asynchronous behavior understandable during debugging.

## Event Versioning

Event schemas will evolve, so versioning must be explicit.

Recommended versioning approach:

- each event definition has a version
- consumers declare supported versions
- breaking changes require a new version
- non-breaking compatible additions may stay within the same version by policy

The framework should support policy around deprecation and compatibility reporting.

## External Integrations

External systems may subscribe to or publish events through adapters.

In v0.1, external broker integration should be adapter-based and secondary to the internal event model.

The internal source of truth remains:

- domain event definitions
- outbox persistence
- delivery queue

Examples of later adapters:

- Kafka
- SQS
- Pub/Sub
- webhooks

Capabilities should not directly publish to external brokers.

## Operational Model

The event subsystem involves:

- event definitions
- outbox storage
- dispatcher process
- delivery queue
- consumers
- dead-letter handling
- monitoring and audit views

Operational tooling should expose:

- outbox backlog
- delivery latency
- retry counts
- dead-letter events
- consumer health

## Testing Model

The event system should be testable at several levels:

- event schema validation tests
- capability emission tests
- consumer idempotency tests
- flow trigger integration tests
- delivery retry and dead-letter tests

Event behavior should be testable without requiring external brokers in v0.1.

## AI-Friendly Design Considerations

To support coding agents safely:

- event definitions should be explicit and discoverable
- event names should be stable and literal
- consumer registration should be visible
- generated manifests should describe producers and consumers
- event payloads should be schema-defined

This reduces ambiguity and makes asynchronous behavior easier to modify safely.

## Best Practices

Recommended practices:

- emit events for domain facts, not commands
- keep payloads focused and purposeful
- treat consumer idempotency as mandatory
- preserve tenant and actor context
- avoid putting unnecessary sensitive data in payloads
- prefer flows for complex orchestration instead of chains of loosely understood event consumers

## Summary

The Plumbus event system provides durable, explicit, and auditable asynchronous communication.

It uses:

- schema-defined domain events
- outbox-backed persistence
- queue-backed delivery
- at-least-once semantics
- explicit consumers and flow triggers

This model keeps asynchronous behavior reliable, observable, and compatible with the rest of the Plumbus architecture.
