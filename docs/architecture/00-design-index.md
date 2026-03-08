# Plumbus Architecture Design Index

## Purpose

This document provides an index of the architecture design documents for the Plumbus framework.

Plumbus is a large system composed of multiple subsystems. Each subsystem has its own dedicated design document. This index provides:

- a map of the architecture documentation
- a description of each document's purpose
- the recommended reading order
- subsystem relationships

## Recommended Reading Order

1. Design Principles
2. System Architecture
3. Capability System Design
4. Flow Engine Design
5. Event System Design
6. Entity and Data Model
7. Security Model
8. Authentication Model
9. AI Runtime
10. RAG System
11. Advisory Governance
12. Policy Compatibility Assessment
13. Runtime Architecture
14. CLI Design
15. Testing Model
16. UI Architecture
17. AI Agent Integration

## Document Index

### 01-design-principles.md
Defines the philosophical foundations of Plumbus: API-first design, capability-based architecture, flow orchestration, advisory-first governance, auditability, explainability, privacy-aware development, compliance support, managed AI integration, and AI-friendly codebase structure.

### 02-system-architecture.md
Provides the high-level system map: UI, API, capabilities, flows, events, AI runtime, data layer, and infrastructure. Describes the major execution lifecycles and subsystem boundaries.

### 03-capability-system-design.md
Defines the core building block of the framework: capabilities. Covers contracts, implementation structure, execution context, access control, effect declarations, AI usage, auditing, and testing expectations.

### 04-flow-engine-design.md
Defines the orchestration model for Plumbus flows. Covers flow definitions, durable state, queue-backed execution, retries, waits, scheduling, event triggers, and fault handling.

### 05-event-system-design.md
Defines how domain events are modeled, emitted, stored, delivered, and consumed. Covers event schemas, versioning, the outbox pattern, delivery guarantees, idempotency, and flow triggering.

### 06-entity-data-model.md
Defines entities, field classification, schema generation, migrations, repository services, and safe data access patterns.

### 07-security-model.md
Defines authorization and access control: capability-level enforcement, tenant isolation, deny-by-default behavior, and field-level restrictions.

### 08-authentication-model.md
Defines how principals are authenticated and introduced into execution context. Covers providers, adapters, sessions, and identity propagation.

### 09-ai-runtime.md
Defines the managed AI service model, provider abstraction, prompt templates, output validation, usage tracking, and policy enforcement.

### 10-rag-system.md
Defines document ingestion, chunking, embedding, indexing, retrieval filtering, provenance, and secure context assembly.

### 11-advisory-governance.md
Defines the warning, acknowledgement, and override model that underpins advisory-first governance.

### 12-policy-compatibility-assessment.md
Defines policy profiles, policy evaluation, compatibility reporting, acknowledged risk summaries, and readiness reporting.

### 13-runtime-architecture.md
Defines the server, worker, scheduler, queue, and deployment runtime structure.

### 14-cli-design.md
Defines the Plumbus CLI surface for scaffolding, generation, verification, policy reporting, and agent-facing tooling.

### 15-testing-model.md
Defines the testing stack and strategy: unit tests, capability tests, flow simulation, API integration tests, and browser E2E tests.

### 16-ui-architecture.md
Defines the recommended frontend architecture, generated clients, UI responsibilities, and the rule that business logic stays in capabilities.

### 17-ai-agent-integration.md
Defines repository conventions, generated briefs, edit zones, and how coding agents should safely interact with a Plumbus codebase.

## Notes

These documents form the internal architecture specification for Plumbus. They should stabilize before user-facing guides are written.

User-facing guides should be derived from these design docs, not written independently.
