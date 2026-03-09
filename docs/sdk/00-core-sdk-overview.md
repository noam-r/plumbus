# Plumbus Core SDK Overview

## Purpose

This document defines the **Plumbus Core SDK**, the programming interface developers and AI coding agents use to build applications on the Plumbus framework.

While previous documents describe the architecture of the framework, the SDK defines **how developers actually write Plumbus code**.

The SDK provides the primitives used to define:

- capabilities
- flows
- entities
- events
- prompts
- policies

These primitives create a structured environment where:

- application behavior is explicit
- contracts are machine-readable
- architecture is enforceable
- AI coding agents can reason about the system safely

## Core Philosophy

The Plumbus SDK is designed around three ideas:

### 1. Contracts First

Every system component declares a contract describing:

- inputs
- outputs
- security requirements
- side effects

Contracts make system behavior explicit and analyzable.

### 2. Declarative Architecture

Developers describe what the system should do, and the framework handles:

- routing
- validation
- authorization
- auditing
- observability

This reduces accidental complexity.

### 3. AI-Operable Code

The SDK is intentionally structured so that LLMs can understand and modify code safely.

This is achieved through:

- explicit primitives
- predictable file roles
- strongly typed contracts
- small isolated components

## Core SDK Primitives

The Plumbus SDK exposes a small set of primitives used to define application behavior.

### Capability

Defines an atomic business operation.

### Flow

Defines a multi-step workflow.

### Entity

Defines persistent domain data.

### Event

Defines domain events emitted by capabilities or flows.

### Prompt

Defines an AI prompt template.

## Runtime Context

Capability handlers receive a runtime context (`ctx`).

The context provides controlled access to system services, including:

- `ctx.auth`
- `ctx.data`
- `ctx.events`
- `ctx.flows`
- `ctx.ai`
- `ctx.audit`

## Capability Lifecycle

When a capability executes:

1. request authenticated
2. authorization policy evaluated
3. input validated
4. capability handler executed
5. side effects performed
6. events emitted
7. audit logs written
8. output validated
9. response returned

The framework manages this lifecycle automatically.

## SDK Design Goals

The SDK is designed to provide:

- predictable code structure
- clear system boundaries
- safe AI-assisted development
- strong type safety
- governance visibility

Developers focus on business logic, while the framework manages operational concerns.

## Summary

The Plumbus Core SDK defines the programming model used to build applications within the framework.

By exposing a small set of structured primitives and a controlled runtime context, the SDK ensures that applications remain:

- secure
- auditable
- testable
- AI-friendly

The SDK is the foundation upon which all Plumbus applications are built.
