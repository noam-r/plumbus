# Plumbus --- Executive Summary

## What is Plumbus?

**Plumbus** is an **AI‑first application framework** designed to make
building modern web applications **faster, safer, and more reliable when
using AI coding assistants**.

Traditional frameworks assume humans are the primary developers. Plumbus
assumes that **both humans and AI agents will write and maintain code**,
and therefore provides a development environment that is:

-   structured
-   contract‑driven
-   auditable
-   secure by design
-   governance‑aware

Plumbus enforces architectural patterns that reduce mistakes, improve
maintainability, and allow AI tools to operate safely within a
controlled development environment.

------------------------------------------------------------------------

## The Core Idea

Modern LLM coding tools work best when:

-   the architecture is explicit
-   interfaces are strict
-   responsibilities are clearly separated
-   system behavior is observable

Plumbus introduces a **strict but flexible application architecture**
where:

-   business logic is implemented as **capabilities**
-   workflows are implemented as **flows**
-   data models are defined as **entities**
-   events represent domain facts
-   AI interactions are governed through a managed runtime

By enforcing these patterns, Plumbus dramatically reduces ambiguity in
application code.

------------------------------------------------------------------------

## Why Plumbus Exists

Modern application development faces several challenges:

### AI-generated code can introduce hidden risks

LLMs can accidentally break architecture, introduce security
vulnerabilities, or bypass system constraints.

### Security and compliance are often afterthoughts

Developers frequently retrofit security, privacy, and compliance
features late in the process.

### Complex workflows are difficult to manage

Long‑running processes involving queues, retries, and event
orchestration are difficult to implement correctly.

### AI integrations lack governance

AI usage often bypasses observability, auditing, and cost tracking.

Plumbus addresses these issues by **making architecture, governance, and
security first‑class concerns from the beginning**.

------------------------------------------------------------------------

## Key Principles

Plumbus is built around several core principles.

### 1. AI‑First Architecture

The framework is designed so AI coding agents can safely modify the
codebase.

This is achieved through:

-   strict contracts
-   predictable repository structure
-   explicit runtime APIs
-   generated documentation for AI agents

------------------------------------------------------------------------

### 2. API‑First Design

Plumbus applications expose a **structured backend API** that can be
consumed by any frontend.

The UI is separate and interacts with the system through generated
clients.

------------------------------------------------------------------------

### 3. Capability‑Driven Business Logic

All application actions are implemented as **capabilities**.

A capability is a single, well-defined operation with:

-   validated inputs
-   validated outputs
-   explicit access rules
-   declared side effects

This makes application behavior easy to understand and audit.

------------------------------------------------------------------------

### 4. Flow‑Based Orchestration

Complex workflows are implemented using **flows**.

Flows orchestrate capabilities into reliable processes that support:

-   retries
-   scheduling
-   asynchronous execution
-   event-driven behavior

------------------------------------------------------------------------

### 5. Advisory‑First Governance

Plumbus introduces a governance model that **guides developers rather
than blocking them**.

If developers make risky architectural decisions (for example storing
sensitive data improperly), the framework:

-   warns them
-   records the decision
-   allows explicit acknowledgment of the risk

This enables organizations to generate **compliance reports
automatically**.

------------------------------------------------------------------------

### 6. Built‑in Auditability and Explainability

Every major system action produces structured audit signals.

This allows teams to answer questions like:

-   Why did this process run?
-   Who triggered this action?
-   Why did the AI generate this result?
-   What data was accessed?

This dramatically improves debugging and operational transparency.

------------------------------------------------------------------------

### 7. Privacy‑Opinionated Data Modeling

Data models support classification metadata such as:

-   personal
-   sensitive
-   highly sensitive

This allows the framework to guide developers toward privacy‑safe
patterns and generate compliance signals for regulations such as:

-   GDPR
-   PCI DSS
-   SOC2

------------------------------------------------------------------------

## Built‑In AI Runtime

Plumbus treats AI as **managed infrastructure**, not just API calls.

The framework provides:

-   prompt templates
-   structured outputs
-   cost tracking
-   security filtering
-   RAG integration
-   AI usage auditing

This makes AI behavior observable and controllable in production
systems.

------------------------------------------------------------------------

## What Developers Build in Plumbus

Developers primarily write:

### Capabilities

Atomic application actions.

Examples:

-   createCustomer
-   approveRefund
-   generateInvoice

------------------------------------------------------------------------

### Flows

Multi‑step workflows.

Examples:

-   customer onboarding
-   refund approval
-   document processing

------------------------------------------------------------------------

### Entities

Persistent domain data models.

Examples:

-   Customer
-   Invoice
-   Refund

------------------------------------------------------------------------

### Events

Domain facts emitted by the system.

Examples:

-   refund.approved
-   invoice.generated

------------------------------------------------------------------------

### Prompts

Reusable AI interaction templates.

Examples:

-   summarize-ticket
-   classify-message

------------------------------------------------------------------------

## What Plumbus Handles Automatically

The framework manages:

-   authentication
-   authorization
-   input validation
-   database access
-   workflow orchestration
-   background job processing
-   event delivery
-   audit logging
-   AI usage tracking
-   governance checks

This allows developers to focus on **business logic rather than
infrastructure**.

------------------------------------------------------------------------

## Benefits

Using Plumbus provides several advantages:

### Faster development

Strict architecture reduces design decisions and speeds up
implementation.

### Safer AI usage

AI coding assistants operate within clearly defined boundaries.

### Built‑in compliance visibility

Security and privacy considerations are tracked automatically.

### Better debugging

Structured audit logs allow engineers to understand exactly what
happened in the system.

### Scalable architecture

Flows, events, and queues enable reliable large‑scale systems.

------------------------------------------------------------------------

## Who Plumbus Is For

Plumbus is designed for:

-   startups building AI‑enabled products
-   teams adopting AI coding assistants
-   organizations requiring strong compliance visibility
-   developers building complex workflow systems

It is especially useful for systems that require:

-   strong security guarantees
-   auditability
-   AI integrations
-   workflow orchestration

------------------------------------------------------------------------

## Summary

Plumbus is an **AI‑first application framework** that combines:

-   strict architectural contracts
-   capability‑driven development
-   workflow orchestration
-   built‑in governance
-   managed AI infrastructure

The result is a framework that enables teams to build modern
applications that are **secure, auditable, AI‑compatible, and easier to
maintain**.
