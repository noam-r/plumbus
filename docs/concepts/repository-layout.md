# Plumbus Repository Layout Specification

## Purpose

This document defines the **standard repository layout for Plumbus
applications**.

A consistent repository structure is critical because Plumbus is
designed to support:

-   human developers
-   AI coding agents
-   automated governance tools
-   code generation
-   architectural analysis

The repository layout must therefore be:

-   predictable
-   machine-readable
-   easy to navigate
-   safe for automated modification

This specification defines the canonical structure for a Plumbus
project.

------------------------------------------------------------------------

# Design Principles

## 1. Feature Locality

Application logic should be grouped by **domain feature**, not technical
layer.

This ensures that when developers or AI agents work on a feature, they
only need to read a small set of files.

Example:

    billing/
      approve-refund/
      generate-invoice/

------------------------------------------------------------------------

## 2. Explicit Architecture Layers

The repository must clearly separate:

-   application logic
-   framework-generated artifacts
-   configuration
-   infrastructure

This prevents accidental modification of generated files.

------------------------------------------------------------------------

## 3. AI Operability

The structure must be understandable by AI agents.

Rules:

-   predictable directories
-   explicit file naming
-   minimal hidden conventions
-   stable locations for contracts and implementations

------------------------------------------------------------------------

# Top-Level Repository Layout

A standard Plumbus project should look like:

    app/
      capabilities/
      flows/
      entities/
      events/
      prompts/
      adapters/

    ui/

    tests/

    .plumbus/

    config/

    scripts/

Each section has a specific purpose.

------------------------------------------------------------------------

# app/

The `app/` directory contains **all application logic**.

This includes:

-   capabilities
-   flows
-   entities
-   events
-   prompts
-   external adapters

Application logic should never be placed outside this directory.

------------------------------------------------------------------------

# app/capabilities/

Contains all capability definitions.

Structure:

    app/capabilities/<domain>/<capability-name>/

Example:

    app/capabilities/billing/approve-refund/
      capability.ts
      impl.ts
      tests/
      fixtures/

Rules:

-   one capability per directory
-   capability contract defined in `capability.ts`
-   implementation in `impl.ts`
-   tests colocated with the capability

------------------------------------------------------------------------

# app/flows/

Contains workflow definitions.

Structure:

    app/flows/<domain>/<flow-name>/

Example:

    app/flows/billing/refund-approval/
      flow.ts
      steps/
      tests/

Flows orchestrate capabilities into larger business processes.

------------------------------------------------------------------------

# app/entities/

Contains entity definitions used for database schema generation.

Example:

    app/entities/customer.entity.ts
    app/entities/invoice.entity.ts
    app/entities/refund.entity.ts

Rules:

-   one entity per file
-   entities define structure only (no business logic)

------------------------------------------------------------------------

# app/events/

Contains event contracts.

Example:

    app/events/refund-approved.event.ts
    app/events/customer-created.event.ts

Events represent domain facts emitted by capabilities or flows.

------------------------------------------------------------------------

# app/prompts/

Contains AI prompt templates.

Example:

    app/prompts/summarize-ticket.prompt.ts
    app/prompts/classify-ticket.prompt.ts

Prompt contracts define input and output schemas for AI operations.

------------------------------------------------------------------------

# app/adapters/

Contains integrations with external systems.

Examples:

-   payment providers
-   messaging systems
-   external APIs

Example:

    app/adapters/stripe/
    app/adapters/sendgrid/

Adapters isolate infrastructure concerns from core application logic.

------------------------------------------------------------------------

# ui/

Contains the frontend application.

Typical structure:

    ui/
      pages/
      components/
      hooks/
      generated/

The UI layer consumes backend capabilities via generated clients.

Business logic should remain in backend capabilities.

------------------------------------------------------------------------

# tests/

Contains global integration tests and end-to-end tests.

Example:

    tests/
      integration/
      e2e/

Capability and flow tests should remain colocated with their respective
components.

------------------------------------------------------------------------

# .plumbus/

Contains framework-generated artifacts.

Example:

    .plumbus/
      manifests/
      generated/
      briefs/

These files should **never be edited manually**.

### manifests/

Machine-readable metadata about the system:

-   capability manifests
-   flow manifests
-   entity manifests

Used by governance tools and documentation generation.

------------------------------------------------------------------------

### generated/

Contains generated code such as:

-   API clients
-   schema metadata
-   runtime artifacts

These files are regenerated by the CLI.

------------------------------------------------------------------------

### briefs/

Contains AI-friendly documentation.

Examples:

    .plumbus/briefs/project.md
    .plumbus/briefs/capabilities/

Briefs help AI agents understand project structure quickly.

------------------------------------------------------------------------

# config/

Contains configuration files.

Examples:

    config/app.config.ts
    config/ai.config.ts
    config/security.config.ts

Configuration should remain separate from application logic.

------------------------------------------------------------------------

# scripts/

Contains developer utilities and operational scripts.

Examples:

    scripts/migrate.ts
    scripts/seed.ts
    scripts/reindex.ts

Scripts should not contain core application logic.

------------------------------------------------------------------------

# Generated Files Policy

Files inside `.plumbus/generated/` must never be modified manually.

Instead, they are updated through CLI commands such as:

    plumbus generate

This ensures consistency between contracts and generated artifacts.

------------------------------------------------------------------------

# Naming Conventions

Consistent naming improves developer productivity and AI comprehension.

Recommended rules:

-   kebab-case for directories
-   descriptive capability names
-   event names in past tense
-   prompt names reflecting their task

Examples:

    approve-refund
    refund-approved
    summarize-ticket

------------------------------------------------------------------------

# Dependency Direction

Dependencies should flow in the following direction:

    entities
       ↓
    capabilities
       ↓
    flows
       ↓
    ui

Flows may depend on capabilities, but capabilities should not depend on
flows.

------------------------------------------------------------------------

# AI Editing Zones

The repository may define editing zones for AI agents.

Safe zones:

-   capability implementations
-   tests
-   flows
-   prompts

Restricted zones:

-   entity definitions
-   security configuration
-   generated files

This reduces risk when AI tools modify the codebase.

------------------------------------------------------------------------

# Example Repository

Example simplified repository:

    app/
      capabilities/
        billing/
          approve-refund/
      flows/
        billing/
          refund-approval/
      entities/
        refund.entity.ts
      events/
        refund-approved.event.ts
      prompts/
        summarize-ticket.prompt.ts

    ui/

    tests/

    .plumbus/
      manifests/
      generated/
      briefs/

    config/
    scripts/

------------------------------------------------------------------------

# Summary

The Plumbus repository layout ensures that projects remain:

-   structured
-   analyzable
-   AI-operable
-   easy to navigate

By separating application logic, generated artifacts, configuration, and
infrastructure, the layout provides a stable foundation for building
Plumbus applications.
