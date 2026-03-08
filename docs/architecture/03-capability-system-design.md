# Plumbus Capability System Design

## Purpose

This document describes the capability system, the fundamental unit of business logic in Plumbus.

Capabilities represent atomic business actions or queries that define how an application behaves. All domain logic in a Plumbus application is implemented through capabilities.

The capability system provides:

- predictable system behavior
- strong security boundaries
- explicit side-effect management
- auditability
- explainability
- safe modification by humans and AI agents

## Core Concept

A capability is a small, well-defined unit of business functionality.

Examples:

- createCustomer
- approveRefund
- sendInvoice
- listTransactions
- calculateTax

Each capability represents a single domain action or query.

Capabilities must be:

- deterministic in structure
- small in scope
- fully described through contracts
- independently testable

Capabilities are the only place where business logic should live.

## Capability Responsibilities

Capabilities define:

1. input schema
2. output schema
3. authorization requirements
4. side effects
5. audit behavior
6. AI usage, if applicable
7. error conditions
8. testing expectations

By defining these explicitly, the framework can:

- generate APIs
- enforce policies
- track side effects
- analyze compliance
- support safe automated code modification

## Capability Structure

Each capability lives in a dedicated directory.

Example:

`app/capabilities/billing/approve-refund/`

Containing:

- `capability.ts`
- `impl.ts`
- `tests/approve-refund.spec.ts`
- `fixtures/approve-refund.fixture.ts`

This structure ensures the edit scope of a feature is localized.

## Capability Contract

The capability contract defines the interface and rules of the capability.

A capability contract should define:

- name
- domain
- capability kind
- input schema
- output schema
- access rules
- effect declarations
- audit settings
- AI settings, if needed
- verification requirements
- documentation metadata

The contract is declarative and should not contain business logic.

## Implementation File

The implementation file contains the executable logic.

The implementation receives:

- a framework execution context
- validated input

and must return output matching the declared schema.

Implementation code should use explicit framework services rather than arbitrary side-effect libraries.

## Execution Context

Capabilities interact with the system through a controlled execution context.

Expected context services include:

- `ctx.data`
- `ctx.emit`
- `ctx.ai`
- `ctx.flow`
- `ctx.audit`
- `ctx.auth`
- `ctx.errors`

These services ensure side effects remain visible and auditable.

## Data Access

Capabilities access persistent data through the data service.

Examples:

- `ctx.data.customer.findById(id)`
- `ctx.data.invoice.update(id, data)`

Direct database access from capability code is discouraged.

The data service is responsible for:

- tenant isolation
- audit hooks
- policy evaluation
- consistent validation

## Event Emission

Capabilities may emit domain events.

Example:

`ctx.emit("invoice.generated", { invoiceId })`

Events allow the system to:

- trigger flows
- notify integrations
- drive asynchronous processes

Events follow a structured schema and versioning model.

## AI Integration

Capabilities may interact with AI through the AI runtime.

Example operations include:

- generation
- extraction
- classification
- retrieval

The AI runtime handles:

- provider access
- prompt templates
- cost tracking
- audit logging
- output validation

Capabilities should not call AI provider SDKs directly for standard use.

## Authorization Model

Capabilities declare access requirements in their contract.

Execution model:

1. the API layer authenticates the request
2. the capability system evaluates access policy
3. if access is denied, execution stops before implementation logic runs

Authorization checks occur before implementation.

## Effects Declaration

Capabilities must declare meaningful side effects.

Effects may include:

- data access
- event emission
- external integrations
- AI usage

Effects declarations help the framework:

- generate audit reports
- detect undeclared side effects
- analyze policy compatibility
- provide safe edit boundaries for AI agents

Direct external SDK usage may be allowed, but meaningful external I/O should still be declared.

## Testing Requirements

Every capability must include automated tests.

Testing should cover:

- correct behavior
- error conditions
- access control
- audit generation
- effect behavior
- AI output validation where applicable

Capabilities should be testable without requiring a running browser or API server.

## Error Handling

Capabilities should throw structured framework errors.

Standardized errors improve:

- API responses
- logging
- debugging
- client behavior

## Audit Integration

Capability execution automatically generates audit records.

Audit events should capture:

- actor identity
- capability name
- execution timing
- side effects
- outcome status
- relevant metadata

Sensitive fields may be masked according to data classification rules.

## Capability Lifecycle

Typical execution lifecycle:

1. request received
2. input validated
3. authorization evaluated
4. implementation executed
5. data, events, AI, or flow services invoked as needed
6. audit and explanation records stored
7. response returned

Failures generate structured error responses and audit entries.

## AI-Friendly Design Constraints

The capability system is designed to support safe AI-assisted development.

Key constraints:

- one capability per directory
- explicit contract file
- isolated implementation file
- predictable naming
- small edit scope
- generated code separated from user code

These rules allow coding agents to modify implementations without breaking unrelated parts of the system.

## Best Practices

Capabilities should follow these rules:

- implement one business action
- avoid large implementations
- avoid hidden side effects
- rely on framework services
- include comprehensive tests
- keep logic deterministic where possible

If a capability becomes complex, it should likely be split into smaller capabilities.

## Summary

The capability system is the foundation of Plumbus.

Capabilities provide:

- clear boundaries for business logic
- explicit contracts for behavior
- predictable side effects
- safe integration with AI and automation

By enforcing these conventions, Plumbus enables systems that are easier to understand, maintain, audit, and extend.
