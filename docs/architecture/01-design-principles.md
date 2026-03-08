# Plumbus Design Principles

## Purpose

This document defines the core design principles of the Plumbus framework. These principles guide architectural decisions across the system and act as the reference point when trade-offs arise.

Plumbus is not just a set of libraries. It is a structured development environment designed for human and AI-assisted software development. The principles below explain why the framework behaves the way it does.

## 1. API-First Architecture

All backend functionality in Plumbus is exposed through structured APIs.

The backend is a fully functional service that exposes capabilities through machine-readable contracts rather than generating HTML responses.

Benefits:

- consistent integration boundaries
- easier testing and automation
- predictable frontend interaction
- easier documentation generation
- simpler system decomposition

Frontend systems consume the backend through these APIs.

## 2. Capability-Based System Design

The smallest unit of application behavior in Plumbus is a capability.

A capability represents a single business action or query.

Examples:

- createCustomer
- approveRefund
- generateInvoice
- listTransactions

Each capability defines:

- input schema
- output schema
- security rules
- side effects
- audit requirements
- testing expectations

Capabilities are intentionally small and focused so they are easy to test, reason about, compose, audit, and modify safely by humans and AI agents.

## 3. Flow-Oriented Orchestration

Complex processes are implemented using flows.

Flows orchestrate capabilities into multi-step workflows that may include:

- conditional branching
- retries
- scheduled execution
- event triggers
- wait/resume operations
- long-running processes

Flows separate process logic from atomic business actions, making system behavior easier to understand and debug.

Each flow execution generates durable state, audit records, and explanation metadata.

## 4. Advisory-First Governance

Plumbus does not prohibit risky design choices by default.

Instead, it follows an advisory-first governance model:

1. detect potential risks
2. warn developers
3. require explicit acknowledgement for deviations
4. record the justification

This ensures that developers remain in control while risks stay visible, deliberate, and documented.

Guardrails, not gates.

## 5. Security by Contract

Security is defined at the capability level.

Capabilities explicitly declare:

- who can invoke them
- what resources they access
- what roles or scopes are required
- what fields may be modified

The system follows a deny-by-default model. If a capability does not declare access to a resource, it cannot access it.

## 6. Built-in Auditability

All significant system actions generate structured audit events.

Audit records capture:

- actor identity
- capability execution
- flow execution
- authorization decisions
- data access
- side effects

Auditability enables debugging, incident investigation, compliance reporting, and behavioral analysis.

## 7. Explainable System Behavior

In addition to audit logs, Plumbus produces explanation trails.

Explanation trails describe why decisions were made, not just what happened.

Examples:

- why a workflow branch was taken
- why a refund was approved
- why an AI system returned a specific result

Explainability is critical for complex systems that combine automation, policy, and AI.

## 8. Privacy-Aware Data Modeling

Plumbus encourages responsible handling of sensitive data.

Data fields may be classified as:

- public
- internal
- personal
- sensitive
- highly sensitive

The framework highlights potential privacy risks, such as storing sensitive information unnecessarily, logging personal data, or exposing private fields through APIs.

Developers may override warnings but must justify the decision.

## 9. Compliance-Aware Development

Plumbus supports policy profiles representing regulatory or internal governance standards.

Examples:

- GDPR
- PCI DSS
- SOC2
- internal security policies

The framework can evaluate an application against these policies and generate compatibility reports describing alignment and acknowledged deviations.

## 10. AI as a Managed System Component

AI integrations are not treated as ad hoc API calls.

Instead, Plumbus provides a managed AI runtime responsible for:

- model access
- prompt management
- output validation
- cost tracking
- security restrictions
- audit logging

This ensures AI usage remains observable, predictable, and safe.

## 11. AI-Friendly Codebase Structure

Plumbus is designed to work well with coding agents.

To support this, the framework uses structural conventions that make repositories easier for AI systems to modify safely:

- feature-local directory structure
- explicit contracts
- isolated generated code
- limited edit scope
- predictable file roles

These rules allow AI agents to operate safely without requiring full repository context.

## 12. Explicit System Boundaries

Side effects must occur through explicit system services such as:

- data access
- AI interaction
- event emission
- flow orchestration
- audit recording

Capabilities should not perform hidden side effects through arbitrary imports.

Explicit boundaries improve predictability, auditing, testability, and debugging.

## Summary

Plumbus is designed around principles that prioritize:

- clarity
- safety
- observability
- composability
- AI-assisted development

These principles form the foundation for every subsystem in the framework.
