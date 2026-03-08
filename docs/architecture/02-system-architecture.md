# Plumbus System Architecture

## Purpose

This document describes the high-level architecture of the Plumbus framework.

It explains how the major components of the system interact and how applications built with Plumbus are structured.

The architecture is designed to support:

- API-first application development
- capability-based business logic
- workflow orchestration
- AI integration
- compliance and governance tooling
- AI-assisted development workflows

## Architectural Overview

A Plumbus application consists of several layered subsystems:

- UI layer
- API layer
- capability layer
- flow engine
- event system
- AI runtime
- data layer
- infrastructure layer

Each layer has a clearly defined responsibility.

## 1. UI Layer

The UI layer contains frontend applications that interact with the backend.

Recommended default stack:

- React
- Next.js
- TypeScript

The UI layer is responsible for:

- rendering interfaces
- collecting user input
- displaying system state
- invoking backend capabilities through generated API clients

Business logic should not live in the UI layer. All domain actions must occur through backend capabilities or flows.

## 2. API Layer

The API layer exposes capabilities and flows through HTTP endpoints.

The API layer is generated automatically from capability contracts and flow trigger definitions.

Responsibilities:

- request routing
- schema validation
- authentication context injection
- response formatting
- error normalization

Recommended runtime:

- Node.js
- Fastify server

The API layer acts primarily as a transport adapter between clients and the capability system.

## 3. Capability Layer

Capabilities implement atomic units of business logic.

Each capability consists of:

- `capability.ts`
- `impl.ts`
- tests
- fixtures

The capability contract defines:

- input schema
- output schema
- authorization rules
- side effects
- audit requirements

The implementation file contains execution logic.

Capabilities interact with the rest of the system through a controlled execution context:

- `ctx.data`
- `ctx.emit`
- `ctx.ai`
- `ctx.flow`
- `ctx.audit`
- `ctx.auth`

This prevents hidden side effects and improves observability.

## 4. Flow Engine

Flows orchestrate capabilities into multi-step workflows.

Examples:

- onboarding workflows
- refund approval processes
- billing pipelines
- AI-powered automation

The flow engine consists of:

- flow definitions
- flow execution state
- step execution workers
- retry scheduler

Architecture model:

Flow state is stored durably in PostgreSQL, while runnable steps are executed through a queue-backed worker model.

This provides:

- retries
- delayed execution
- failure handling
- distributed processing
- resumable workflows

## 5. Event System

The event system allows capabilities and flows to emit domain events.

Events are used to trigger:

- flows
- integrations
- asynchronous processing
- downstream capability execution

The event system follows an outbox pattern to ensure reliable delivery.

## 6. Data Layer

The data layer manages application entities and persistent storage.

Recommended stack:

- PostgreSQL
- Drizzle ORM

Entities define:

- fields
- validation rules
- data classification
- retention hints

Capabilities access data through a controlled service layer, not arbitrary database calls.

This ensures:

- consistent audit behavior
- tenant isolation
- policy enforcement
- predictable query behavior

## 7. AI Runtime

The AI runtime manages all interactions with AI models.

Capabilities access AI functionality through the context API:

- `ctx.ai.generate()`
- `ctx.ai.extract()`
- `ctx.ai.classify()`
- `ctx.ai.retrieve()`

The AI runtime handles:

- provider selection
- prompt templates
- token usage tracking
- cost accounting
- output validation
- audit logging

## 8. RAG System

The RAG system enables retrieval-augmented generation.

Pipeline stages include:

- document ingestion
- classification
- chunking
- embedding
- indexing
- retrieval filtering
- context assembly

Recommended infrastructure:

- PostgreSQL
- pgvector

Retrieval queries respect authorization rules, tenant boundaries, and data classification policies.

## 9. Governance System

Plumbus includes governance mechanisms that analyze system design.

The governance system evaluates:

- security posture
- data privacy risks
- AI usage policies
- external integrations
- compliance alignment

Developers can generate compatibility reports with commands such as:

`plumbus certify policy <policy>`

These reports describe:

- aligned rules
- policy gaps
- acknowledged risks

The governance system does not prohibit risky choices by default. It surfaces them.

## 10. Runtime Infrastructure

The runtime environment includes:

- API server
- background workers
- flow execution workers
- event processors
- AI runtime services
- scheduler processes

Workers process asynchronous tasks such as:

- flow steps
- event handlers
- scheduled jobs

Infrastructure services include:

- database
- queue system
- vector storage
- logging

## Request Lifecycle

Typical request flow:

1. client request arrives
2. API layer validates input and builds auth context
3. capability executes
4. capability may access data, emit events, or call AI
5. audit and explanation records are produced
6. response is returned

## Flow Execution Lifecycle

Typical flow lifecycle:

1. flow trigger occurs
2. flow execution record is created
3. first step is enqueued
4. worker executes step
5. state is updated
6. next step is scheduled, or the flow completes, waits, or fails

Retries and delays are managed through the execution queue.

## AI Interaction Lifecycle

Typical AI interaction flow:

1. capability invokes the AI runtime
2. retrieval occurs if needed
3. prompt is assembled
4. model executes
5. output is validated
6. audit record is produced
7. capability continues or returns output

## System Boundaries

The architecture enforces clear boundaries between:

- UI logic
- business logic
- infrastructure
- AI services
- governance analysis

These boundaries improve system safety and maintainability.

## Summary

The Plumbus architecture is designed to support:

- structured business logic
- safe AI integration
- auditability
- compliance readiness
- AI-assisted development workflows

Each subsystem has clearly defined responsibilities and interacts with others through explicit contracts.
